# Platform Constraints and Data Availability Reference

## Purpose

This file defines what Nexthink can and cannot observe via NQL,
the exact retention window for every relevant data table, Collector
version requirements for specific fields, and the approved workaround
for every data gap. The agent MUST consult this file before declaring
a metric available, before stating a retention limit, and before
recommending a remote action as a substitute for missing data.

Never infer retention periods, field availability, or Collector
requirements from memory. Use this file as the authoritative source.

---

## 1. Data Retention Reference

### 1.1 Event tables (30-day retention)

These tables retain data for 30 days from the event timestamp.
Queries using `during past 30d` will return the full available window.
Queries requesting data older than 30 days will return no results —
state this constraint explicitly rather than returning an empty result
without explanation.

| Table | Namespace | Key fields |
|---|---|---|
| `events` | `device_performance` | CPU, memory, disk, GPU metrics |
| `boots` | `device_performance` | Boot type, duration |
| `system_crashes` | `device_performance` | Error code, label, timestamp |
| `hard_resets` | `device_performance` | Timestamp, count |
| `events` | `execution` | Freezes, CPU time, memory, connections |
| `crashes` | `execution` | Crash count, crash_on_start, binary path |
| `events` | `connectivity` | Wi-Fi signal, adapter type, band |
| `events` | `connection` | TCP failures, connection ratios |
| `logins` | `session` | Logon durations, script duration |
| `sessions` | `collaboration` | Call quality, audio/video metrics |
| `scores` | `dex` | All DEX score fields and subscores |
| `application_scores` | `dex` | Per-application score nodes |
| `executions` | `remote_action` | Execution status, outputs, trigger |
| `executions` | `workflow` | Execution outcome, status |
| `installations` | `package` | Install timestamp per device |

### 1.2 Inventory tables (30-day retention from last activity)

These tables retain records for 30 days from the last observed
activity on the object. A device not seen for 30 days will be
removed from inventory.

| Table | Namespace | Notes |
|---|---|---|
| `devices` | `device` | Removed 30 days after `device.days_since_last_seen` |
| `users` | `user` | Removed 30 days after last activity |
| `binaries` | `binary` | Removed 30 days after `binary.last_seen` |
| `packages` | `package` | Removed 30 days after last activity |
| `installed_packages` | `package` | Current installed state; reflects last known inventory |
| `applications` | `application` | Removed 30 days after last activity |

### 1.3 Trend tables (90 days to 13 months retention)

| Table | Namespace | Retention | Notes |
|---|---|---|---|
| `executions_summary` | `remote_action` | 13 months | Aggregated RA execution counts |
| `executions_summary` | `workflow` | 13 months | Aggregated workflow execution counts |
| Software metering tables | `software_metering` | 13 months | Usage trends per application |
| Custom trend tables | `custom_trend` | 90 days | Organization-defined metrics |

### 1.4 Special retention rules

| Data type | Retention | Notes |
|---|---|---|
| `campaign.responses` | Indefinite | Campaign responses are never purged |
| DEX trend dashboards (UI) | 13 months | **Not queryable via NQL.** Visible in the DEX module UI only. If a user requests DEX trend data older than 30 days, direct them to the DEX module UI — do not attempt an NQL query. |
| `agent.conversations` (NQL metadata) | ~13 months | State, outcome, type, intent only |
| `agent.conversations` (raw messages) | ~30 days | Viewable via dedicated UI only, not via NQL |

### 1.5 Retention constraint — mandatory agent behavior

When a user requests data for a period that exceeds the retention
window of the relevant table:
1. State the retention limit explicitly (e.g., "execution.crashes
   retains 30 days of data").
2. Offer the maximum available window as an alternative.
3. For DEX trend data older than 30 days, direct the user to the
   DEX module UI which retains up to 13 months of trend data.
4. Never return an empty result without explaining why.

---

## 2. Collector Version Requirements

Some NQL fields are only populated when the device runs a minimum
Collector version. Always check the device's Collector version before
relying on these fields. If the version requirement is not met, the
field will return null — state this explicitly rather than treating
null as "no data."

| Field | Table | Minimum Collector | Platform | Notes |
|---|---|---|---|---|
| `event.cpu.duration_with_high_usage` | `device_performance.events` | 26.5 | Windows | CPU high-pressure duration; null on older Collectors |
| `event.cpu.frequency_ratio` | `device_performance.events` | 26.5 | Windows | CPU throttling indicator; null on older Collectors |
| `event.cpu.dpc_usage` | `device_performance.events` | 26.5 | Windows | Deferred Procedure Call usage; null on older Collectors |
| `event.duration_with_medium_memory_pressure` | `device_performance.events` | 25.8 | macOS | macOS memory pressure duration |
| `event.highest_process_visibility` | `execution.events` | 25.3 | Windows, macOS | Foreground / background / system classification |
| Freeze process hierarchy detail | `execution.events` | 26.6 | Windows | Per-freeze process chain; older Collectors return 15-min aggregate only |
| `crash.binary_path_root` | `execution.crashes` | 26.5 | Windows, macOS | Binary launch location root; null on older Collectors |
| ICA/HDX bandwidth per virtual channel | `session.vdi_events` | 26.5 | Windows | VDI channel-level bandwidth metrics |

**Agent rule**: when a field listed above returns null for a device,
check the device's Collector version before concluding "no data."
State: *"This field requires Collector 26.x or higher. The device
is running Collector [version] — this metric is not available."*

---

## 3. Network Observability Gaps and Workarounds

Nexthink does not provide direct NQL fields for several commonly
requested network metrics. The table below defines what is requested,
what is actually available, the approved NQL substitute, and the
recommended remote action when no NQL substitute exists.

Never claim a metric is available when it is not. Always declare the
gap and offer the substitute or remote action.

### 3.1 Network gap matrix

| Requested metric | NQL availability | NQL substitute | Remote action workaround |
|---|---|---|---|
| **VPN latency / performance** | ❌ Not a dedicated field | `connection.events`: `event.number_of_no_service_connections` filtered by VPN gateway destination domains. High count = VPN service unreachable or degraded. | "Test Connection from Device" (Windows) — tests TCP reachability to a specified destination |
| **DNS latency** | ❌ Not measured directly | `connection.events`: `event.number_of_no_host_connections`. This field counts connections that failed because the device could not reach the destination host — the primary proxy for DNS resolution failures or routing failures. | "Get Interface Connection Status" — returns DNS domain and adapter state |
| **Proxy failures** | ❌ Not a dedicated field | `connection.events`: `event.number_of_no_service_connections` filtered by known proxy destination domains. Host reachable but service not responding = proxy block or misconfiguration. | "Test Connection from Device" targeting proxy FQDN |
| **General device-level packet loss** | ❌ Not available | No NQL substitute for general packet loss. | Remote action required to run a ping/traceroute test |
| **Packet loss — collaboration calls** | ✅ Available for Teams and Zoom only | `collaboration.sessions`: `session.audio.inbound_packet_loss`, `session.audio.outbound_packet_loss` | Not required — data available in NQL |
| **Authentication delays** | ❌ Not directly observable | Correlate `session.logins`: `login.logon_script_duration` (proxy for authentication/GPO processing time) with `connection.events`: `event.number_of_no_service_connections` in the same time window. Elevated script duration + elevated no_service_connections = probable auth/proxy initialization delay. | No dedicated remote action; use correlation approach |
| **Wi-Fi signal quality** | ✅ Available | `connectivity.events`: `event.wifi.signal_strength` (dBm), `event.wifi.band`, `event.wifi.ssid`, `event.wifi.transmission_rate` | Not required — data available in NQL |
| **Adapter type and transitions** | ✅ Available | `connectivity.events`: `event.primary_physical_adapter.type` (WiFi / Ethernet / Bluetooth). Frequent transitions within a short window = instability signal. | Not required — data available in NQL |
| **TCP connection failure ratio** | ✅ Available | `connection.events`: `event.failed_connection_ratio` | Not required — data available in NQL |
| **Per-application connection failures** | ✅ Available | `execution.events`: `event.number_of_no_host_connections`, `event.number_of_no_service_connections`, `event.number_of_rejected_connections` grouped by `binary.product_name` | Not required — data available in NQL |
| **Microsoft 365 / cloud service connectivity** | ✅ Partial | `connection.events` filtered by `event.destination.domain` matching `*.microsoft.com`, `*.office.com`, `*.teams.microsoft.com`. Failure fields apply. | Not required for failure detection; "Test Connection from Device" for active reachability test |

### 3.2 Inference rules for unavailable metrics

When a direct metric is unavailable, apply these inference rules.
Always label the result as **Inferred** — never as **Observed**.

| Inference | Evidence required | Confidence ceiling |
|---|---|---|
| VPN degradation | `no_service_connections` > 10% of attempted connections to VPN gateway domain, sustained over ≥ 2 consecutive 15-min buckets | Medium |
| DNS failure | `no_host_connections` spike (> 5% of attempted connections) with no corresponding Wi-Fi signal degradation | Medium |
| Proxy block | `no_service_connections` to proxy domain with `no_host_connections` near zero (host reachable, service not) | Medium |
| Auth delay contribution to logon | `login.logon_script_duration` > 30s AND `no_service_connections` elevated in the same 15-min window as the logon event | Low–Medium |

---

## 4. Add-in and Extension Observability Gaps

Nexthink does not have a dedicated NQL table for Office COM add-ins,
browser extension names/IDs, or plugin enable/disable state.

### 4.1 What IS observable

| Observable | NQL source | Fields |
|---|---|---|
| Installed packages (including add-in installers) | `package.installed_packages` | `package.name`, `package.publisher`, `package.type`, `package.version`, `package.parent_name` |
| Binary executions by product category | `execution.events` | `binary.product_category`, `binary.product_subcategory`, `binary.company`, `binary.description` |
| Third-party binaries running within host apps | `execution.crashes` | `binary.company` ≠ host app publisher = third-party component indicator |
| Crash-on-start events | `execution.crashes` | `crash.crash_on_start == true` = initialization failure (typical of add-in load conflicts) |
| Collaboration driver versions | `collaboration.sessions` | `session.participant_device.vendor_wifi_driver_version`, `camera_driver`, `speaker_driver` |

### 4.2 What is NOT observable in NQL

| Data point | Availability | Required workaround |
|---|---|---|
| Office COM add-in names and registry entries | ❌ | Remote action: "Get Office Add-ins" (Endpoint Performance Toolkit) |
| Browser extension names and IDs | ❌ | Remote action: "Get Browser Extensions" |
| Add-in enabled / disabled state | ❌ | Remote action: "Get Office Add-ins" |
| GPO-managed extension policies | ❌ | Remote action or manual review |
| Add-in version history | ❌ | `package.installations` for the add-in installer package only |

### 4.3 Mandatory disclosure

Every add-in and extension analysis section MUST include the
following statement:

*"Direct add-in inventory (Office COM add-ins, browser extension
names and IDs) is not available in the Nexthink NQL data model.
The analysis above is based on package inventory, binary execution
metadata, and crash correlation. A remote action is required to
collect granular add-in data from the device."*

---

## 5. Collector Data Transmission Frequency

Understanding how frequently the Collector sends data is essential
for interpreting time-bucket granularity and avoiding false
conclusions about gaps in data.

| Data type | Transmission frequency | Implication for analysis |
|---|---|---|
| CPU, memory, executions, connections | Every 60 seconds | High-resolution; 1-min buckets available |
| Object status (app running/crashed, connection state) | Every 5 minutes | Crash/freeze detection has up to 5-min latency |
| OS info, user info, running apps, application experience | Every 15 minutes | App experience data has up to 15-min latency |
| Installed packages, security info, hardware info | Every 60 minutes | Package inventory reflects state at last hourly sync |
| GPO information, monitors, user groups | Every 360 minutes | GPO data may lag up to 6 hours |

**Agent rule**: when a user reports an event at a specific time and
the data shows no activity in that exact minute, consider the
transmission frequency before concluding "no data." A crash detected
at 14:32 may appear in the 14:30–14:35 bucket.

---

## 6. Platform-Level Observability Boundaries

These are hard boundaries that cannot be worked around via NQL or
remote actions within the standard Nexthink platform.

| Boundary | Explanation |
|---|---|
| **Custom fields** | Nexthink Assist does not currently support querying custom fields. Do not reference custom fields in any generated NQL. |
| **ITSM incident data** | ServiceNow incidents are not natively queryable in the main NQL namespaces. They are accessible only through inbound connector tables when the ITSM integration is configured. |
| **Raw conversation messages** | `agent.conversations` raw messages are viewable only via a dedicated UI, not via NQL. |
| **Sentiment score in NQL** | The DEX Sentiment score is derived from campaign responses. It is not a field in `dex.scores` and cannot be queried alongside Technology subscores in a single NQL statement. |
| **DEX trend data > 30 days** | The DEX module UI retains up to 13 months of trend data. This data is not accessible via NQL. |
| **macOS CPU frequency ratio** | CPU frequency ratio (throttling indicator) is a Windows-only field. No equivalent exists for macOS in the current data model. |
| **Linux device support** | Linux devices are monitored by the Nexthink Collector but have a reduced data model compared to Windows and macOS. Many performance fields return null on Linux. |

---

## 7. Quick-Reference: "Can I query this?" Decision Table

Use this table to answer availability questions before formulating
a query. If the answer is "Partial" or "No", apply the workaround
defined in the relevant section above.

| Question | Answer | Section |
|---|---|---|
| Can I query DEX scores for the last 30 days? | ✅ Yes | §1.1 |
| Can I query DEX trend data for the last 6 months? | ❌ No — UI only | §1.4 |
| Can I query application crashes for the last 30 days? | ✅ Yes | §1.1 |
| Can I query package installations for the last 30 days? | ✅ Yes | §1.1 |
| Can I query VPN latency? | ❌ No — infer from no_service_connections | §3.1 |
| Can I query DNS latency? | ❌ No — infer from no_host_connections | §3.1 |
| Can I query Wi-Fi signal strength? | ✅ Yes | §3.1 |
| Can I query TCP failure ratio? | ✅ Yes | §3.1 |
| Can I query Office add-in names? | ❌ No — remote action required | §4.2 |
| Can I query browser extension names? | ❌ No — remote action required | §4.2 |
| Can I query CPU frequency ratio (throttling)? | ✅ Windows only, Collector 26.5+ | §2 |
| Can I query freeze process hierarchy? | ✅ Windows only, Collector 26.6+ | §2 |
| Can I query the Sentiment score in NQL? | ❌ No — campaign.responses only | §6 |
| Can I query custom fields? | ❌ No — not supported by Assist | §6 |