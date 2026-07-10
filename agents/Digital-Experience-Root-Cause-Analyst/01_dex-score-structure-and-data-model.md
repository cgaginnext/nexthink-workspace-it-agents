# dex-score-structure-and-data-model.md
# DEX Score Structure and Data Model Reference

## Purpose

This file is the authoritative reference for the DEX score structure,
its NQL field mappings, computation rules, and query constraints.
The agent MUST consult this file before formulating any DEX-related
query or interpreting any DEX score result. Never infer field names,
table names, or computation rules from memory — use this file.

---

## 1. DEX Score Architecture

The DEX score is a composite score combining two top-level components:

DEX Score (score.value) ├── Technology Score (score.technology.value) │ ├── Endpoint Score (score.endpoint.value) │ │ ├── Boot Speed (score.endpoint.boot_speed_value) │ │ ├── Logon Speed (score.endpoint.logon_speed_value) │ │ ├── Device Reliability (score.endpoint.device_reliability_value) │ │ ├── Software Performance (score.endpoint.software_performance_value) │ │ │ ├── With GUI (score.endpoint.software_performance_with_gui_value) │ │ │ └── Without GUI (score.endpoint.software_performance_without_gui_value) │ │ ├── Device Performance (score.endpoint.device_performance_value) │ │ └── VDI Performance (score.endpoint.vdi_performance_value) [VDI only] │ │ └── Virtual Session Lag (score.endpoint.virtual_session_lag_value) │ ├── Applications Score (score.applications.value) │ │ └── [Sourced from dex.application_scores — see Section 4] │ └── Collaboration Score (score.collaboration.value) │ ├── Teams Audio Quality (score.collaboration.teams_audio_quality_value) │ ├── Zoom Audio Quality (score.collaboration.zoom_audio_quality_value) │ └── Zoom Video Quality (score.collaboration.zoom_video_quality_value) └── Sentiment Score └── [Sourced from DEX campaign responses — not in dex.scores NQL table]


---

## 2. Score Value Scale

The only valid DEX score scale is:

| Range   | Label       | Color  |
|---------|-------------|--------|
| 71–100  | Good        | Green  |
| 31–70   | Average     | Yellow |
| 0–30    | Frustrating | Red    |

This scale applies to ALL nodes: the overall DEX score, Technology score,
all subscores, and all leaf nodes. Any other scale is incorrect.

---

## 3. NQL Field Reference — dex.scores Table

### Table identity
- **Namespace**: `dex`
- **Table**: `scores`
- **Associations**: `device.devices`, `user.users`
- **Computation**: once per day at 00:00 UTC
- **Window**: rolling 7-day window per data point
- **Granularity**: one record per user + device combination per day

### Score value fields

| NQL Field | Description |
|---|---|
| `score.value` | Overall DEX score (Technology + Sentiment combined) |
| `score.technology.value` | Technology score (hard metrics only) |
| `score.endpoint.value` | Endpoint subscore |
| `score.endpoint.boot_speed_value` | Boot speed — based on boot event durations |
| `score.endpoint.logon_speed_value` | Logon speed — based on logon event durations |
| `score.endpoint.device_reliability_value` | Device reliability — based on system crashes and hard resets |
| `score.endpoint.software_performance_value` | Software performance — based on all freeze events |
| `score.endpoint.software_performance_with_gui_value` | Software performance — freezes of binaries WITH a user interface |
| `score.endpoint.software_performance_without_gui_value` | Software performance — freezes of binaries WITHOUT a user interface |
| `score.endpoint.device_performance_value` | Device performance — based on CPU, GPU, memory, and free disk space |
| `score.endpoint.vdi_performance_value` | VDI performance — disk I/O, CPU, memory, volume usage [VDI only] |
| `score.endpoint.virtual_session_lag_value` | Virtual session lag — network latency for VDI sessions [VDI only] |
| `score.applications.value` | Applications score — performance and reliability of applications |
| `score.collaboration.value` | Collaboration score — Teams and Zoom combined |
| `score.collaboration.teams_audio_quality_value` | Teams audio quality — ratio of poor audio calls |
| `score.collaboration.zoom_audio_quality_value` | Zoom audio quality — ratio of poor audio calls |
| `score.collaboration.zoom_video_quality_value` | Zoom video quality — ratio of poor video calls |

All score value fields support aggregations: `avg`, `min`, `max`,
`sum`, `sumif`.

### Score impact fields

Each node exposes a `_score_impact` field representing the **estimated
decrease in the Technology score** caused by issues in that node.
Use these fields to rank which subscores contributed most to DEX
degradation.

| NQL Field | Node it measures |
|---|---|
| `score.endpoint.boot_speed_score_impact` | Boot speed degradation impact |
| `score.endpoint.logon_speed_score_impact` | Logon speed degradation impact |
| `score.endpoint.software_performance_score_impact` | Software freeze impact (all) |
| `score.endpoint.software_performance_with_gui_score_impact` | Software freeze impact (GUI binaries) |
| `score.endpoint.software_performance_without_gui_score_impact` | Software freeze impact (background binaries) |
| `score.endpoint.vdi_performance_score_impact` | VDI performance degradation impact |
| `score.collaboration.zoom_audio_quality_score_impact` | Zoom audio quality degradation impact |
| `application_score.node.score_impact` | Per-application node impact [dex.application_scores only] |

All `_score_impact` fields support aggregations: `avg`, `min`, `max`,
`sum`, `sumif`.

**Interpretation rule**: a higher `_score_impact` value means that node
is pulling the Technology score down more. Rank nodes by
`_score_impact.avg()` descending to identify the primary driver of
DEX degradation.

---

## 4. NQL Field Reference — dex.application_scores Table

### Table identity
- **Namespace**: `dex`
- **Table**: `application_scores`
- **Associations**: `device.devices`, `user.users`, `application.applications`
- **Computation**: same daily cycle as `dex.scores`

### Key fields

| NQL Field | Description |
|---|---|
| `application_score.node.type` | Node type within the application score structure |
| `application_score.node.value` | Score value for the specified node |
| `application_score.node.score_impact` | Estimated Technology score decrease for the specified node |
| `application_score.time` | Timestamp of the score event |

### Node type enumeration

The `application_score.node.type` field accepts the following values:

| Value | What it measures |
|---|---|
| `Application` | Overall application score |
| `Page_loads` | Web page load performance |
| `Web_reliability` | Web application reliability |
| `Transactions` | Web transaction performance |
| `Crashes` | Application crash contribution |
| `Freezes` | Application freeze contribution |

**Usage pattern**: always filter by `application_score.node.type` to
target a specific dimension. For example, to find applications with
the highest crash impact, filter on `node.type == Crashes` and sort
by `node.score_impact.avg()` descending.

---

## 5. Computation Rules

### Rolling 7-day window
Each daily DEX score data point is computed using data from the
**7 days preceding** the computation date. The score computed at
00:00 UTC on day D incorporates data from day D-7 to day D.

### Computation timing
- Computation starts at 00:00 UTC.
- Results are tagged with 04:00 UTC once computation completes.
- The score is associated with **today's date**, not yesterday's.

### Hourly sampling
The DEX computation samples **hourly moments of experience** for each
user. Each hour is evaluated against configured thresholds. The final
score reflects the proportion of hours rated Good, Average, or
Frustrating — it is not a simple average of raw metric values.

### User + device granularity
A score record is created for each **user + device combination** active
in the 7-day window. A user who worked on two devices will have two
score records per day. Always filter by both `device.name` and
`user.name` when analyzing a specific device-user pair.

### Null score rule
If a user has not been active on a device for 7 consecutive days,
their score for that device will be null. No historical score is
retained beyond the 7-day inactivity threshold.

---

## 6. Query Rules and Constraints

### Rule 1 — Correct time syntax for 30-day trend analysis
To observe DEX score evolution over 30 days, query:
dex.scores during past 30d

This returns approximately 30 daily data points, each representing a
different rolling 7-day window. This is the correct approach for
trend analysis.

### Rule 2 — Never use `during past 7d` for a trend
-- WRONG: returns 7 overlapping windows, not a single snapshot dex.scores during past 7d

-- CORRECT: returns today's single score (latest rolling window) dex.scores during past 24h


### Rule 3 — Single-day snapshot syntax
To retrieve the score for a specific date:
dex.scores on 2026-07-07

To retrieve today's current score:
dex.scores during past 24h


### Rule 4 — Aligning raw data tables with a specific score date
To compare raw metric data with the DEX score computed on date D,
the raw data query window must match the 7-day computation window:
from [D - 7 days at 00:00 UTC] to [D at 00:00 UTC]

Adjust for browser timezone offset. For example, for a CET browser
(UTC+1) and score date 2026-07-07:
device_performance.events from 2026-06-30 01:00:00 to 2026-07-07 01:00:00


### Rule 5 — Applications score requires a separate table
`score.applications.value` in `dex.scores` gives the aggregate
Applications score. For per-application breakdown (which app is
driving the score down), query `dex.application_scores` separately
and filter by `application_score.node.type`.

### Rule 6 — Sentiment score is not in dex.scores NQL
The Sentiment score is derived from DEX campaign responses
(`campaign.responses` tables). It is not a field in `dex.scores`
and cannot be queried alongside Technology subscores in the same
NQL statement.

### Rule 7 — DEX trend dashboards are not NQL-queryable
The DEX module UI dashboards retain trend data for up to 13 months.
This data is **not accessible via NQL**. If a user asks for DEX
trend data older than 30 days, explain this constraint and direct
them to the DEX module UI for historical visualization.

---

## 7. Raw Data Tables Mapped to DEX Score Nodes

Use these mappings to retrieve the underlying metric data that feeds
each DEX score node. Always apply the 7-day alignment rule (Rule 4)
when comparing raw data to a specific score date.

| DEX Score Node | Raw Data Table | Key Fields |
|---|---|---|
| Boot speed | `device_performance.boots` | `boot.duration`, `boot.type` |
| Logon speed | `session.logins` | `login.time_until_desktop_is_visible`, `login.time_until_desktop_is_ready`, `login.logon_script_duration` |
| Device reliability | `device_performance.system_crashes`, `device_performance.hard_resets` | `system_crash.number_of_system_crashes`, `number_of_hard_resets` |
| Software performance (freezes) | `execution.events` | `event.number_of_freezes` grouped by `binary.product_name` |
| Device performance | `device_performance.events` | `normalized_cpu_usage`, `free_memory` / `installed_memory`, `system_drive_free_space` |
| Application crashes | `execution.crashes` | `crash.number_of_crashes` grouped by `binary.product_name` |
| Application page loads | `web.page_views` | `page_view.page_load_time.overall`, `page_view.experience_level` |
| Web transactions | `web.transactions` | `number_of_transactions`, transaction duration |
| Teams audio quality | `collaboration.sessions` | `session.audio.quality`, `session.call.quality`, `session.audio.inbound_packet_loss`, `session.audio.inbound_jitter`, `session.audio.inbound_rtt` |
| Zoom audio quality | `collaboration.sessions` | `session.audio.quality` filtered by `session.application.type == Zoom` |
| Zoom video quality | `collaboration.sessions` | `session.video.quality` filtered by `session.application.type == Zoom` |
| VDI session lag | `session.vdi_events` | VDI-specific latency metrics |
| Network quality | `connectivity.events` | `event.wifi.signal_strength`, `event.wifi.noise_level`, `event.primary_physical_adapter.type` |

---

## 8. Significant Degradation Thresholds

Use these thresholds consistently across all analysis sections to
define what constitutes a "significant" DEX event.

| Event type | Threshold | Definition |
|---|---|---|
| Score drop | ≥ 10 points | Decline within 3 consecutive daily data points |
| Score recovery | ≥ 10 points | Increase within 3 consecutive daily data points |
| Step-change | ≥ 15 points | Single-day decline between two consecutive data points |
| Sustained degradation | ≥ 7 consecutive days | Score remains in Average or Frustrating range |
| Logon POOR rating | > 30 seconds | `login.time_until_desktop_is_visible` |
| Boot POOR rating | > 150 seconds | `boot.duration` for full boot or fast startup |
| CPU contention | P95 > 80% normalized | `normalized_cpu_usage` aggregated by 1h |
| Wi-Fi signal degradation | < −75 dBm | `event.wifi.signal_strength` sustained |
| TCP failure | > 5% ratio | `event.failed_connection_ratio` |
| Collaboration poor call | Any poor rating | `session.call.quality == poor` |

---

## 9. Common Analysis Patterns

### Pattern 1 — Identify the primary DEX degradation driver
Query `dex.scores during past 30d` for the device. Extract all
`_score_impact` fields. Sort by `avg()` descending. The highest
value identifies the subscore pulling the Technology score down most.

### Pattern 2 — Detect a step-change event
Query `dex.scores during past 30d`. List `score.value` by date.
Identify consecutive pairs where the delta exceeds 15 points.
The date of the larger value is the degradation onset date.

### Pattern 3 — Correlate a subscore drop with raw data
1. Identify the date of the subscore drop from `dex.scores`.
2. Apply the 7-day alignment window (Rule 4).
3. Query the corresponding raw data table (Section 7 mapping).
4. Aggregate by 1h to match the DEX hourly sampling granularity.
5. Identify the hours where the metric breached the threshold
   (Section 8).

### Pattern 4 — Rank applications by DEX impact
Query `dex.application_scores during past 30d` for the device.
Filter by `application_score.node.type == Crashes` and separately
by `application_score.node.type == Freezes`. Sort each by
`application_score.node.score_impact.avg()` descending.
Merge the two ranked lists to produce a combined instability ranking.

### Pattern 5 — Assess collaboration degradation
Query `dex.scores during past 30d`. Extract
`score.collaboration.teams_audio_quality_value` and
`score.collaboration.zoom_audio_quality_value` by date.
For dates where either drops below 31, query
`collaboration.sessions` for the same 7-day window and extract
`session.audio.inbound_packet_loss`, `session.audio.inbound_jitter`,
and `session.call.quality` to identify the call-level evidence.