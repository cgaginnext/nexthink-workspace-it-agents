# Application Stability and Performance Analysis Reference

## Purpose

This file defines the complete methodology for analyzing application
stability (crashes, freezes) and application performance (page loads,
web transactions, collaboration quality) as part of a DEX RCA.
It covers the NQL data sources, field-level reference, thresholds,
analysis patterns, and the rules for distinguishing device-level
resource contention from application-specific failure.

The agent MUST use this file for every analysis section that touches
application crashes, freezes, web performance, or collaboration
quality. Never infer field names, threshold values, or classification
rules from memory.

---

## 1. Application Stability — Crashes

### 1.1 Data source

| Table | Namespace | Associations |
|---|---|---|
| `crashes` | `execution` | `device.devices`, `user.users`, `binary.binaries` |

### 1.2 Field reference

| Field | Type | Description |
|---|---|---|
| `crash.number_of_crashes` | INTEGER | Number of crashes of the same binary within one minute |
| `crash.time` | DATE_TIME | Timestamp of the crash event |
| `crash.crash_on_start` | BOOL | True = binary crashed immediately after launch (initialization failure) |
| `crash.binary_path` | STRING | Full file path of the crashing binary |
| `crash.binary_path_root` | ENUMERATION | Well-known root segment of the launch path (requires Collector 26.5+) |
| `binary.name` | STRING | Filename of the binary |
| `binary.product_name` | STRING | Application name associated with the binary |
| `binary.company` | STRING | Publisher of the binary |
| `binary.has_user_interface` | BOOL | True = binary has an interactive window (user-facing) |
| `binary.version` | VERSION | Binary file version |
| `binary.product_category` | STRING | Broad product classification |

### 1.3 Crash analysis patterns

**Pattern A — Crash volume trend**
Query `execution.crashes during past 30d` grouped by
`binary.product_name` and date. Identify binaries where crash count
increased after the degradation onset date. A binary with zero crashes
before onset and non-zero crashes after onset is a strong regression
signal.

**Pattern B — Crash-on-start cluster**
Filter `crash.crash_on_start == true`. A cluster of crash-on-start
events for the same binary indicates an initialization failure —
typically caused by a missing dependency, a conflicting DLL, or an
add-in load failure. This pattern is the primary NQL proxy for
add-in conflicts when direct add-in inventory is unavailable.

**Pattern C — Third-party binary crashing within a host process**
When `binary.company` differs from the host application publisher
(e.g., a crash in a binary published by "Acme Corp" while the host
application is Microsoft Office), this indicates a third-party
component (add-in, plugin, injected DLL) is the crash source, not
the host application itself.

**Pattern D — Version-correlated crash cluster**
Join `execution.crashes` with `package.installations` on
`binary.product_name` ≈ `package.name` and `crash.time` >
`installation.time`. A crash cluster that begins within 24h of a
package installation and involves the same product is a strong
causality candidate.

### 1.4 Crash severity classification

| Classification | Criteria |
|---|---|
| **Critical** | `crash.crash_on_start == true` for a user-facing binary (`binary.has_user_interface == true`); application is unusable |
| **High** | ≥ 5 crashes per day for the same binary; user-facing binary |
| **Medium** | 2–4 crashes per day for the same binary; OR any crash count for a background binary |
| **Low** | 1 crash per day; isolated; no recurrence pattern |

---

## 2. Application Stability — Freezes

### 2.1 Data source

| Table | Namespace | Associations |
|---|---|---|
| `events` | `execution` | `device.devices`, `user.users`, `binary.binaries` |

### 2.2 Field reference

| Field | Type | Description |
|---|---|---|
| `event.number_of_freezes` | INTEGER | Number of execution freezes in the time bucket |
| `event.cpu_time` | DURATION | Total CPU time consumed by the binary in the bucket |
| `event.cpu.duration_with_high_usage` | DURATION | Duration where the binary's normalized CPU usage exceeded the high threshold (30-second samples) |
| `event.memory` | BYTES | Average memory usage of the binary in the bucket |
| `event.execution_duration` | DURATION | Duration the process was active in the bucket |
| `event.highest_process_visibility` | ENUMERATION | Foreground / Background / System (requires Collector 25.3+) |
| `binary.has_user_interface` | BOOL | True = user-facing binary; freezes are directly user-impacting |
| `binary.product_name` | STRING | Application name |

### 2.3 Freeze vs. resource contention disambiguation

A freeze can be caused by the application itself (bug, deadlock,
memory leak) or by device-level resource contention (CPU starvation,
memory pressure, disk I/O wait). The agent MUST distinguish between
these two causes before assigning a root cause.

**Disambiguation rule**:

| Observation | Interpretation |
|---|---|
| Freeze cluster for ONE binary; `device_performance.events` CPU and memory are within normal range | Application-specific issue — bug, deadlock, or add-in conflict |
| Freeze cluster for MULTIPLE binaries simultaneously; `device_performance.events` shows CPU P95 > 80% or memory swap rate elevated | Device resource contention — the device cannot serve all running processes; not application-specific |
| Freeze cluster for ONE binary; `device_performance.events` shows CPU P95 > 80% | Ambiguous — the binary may be causing the CPU spike, or the CPU spike may be causing the freeze. Investigate `event.cpu.duration_with_high_usage` for the specific binary to determine if it is the CPU consumer |
| Freeze cluster correlates with `event.number_of_no_host_connections` or `event.number_of_no_service_connections` spike | Network-induced freeze — the application is waiting for a network response that never arrives; not a device resource issue |

### 2.4 Freeze severity classification

| Classification | Criteria |
|---|---|
| **Critical** | ≥ 10 freezes per day for a user-facing binary (`binary.has_user_interface == true`) |
| **High** | 5–9 freezes per day for a user-facing binary |
| **Medium** | 2–4 freezes per day for a user-facing binary; OR any freeze count for a background binary with `event.cpu.duration_with_high_usage` > 30 min/day |
| **Low** | 1 freeze per day; isolated; no recurrence |

### 2.5 DEX score mapping for freezes

| DEX field | What it measures |
|---|---|
| `score.endpoint.software_performance_value` | Combined freeze score (all binaries) |
| `score.endpoint.software_performance_with_gui_value` | Freeze score for user-facing binaries only |
| `score.endpoint.software_performance_without_gui_value` | Freeze score for background binaries only |
| `score.endpoint.software_performance_score_impact` | Estimated Technology score decrease from all freezes |
| `score.endpoint.software_performance_with_gui_score_impact` | Estimated Technology score decrease from user-facing freezes |

When `software_performance_with_gui_score_impact` is significantly
higher than `software_performance_without_gui_score_impact`, the
degradation is user-visible and should be prioritized.

---

## 3. Web Application Performance

### 3.1 Data sources

| Table | Namespace | What it captures |
|---|---|---|
| `page_views` | `web` | Individual page load events with timing breakdown |
| `page_views_summary` | `web` | Aggregated page load trends (longer retention) |
| `transactions` | `web` | In-app transaction events (SPA flows) |

### 3.2 Page view field reference

| Field | Type | Description |
|---|---|---|
| `page_view.page_load_time.overall` | DURATION | Total page load time |
| `page_view.page_load_time.backend` | DURATION | Time spent on server-side processing |
| `page_view.page_load_time.network` | DURATION | Time for the request to travel over the network |
| `page_view.page_load_time.client` | DURATION | Time spent on client-side rendering |
| `page_view.experience_level` | ENUMERATION | `good` / `average` / `frustrating` — evaluated against configured thresholds |
| `page_view.is_soft_navigation` | BOOL | True = SPA in-page navigation (no full reload) |
| `page_view.url` | STRING | URL of the navigation |
| `page_view.number_of_page_views` | INTEGER | Count of page views in the bucket |
| `page_view.number_of_resource_errors` | INTEGER | Resources that failed to load during the navigation |
| `page_view.number_of_resources` | INTEGER | Total resources loaded |
| `page_view.largest_resource_load_time` | DURATION | Load time of the largest resource |
| `page_view.adapter_type` | ENUMERATION | Network adapter type at time of navigation: `WiFi` / `Ethernet` / `Bluetooth` |
| `page_view.start_time` | DATE_TIME | Timestamp of the navigation |

### 3.3 Web performance thresholds

These are the default Nexthink experience level thresholds for page
loads. An organization may override them per application.

| Experience level | Page load time |
|---|---|
| Good | < 3 seconds |
| Average | 3–10 seconds |
| Frustrating | > 10 seconds |

For web transactions (SPA in-app flows):

| Experience level | Transaction duration |
|---|---|
| Good | < 4 seconds |
| Average | 4–16 seconds |
| Frustrating | ≥ 16 seconds |

### 3.4 Web performance analysis patterns

**Pattern A — Backend vs. network vs. client decomposition**
When `page_view.page_load_time.overall` is elevated, decompose into
its three components:
- `backend` dominant → server-side issue; not device or network
- `network` dominant → connectivity issue; correlate with
  `connectivity.events` signal strength and `connection.events`
  failure ratios
- `client` dominant → device resource issue; correlate with
  `device_performance.events` CPU and memory

**Pattern B — Adapter type correlation**
Group `page_view.experience_level` by `page_view.adapter_type`.
If `frustrating` ratio is significantly higher on `WiFi` than on
`Ethernet`, the degradation is network-path specific, not
server-side or device-side.

**Pattern C — Resource error spike**
A sudden increase in `page_view.number_of_resource_errors` without
a corresponding increase in `page_view.page_load_time.backend`
indicates a CDN or static asset delivery issue, not a server
processing problem.

**Pattern D — SPA soft navigation performance**
Filter `page_view.is_soft_navigation == true` to isolate SPA
in-page navigation performance. Soft navigations that are
consistently `frustrating` while hard navigations are `good`
indicate a client-side rendering bottleneck, not a network or
backend issue.

### 3.5 DEX score mapping for web performance

| DEX field | What it measures |
|---|---|
| `application_score.node.type == Page_loads` | Page load performance contribution to application score |
| `application_score.node.type == Web_reliability` | Web application reliability contribution |
| `application_score.node.type == Transactions` | Web transaction performance contribution |

---

## 4. Collaboration Quality Analysis

### 4.1 Data source

| Table | Namespace | Associations |
|---|---|---|
| `sessions` | `collaboration` | `device.devices`, `user.users` |

### 4.2 Session-level field reference

**Call identity and timing**

| Field | Type | Description |
|---|---|---|
| `session.call.id` | STRING | Unique call identifier |
| `session.call.start_time` | DATE_TIME | When the first participant joined |
| `session.call.end_time` | DATE_TIME | When the last participant left |
| `session.call.type` | ENUMERATION | `peer_to_peer` / `group_call` (Teams only) |
| `session.call.quality` | ENUMERATION | Overall call quality: `good` / `poor` / `unknown` |
| `session.application.type` | ENUMERATION | `Teams` / `Zoom` / `Skype_for_Business` / `Lync` |
| `session.application.version` | VERSION | Client version used during the session |

**Audio quality metrics**

| Field | Type | Description |
|---|---|---|
| `session.audio.quality` | ENUMERATION | `good` / `poor` / `unknown` |
| `session.audio.inbound_packet_loss` | FLOAT | Ratio of inbound audio packets lost |
| `session.audio.outbound_packet_loss` | FLOAT | Ratio of outbound audio packets lost |
| `session.audio.inbound_jitter` | DURATION | Average variation in inbound packet arrival time |
| `session.audio.outbound_jitter` | DURATION | Average variation in outbound packet arrival time |
| `session.audio.inbound_rtt` | DURATION | Round-trip time for inbound audio |
| `session.audio.outbound_rtt` | DURATION | Round-trip time for outbound audio |
| `session.audio.inbound_latency` | DURATION | One-way latency for inbound audio |
| `session.audio.outbound_latency` | DURATION | One-way latency for outbound audio |
| `session.audio.inbound_rocs` | FLOAT | Ratio of concealment frames to total frames (inbound) |
| `session.audio.outbound_rocs` | FLOAT | Ratio of concealment frames to total frames (outbound) |

**Video quality metrics**

| Field | Type | Description |
|---|---|---|
| `session.video.quality` | ENUMERATION | `good` / `poor` / `unknown` |
| `session.video.inbound_packet_loss` | FLOAT | Ratio of inbound video packets lost |
| `session.video.outbound_packet_loss` | FLOAT | Ratio of outbound video packets lost |
| `session.video.inbound_jitter` | DURATION | Average variation in inbound video packet arrival |
| `session.video.outbound_jitter` | DURATION | Average variation in outbound video packet arrival |
| `session.video.inbound_rtt` | DURATION | Round-trip time for inbound video |
| `session.video.outbound_rtt` | DURATION | Round-trip time for outbound video |

**Device context during session (Teams on Windows only)**

| Field | Type | Description |
|---|---|---|
| `session.participant_device.type` | ENUMERATION | Device type: `Windows` / `macOS` / `iOS` / `Android` / etc. |
| `session.participant_device.speaker` | STRING | Speaker device name |
| `session.participant_device.speaker_driver` | STRING | Speaker driver name and version |
| `session.participant_device.camera` | STRING | Camera device name |
| `session.participant_device.camera_driver` | STRING | Camera driver name and version |
| `session.participant_device.vendor_wifi_driver` | STRING | Wi-Fi driver name |
| `session.participant_device.vendor_wifi_driver_version` | STRING | Wi-Fi driver version |
| `session.participant_device.microphone_driver` | STRING | Microphone driver name and version |

### 4.3 Collaboration quality thresholds

These thresholds define when a metric is considered degraded.
A single metric breaching its threshold is sufficient to classify
`session.audio.quality` or `session.video.quality` as `poor`.

| Metric | Degraded threshold | Severe threshold |
|---|---|---|
| Audio packet loss (inbound or outbound) | > 5% | > 10% |
| Video packet loss (inbound or outbound) | > 5% | > 10% |
| Audio jitter (inbound or outbound) | > 30 ms | > 50 ms |
| Video jitter (inbound or outbound) | > 30 ms | > 50 ms |
| Audio RTT (inbound or outbound) | > 300 ms | > 500 ms |
| Video RTT (inbound or outbound) | > 300 ms | > 500 ms |
| Audio ROCS (concealment ratio) | > 10% | > 20% |

### 4.4 Collaboration degradation root cause disambiguation

When `session.call.quality == poor`, apply this decision tree to
identify the root cause before assigning a classification.

**Step 1 — Is the degradation inbound, outbound, or both?**
- Inbound only (`inbound_packet_loss` elevated, `outbound_packet_loss`
  normal): the network path from the remote participant to this device
  is degraded. The issue is likely on the network infrastructure or
  the remote participant's side, not this device.
- Outbound only (`outbound_packet_loss` elevated, `inbound_packet_loss`
  normal): the network path from this device to the remote participant
  is degraded. Correlate with `connectivity.events` Wi-Fi signal
  strength and `connection.events` failure ratios for this device.
- Both elevated: symmetric degradation — likely a shared network
  path issue (ISP, VPN, corporate network segment).

**Step 2 — Is the degradation device-specific or environment-wide?**
- If poor call quality is observed for this device only, and other
  devices on the same network segment show good quality: device-specific
  issue (driver, hardware, or application version).
- If poor call quality is observed across multiple devices in the same
  location or on the same network: environment-wide issue (network
  infrastructure, ISP, or VPN).

**Step 3 — Is a driver version change correlated?**
Check `session.participant_device.vendor_wifi_driver_version`,
`session.participant_device.speaker_driver`, and
`session.participant_device.camera_driver` for changes that coincide
with the onset of poor call quality. A driver version change
immediately before the degradation onset is a strong Causal candidate.

**Step 4 — Is a client version change correlated?**
Check `session.application.version` for changes that coincide with
the onset. A new client version introducing audio/video regression
is Pattern 5 from the change correlation methodology (file 3).

### 4.5 DEX score mapping for collaboration

| DEX field | What it measures |
|---|---|
| `score.collaboration.value` | Combined collaboration score |
| `score.collaboration.teams_audio_quality_value` | Teams audio quality score |
| `score.collaboration.zoom_audio_quality_value` | Zoom audio quality score |
| `score.collaboration.zoom_video_quality_value` | Zoom video quality score |
| `score.collaboration.zoom_audio_quality_score_impact` | Estimated Technology score decrease from Zoom audio |

---

## 5. Application Score Integration

### 5.1 Using dex.application_scores for application ranking

Query `dex.application_scores during past 30d` for the device.
Use `application_score.node.type` to isolate the dimension of
interest, then sort by `application_score.node.score_impact.avg()`
descending to rank applications by their contribution to DEX
degradation.

| Node type | Raw data table to correlate |
|---|---|
| `Crashes` | `execution.crashes` |
| `Freezes` | `execution.events` (`event.number_of_freezes`) |
| `Page_loads` | `web.page_views` |
| `Web_reliability` | `web.page_views` (`page_view.number_of_resource_errors`) |
| `Transactions` | `web.transactions` |

### 5.2 Application score vs. endpoint score — which to use

| Question | Use |
|---|---|
| Which application is pulling the DEX score down most? | `dex.application_scores` with `node.type` filter |
| Is the device itself the problem (not a specific app)? | `dex.scores` endpoint subscores + `device_performance.events` |
| Is the degradation in web apps or desktop apps? | Compare `score.applications.value` vs. `score.endpoint.software_performance_value` |
| Is collaboration the primary driver? | `score.collaboration.value` and its subscores |

---

## 6. Cross-Section Corroboration Rules

When an application stability or performance finding is used as
corroboration for a causality classification (per file 3, §4.3),
the following rules apply:

| Finding | Corroboration strength | Condition |
|---|---|---|
| Crash cluster for the same binary as the suspected package | Strong | Crash onset aligns with installation timestamp (< 24h) |
| Freeze cluster for a user-facing binary | Strong | `binary.has_user_interface == true` AND freeze onset aligns with installation |
| `application_score.node.score_impact` elevated for the affected application | Moderate | Score impact elevated on the same date as the DEX drop |
| Poor call quality (`session.call.quality == poor`) ratio > 20% | Strong | Onset aligns with collaboration tool update |
| `page_view.experience_level == frustrating` ratio > 30% | Moderate | Onset aligns with browser or web application update |
| `crash.crash_on_start == true` for the affected binary | Strong | Indicates initialization failure — highest specificity for add-in conflicts |

A finding classified as "Strong" corroboration can, combined with
temporal alignment and a plausible mechanism, support a "Causal"
classification. A "Moderate" finding alone is insufficient for
"Causal" — it supports "Contributory" at most.