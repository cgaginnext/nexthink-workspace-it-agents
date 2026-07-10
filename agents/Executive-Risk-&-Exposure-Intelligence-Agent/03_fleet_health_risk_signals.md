# Fleet Health Risk Signals — Executive Risk & Exposure Intelligence Agent

## ⚙️ Customization Manifest

| Ref | Section | Placeholder | Required / Optional | Impact if missing |
|-----|---------|-------------|--------------------|--------------------|
| CFG-H01 | H2 | `CRASH_FREQUENCY_THRESHOLD` (system crashes / 7 days) | Optional | Default: ≥ 3 crashes in 7 days = High |
| CFG-H02 | H3 | `APP_CRASH_THRESHOLD` (application crashes / 7 days) | Optional | Default: ≥ 5 crashes in 7 days = High |
| CFG-H03 | H4 | `BOOT_DURATION_THRESHOLD_MS` | Optional | Default: > 120 000 ms (2 min) = degraded |
| CFG-H04 | H4 | `LOGIN_DURATION_THRESHOLD_MS` | Optional | Default: > 30 000 ms (30 s) = degraded |
| CFG-H05 | H5 | `CPU_HIGH_USAGE_THRESHOLD_MINUTES` | Optional | Default: > 30 min/day sustained above 80% |
| CFG-H06 | H5 | `MEMORY_PRESSURE_THRESHOLD_MINUTES` | Optional | Default: > 20 min/day in critical pressure |
| CFG-H07 | H6 | `DISK_FREE_SPACE_THRESHOLD_GB` | Optional | Default: < 10 GB free on system drive |
| CFG-H08 | H7 | `WIFI_SIGNAL_THRESHOLD_DBM` | Optional | Default: < −75 dBm = poor signal |
| CFG-H09 | H8 | `DEX_SCORE_FLOOR` | Optional | Default: score ≤ 30 = Frustrating (red) |

### Configuration Status

```
CFG-H01 CRASH_FREQUENCY_THRESHOLD:       [ ] Not configured  [ ] Using defaults
CFG-H02 APP_CRASH_THRESHOLD:             [ ] Not configured  [ ] Using defaults
CFG-H03 BOOT_DURATION_THRESHOLD_MS:      [ ] Not configured  [ ] Using defaults
CFG-H04 LOGIN_DURATION_THRESHOLD_MS:     [ ] Not configured  [ ] Using defaults
CFG-H05 CPU_HIGH_USAGE_THRESHOLD_MINUTES:[ ] Not configured  [ ] Using defaults
CFG-H06 MEMORY_PRESSURE_THRESHOLD_MINUTES:[ ] Not configured [ ] Using defaults
CFG-H07 DISK_FREE_SPACE_THRESHOLD_GB:    [ ] Not configured  [ ] Using defaults
CFG-H08 WIFI_SIGNAL_THRESHOLD_DBM:       [ ] Not configured  [ ] Using defaults
CFG-H09 DEX_SCORE_FLOOR:                 [ ] Not configured  [ ] Using defaults
```

---

## Purpose

This document defines the fleet health risk signals used by the agent to
assess Component 2 (Operational Stability) of the Composite Risk Score
defined in `01_risk_scoring_framework.md`.

Fleet health signals differ from security exposure indicators in one
fundamental way: they measure **experienced degradation**, not posture
gaps. A device can have perfect security controls and still be a
productivity liability if it crashes daily, boots in 4 minutes, or
runs out of disk space. Both dimensions matter to the executive audience
— one is a risk to the organization, the other is a cost.

---

## Signal Taxonomy

Fleet health signals are organized into seven domains:

| # | Domain | What it measures |
|---|--------|-----------------|
| H1 | **System Stability** | Hard resets, system crashes (BSOD / kernel panic) |
| H2 | **Application Reliability** | App crashes, freezes, binary-level instability |
| H3 | **Startup & Login Performance** | Boot duration, login time, desktop readiness |
| H4 | **Hardware Resource Pressure** | CPU, memory, swap — sustained pressure events |
| H5 | **Storage Health** | System drive free space, usage trends |
| H6 | **Network Connectivity Quality** | Wi-Fi signal, TCP failure ratio, adapter stability |
| H7 | **DEX Score as Composite Health Proxy** | Endpoint, Applications, Collaboration subscores |

---

## Data Availability Convention

| Tag | Meaning |
|-----|---------|
| ✅ **Native** | Directly queryable from a standard Nexthink field |
| ⚠️ **Partial** | Signal exists but requires inference or a Remote Action |
| ❌ **Out of scope** | Not available in the Nexthink data model |

---

## H1 — System Stability

### H1.1 — System Crash (BSOD / Kernel Panic)

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High (single) → 🔴 Critical (≥ 3 in 7 days) |
| **Nexthink Fields** | `system_crash.number_of_system_crashes`, `system_crash.error_code_hexadecimal`, `system_crash.label`, `system_crash.time` |
| **Nexthink Table** | `device_performance.system_crashes` |
| **DEX Link** | `score.endpoint.system_crash_value`, `score.endpoint.system_crash_score_impact` |
| **Remediation** | Remote Action: *Read Mini Dump* (Windows) / *Analyze Kernel Panic Reports* (macOS) |

A system crash is an uncontrolled OS failure. A single crash may be
transient; a pattern is a reliability failure. Always report
`system_crash.label` and `system_crash.error_code_hexadecimal` — the
error code is the primary diagnostic lead.

**Severity thresholds (default — override with CFG-H01):**

| Crashes in 7 days | Severity |
|-------------------|----------|
| 1 | 🟡 Medium |
| 2 | 🟠 High |
| ≥ 3 | 🔴 Critical |

**Executive framing:**
> *"[N] devices have experienced [X] system crashes in the past 7 days.
> The most common error is [label]. Recurring crashes indicate a
> hardware, driver, or OS-level failure requiring investigation."*

---

### H1.2 — Hard Reset (Unexpected Power-Off)

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High (single) → 🔴 Critical (≥ 2 in 7 days) |
| **Nexthink Fields** | `hard_reset.number_of_hard_resets`, `hard_reset.time` |
| **Nexthink Table** | `device_performance.hard_resets` |
| **DEX Link** | `score.endpoint.hard_reset_value`, `score.endpoint.hard_reset_score_impact`, `score.endpoint.device_reliability_value` |
| **Remediation** | Investigate power supply, thermal events, driver conflicts |

A hard reset is a forced power-off without OS shutdown — typically
caused by a power failure, thermal shutdown, or a crash so severe the
OS cannot write a dump. It is distinct from a system crash: no error
code is generated, making root cause harder to determine.

**Compounding rule:** H1.1 + H1.2 on the same device within 7 days =
automatic Critical override. A device that both crashes and hard-resets
is in active hardware failure.

---

## H2 — Application Reliability

### H2.1 — Application Crash Frequency

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High (by frequency) |
| **Nexthink Fields** | `crash.number_of_crashes`, `binary.name`, `binary.product_name`, `binary.version`, `crash.binary_path_root` |
| **Nexthink Tables** | `execution.crashes`, `binary.binaries` |
| **Remediation** | Update binary / Remote Action: reinstall application |

Application crashes are user-impacting stability failures. Always
report the binary name, product name, and version — the version is
the primary remediation lead (a known-bad version can be targeted
for update fleet-wide).

**Severity thresholds (default — override with CFG-H02):**

| App crashes in 7 days (same binary, same device) | Severity |
|--------------------------------------------------|----------|
| 1–2 | 🟡 Medium |
| 3–4 | 🟠 High |
| ≥ 5 | 🔴 Critical |

> **Agent instruction:** When `crash.binary_path_root` = `temp`,
> `removable_drive`, or `recycle_bin`, cross-reference with Security
> Exposure Model E4. A crashing binary from an unusual path is both
> a stability signal and a security signal.

---

### H2.2 — Application Freeze Events

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High (if recurring) |
| **Nexthink Fields** | `event.number_of_freezes`, `binary.name`, `binary.product_name` |
| **Nexthink Tables** | `execution.events`, `binary.binaries` |
| **Remediation** | Investigate resource contention (CPU/memory); update or replace binary |

A freeze is a non-crash failure — the application becomes unresponsive
without terminating. Freezes are often caused by resource starvation
(CPU or memory pressure) rather than code defects. Always correlate
H2.2 with H4.1 (CPU pressure) and H4.2 (memory pressure) before
attributing cause to the application itself.

---

## H3 — Startup & Login Performance

### H3.1 — Slow Boot Duration

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High (by duration) |
| **Nexthink Fields** | `boot.duration`, `boot.type`, `boot.number_of_boots`, `device.boot.last_full_boot_duration`, `device.boot.days_since_last_full_boot` |
| **Nexthink Tables** | `device_performance.boots`, `device.devices` |
| **DEX Link** | `score.endpoint.boot_speed_value`, `score.endpoint.boot_speed_score_impact` |
| **Remediation** | Remote Action: *Get Login Details* / disk cleanup / startup program audit |

Boot duration is measured from power-on to OS ready state. Report
`boot.type` alongside duration — a `fast_startup` or `hibernate_resume`
is expected to be faster than a `full_boot`; comparing them without
context produces misleading severity assessments.

**Severity thresholds (default — override with CFG-H03):**

| Full boot duration | Severity |
|-------------------|----------|
| 60–120 s | 🟡 Medium |
| 121–180 s | 🟠 High |
| > 180 s | 🔴 Critical |

> **Agent instruction:** Always state `boot.type` when reporting boot
> duration. A 90-second `full_boot` is different from a 90-second
> `fast_startup`. Never compare across boot types without noting the
> distinction.

---

### H3.2 — Slow Login / Desktop Readiness

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High (by duration) |
| **Nexthink Fields** | `login.time_until_desktop_is_visible`, `login.time_until_desktop_is_ready`, `login.logon_script_duration`, `login.number_of_logins` |
| **Nexthink Table** | `session.logins` |
| **DEX Link** | `score.endpoint.logon_speed_score_impact` |
| **Remediation** | Audit GPO / logon scripts; Remote Action: *Get Login Details* |

`time_until_desktop_is_visible` measures the user's perceived wait.
`time_until_desktop_is_ready` measures when the device is actually
usable. The gap between the two is the "false ready" window — the
desktop is visible but the device is still loading. A large gap
indicates heavy startup processes or slow GPO application.

**Severity thresholds (default — override with CFG-H04):**

| Desktop visible time | Severity |
|---------------------|----------|
| 15–30 s | 🟡 Medium |
| 31–60 s | 🟠 High |
| > 60 s | 🔴 Critical |

---

## H4 — Hardware Resource Pressure

### H4.1 — Sustained CPU Pressure

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🔴 Critical (by duration) |
| **Nexthink Fields** | `event.cpu.duration_with_high_usage`, `event.normalized_cpu_usage`, `event.cpu_usage` |
| **Nexthink Table** | `device_performance.events` |
| **DEX Link** | `score.endpoint.CPU_usage_value`, `score.endpoint.device_performance_value` |
| **Remediation** | Identify top CPU-consuming processes via execution events; Remote Action: process audit |

`event.cpu.duration_with_high_usage` measures cumulative time above
80% normalized CPU usage in a given period. This is more meaningful
than a point-in-time average — a device at 40% average CPU may have
spent 2 hours at 100%, which is invisible in the average.

**Severity thresholds (default — override with CFG-H05):**

| Duration with high CPU usage per day | Severity |
|--------------------------------------|----------|
| 15–30 min | 🟡 Medium |
| 31–60 min | 🟠 High |
| > 60 min | 🔴 Critical |

> **Agent instruction:** Always report `event.normalized_cpu_usage`
> alongside duration. High duration + low average = spike pattern
> (intermittent process). High duration + high average = sustained
> overload (hardware undersizing or runaway process).

---

### H4.2 — Critical Memory Pressure

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🔴 Critical (by duration) |
| **Nexthink Fields** | `event.duration_with_high_memory_pressure`, `event.memory_pressure`, `event.memory_swap_rate`, `event.used_memory`, `event.installed_memory`, `device.hardware.memory` |
| **Nexthink Tables** | `device_performance.events`, `device.devices` |
| **DEX Link** | `score.endpoint.memory_pressure_value`, `score.endpoint.memory_pressure_score_impact`, `score.endpoint.memory_usage_value` |
| **Remediation** | Identify memory-heavy processes; assess RAM upgrade eligibility |

`event.duration_with_high_memory_pressure` measures cumulative time
in critical (red) memory pressure. `event.memory_swap_rate` is the
key secondary signal — active swap means the OS is compensating for
insufficient RAM by writing to disk, which directly causes application
freezes and slow response times.

**Severity thresholds (default — override with CFG-H06):**

| Duration in critical memory pressure per day | Severity |
|----------------------------------------------|----------|
| 10–20 min | 🟡 Medium |
| 21–40 min | 🟠 High |
| > 40 min | 🔴 Critical |

**Compounding rule:** H4.2 (memory pressure) + H2.2 (application
freezes) on the same device = the freezes are likely memory-caused.
Do not attribute to the application without first ruling out resource
starvation.

---

### H4.3 — Insufficient Physical RAM

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium |
| **Nexthink Fields** | `device.hardware.memory`, `event.installed_memory` |
| **Nexthink Tables** | `device.devices`, `device_performance.events` |
| **Remediation** | Flag for hardware refresh; assess RAM upgrade eligibility |

A device with insufficient RAM for its workload will chronically
exhibit H4.2. This signal identifies the structural cause rather
than the symptom.

```
RAM_MINIMUM_THRESHOLD:
  # Must be defined by the organization based on workload profiles.
  # Suggested defaults (update per OS and role requirements):
  # Knowledge worker:  8 GB minimum
  # Power user:       16 GB minimum
  # Developer:        32 GB minimum
```

---

## H5 — Storage Health

### H5.1 — Low System Drive Free Space

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🔴 Critical (by threshold) |
| **Nexthink Fields** | `event.system_drive_free_space`, `event.system_drive_capacity`, `event.system_drive_usage` |
| **Nexthink Table** | `device_performance.events` |
| **Remediation** | Remote Action: *Free Up Disk Space* / user notification campaign |

Low disk space directly causes OS instability, failed updates, and
application errors. It is one of the most reliably remediable fleet
health issues — a disk cleanup Remote Action can recover space
immediately.

**Severity thresholds (default — override with CFG-H07):**

| System drive free space | Severity |
|------------------------|----------|
| 5–10 GB | 🟡 Medium |
| 2–5 GB | 🟠 High |
| < 2 GB | 🔴 Critical |

**Compounding rule:** H5.1 (low disk) + C3 (no reboot > 30 days) =
pending patches cannot be applied even if triggered. The disk must
be freed before patching can succeed.

---

### H5.2 — Non-System Drive Capacity Exhaustion

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium |
| **Nexthink Fields** | `event.non_system_drive_free_space`, `event.non_system_drive_capacity` |
| **Nexthink Table** | `device_performance.events` |
| **Remediation** | User notification; data archival policy enforcement |

Secondary drives near capacity may indicate data hoarding, backup
failures, or policy non-compliance (e.g., data stored locally rather
than in approved cloud storage). Less operationally critical than
H5.1 but relevant for data governance.

---

## H6 — Network Connectivity Quality

### H6.1 — Poor Wi-Fi Signal Strength

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High (sustained) |
| **Nexthink Fields** | `event.wifi.signal_strength`, `event.wifi.noise_level`, `event.wifi.ssid`, `event.primary_physical_adapter.type` |
| **Nexthink Table** | `connectivity.events` |
| **DEX Link** | `score.endpoint.wifi_signal_strength_value`, `score.endpoint.wifi_signal_strength_score_impact`, `score.endpoint.network_quality_value` |
| **Remediation** | Remote Action: *Get Wi-Fi Signal Strength* / escalate to network team |

Wi-Fi signal strength is measured in dBm (negative scale — closer to
0 is stronger). Poor signal is the most common cause of collaboration
tool degradation (Teams, Zoom) and VPN instability.

**Signal quality reference (default — override with CFG-H08):**

| Signal strength (dBm) | Quality | Severity |
|-----------------------|---------|----------|
| > −65 dBm | Good | — |
| −65 to −75 dBm | Fair | 🟡 Medium |
| < −75 dBm | Poor | 🟠 High |
| < −85 dBm | Unusable | 🔴 Critical |

> **Agent instruction:** Always report `event.wifi.ssid` alongside
> signal strength. Poor signal on a known corporate SSID points to
> AP placement or interference. Poor signal on an unknown SSID may
> indicate the user is on a personal or public network — a separate
> security concern (cross-reference A5).

---

### H6.2 — High TCP Connection Failure Ratio

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Nexthink Fields** | `event.failed_connection_ratio`, `event.number_of_failed_connections`, `event.number_of_connections`, `event.destination.type` |
| **Nexthink Table** | `connection.events` |
| **Remediation** | Investigate via Network View; check DNS, VPN, proxy configuration |

A sustained TCP failure ratio above 20% indicates a connectivity
path problem. This is an anomaly signal — it requires correlation
with H6.1 (Wi-Fi signal) and Security Model E3 before attributing
cause. A high failure ratio on `destination.type` = `intranet` points
to internal infrastructure; on `internet` it points to VPN or DNS.

---

## H7 — DEX Score as Composite Health Proxy

The DEX score is not a primary diagnostic signal — it is a composite
output of the signals above. The agent uses it as a fleet-level
health proxy and as a triage filter to identify which devices or
populations warrant deeper investigation.

### DEX Score Structure (for agent reference)

| Score | Field | What it aggregates |
|-------|-------|--------------------|
| `score.value` | Overall DEX | Technology + Sentiment |
| `score.technology.value` | Technology | Endpoint + Applications + Collaboration |
| `score.endpoint.value` | Endpoint | Boot speed, login speed, CPU, memory, crashes, network |
| `score.applications.value` | Applications | App crashes, freezes, performance |
| `score.collaboration.value` | Collaboration | Teams, Zoom, and other collaboration tool quality |
| `score.sentiment.value` | Sentiment | Campaign survey responses |

**DEX score interpretation (fixed scale — do not modify):**

| Score range | Label | Color |
|-------------|-------|-------|
| 71–100 | Good | 🟢 Green |
| 31–70 | Average | 🟡 Yellow |
| 0–30 | Frustrating | 🔴 Red |

> **Agent instruction:** The DEX score scale is fixed and must never
> be altered. Do not apply custom thresholds to DEX scores. The
> 0–30 / 31–70 / 71–100 bands are the only valid interpretation.

### H7.1 — Frustrating DEX Score (Endpoint Subscore)

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Nexthink Field** | `score.endpoint.value` ≤ 30 |
| **Nexthink Table** | `dex.scores` |
| **Remediation** | Drill into subscore impacts: `score.endpoint.*_score_impact` fields identify the dominant contributor |

A Frustrating Endpoint subscore means the device is consistently
delivering a poor hardware experience. Use the `*_score_impact` fields
to identify which signal is driving the score down — this is the
fastest path from a DEX alert to a root cause.

**Impact field lookup:**

| Impact field | Root cause it points to |
|-------------|------------------------|
| `score.endpoint.system_crash_score_impact` | → H1.1 System crashes |
| `score.endpoint.hard_reset_score_impact` | → H1.2 Hard resets |
| `score.endpoint.boot_speed_score_impact` | → H3.1 Slow boot |
| `score.endpoint.logon_speed_score_impact` | → H3.2 Slow login |
| `score.endpoint.memory_pressure_score_impact` | → H4.2 Memory pressure |
| `score.endpoint.wifi_signal_strength_score_impact` | → H6.1 Poor Wi-Fi |
| `score.endpoint.virtual_session_lag_score_impact` | → VDI session lag |

---

### H7.2 — Frustrating Applications Subscore

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Nexthink Field** | `score.applications.value` ≤ 30 |
| **Nexthink Table** | `dex.scores` |
| **Remediation** | Cross-reference with H2.1 (crashes) and H2.2 (freezes) to identify the offending application |

---

### H7.3 — Frustrating Collaboration Subscore

| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Nexthink Field** | `score.collaboration.value` ≤ 30 |
| **Nexthink Table** | `dex.scores` |
| **Remediation** | Cross-reference with H6.1 (Wi-Fi signal) and H6.2 (TCP failures) — collaboration tools are highly network-sensitive |

A Frustrating Collaboration score with good Endpoint and Applications
scores almost always points to a network issue, not a device issue.
Do not recommend hardware remediation for a collaboration score
problem without first ruling out H6.1 and H6.2.

---

## Fleet Health Risk Patterns

### Pattern H-S1 — Hardware Failure Signature
**Trigger:** H1.1 (system crashes ≥ 3 in 7 days) + H1.2 (hard resets ≥ 2 in 7 days)

| | |
|---|---|
| **Severity** | 🔴 Critical |
| **Risk Label** | *"Device in active hardware failure"* |
| **Remediation** | Immediate: escalate to hardware team; prepare device replacement |

**Executive framing:**
> *"[N] devices have experienced both repeated system crashes and
> unexpected power-offs in the past 7 days. This pattern is consistent
> with hardware failure — driver corruption, thermal issues, or failing
> storage. These devices require physical inspection and likely
> replacement."*

---

### Pattern H-S2 — Resource Starvation Loop
**Trigger:** H4.1 (CPU pressure > 60 min/day) + H4.2 (memory pressure > 40 min/day) + H2.2 (application freezes)

| | |
|---|---|
| **Severity** | 🟠 High |
| **Risk Label** | *"Device chronically under-resourced for its workload"* |
| **Remediation** | Hardware refresh assessment; interim: identify and terminate resource-heavy processes |

**Executive framing:**
> *"[N] devices are spending significant time under critical CPU and
> memory pressure, resulting in application freezes. These devices
> are structurally under-resourced for their current workload.
> Short-term process management can provide relief; the long-term
> fix is hardware refresh."*

---

### Pattern H-S3 — Patch Delivery Blocked
**Trigger:** H5.1 (disk free < 5 GB) + C3 (no reboot > 30 days) + C2 (no security updates > 30 days)

| | |
|---|---|
| **Severity** | 🟠 High |
| **Risk Label** | *"Device structurally unable to receive patches"* |
| **Remediation** | Sequential: (1) Free disk space via Remote Action, (2) Force reboot, (3) Trigger patch deployment |

**Executive framing:**
> *"[N] devices have not received security patches in over 30 days,
> have not rebooted, and are critically low on disk space. These
> three conditions together mean patches cannot be downloaded,
> installed, or activated. Remediation must be sequenced — disk
> cleanup first, then reboot, then patching."*

---

### Pattern H-S4 — Remote Worker Connectivity Failure
**Trigger:** H6.1 (Wi-Fi signal < −75 dBm, sustained) + H7.3 (Collaboration score ≤ 30) + `device.location.type` = `remote`

| | |
|---|---|
| **Severity** | 🟠 High |
| **Risk Label** | *"Remote employee with degraded connectivity and collaboration quality"* |
| **Remediation** | Campaign: notify user with Wi-Fi improvement guidance; Remote Action: *Get Wi-Fi Signal Strength* |

**Executive framing:**
> *"[N] remote employees are experiencing poor Wi-Fi signal and
> degraded collaboration tool performance simultaneously. This
> pattern indicates a home network or physical environment issue
> rather than a corporate infrastructure problem. User-facing
> guidance and a connectivity assessment are the appropriate
> first response."*

---

## Signal → DEX → Risk: Reasoning Chain

```
1. DETECT health signals
   └─ Query H1–H6 for threshold breaches on target device/population

2. CHECK DEX score context
   └─ Use score.endpoint.*_score_impact to confirm which signals
      are driving DEX degradation
   └─ A signal that is not reflected in the DEX score may be
      transient or already resolved

3. IDENTIFY fleet health patterns
   └─ Check if active signals match H-S1 through H-S4
   └─ A pattern supersedes its constituent signals in the summary

4. CROSS-REFERENCE with security exposure
   └─ H5.1 (low disk) → check C3 (patch pending)
   └─ H2.1 (app crash from temp path) → check E4 (suspicious binary)
   └─ H6.1 (poor Wi-Fi on unknown SSID) → check A5 (firewall partial)

5. COMMUNICATE findings
   └─ Lead with pattern label if triggered
   └─ State affected device/user count
   └─ Provide sequential remediation steps where order matters
      (e.g., H-S3: disk → reboot → patch)
```

---

## Data Model Constraints Summary

| Constraint | Detail |
|-----------|--------|
| **SMART / disk health** | Physical disk health (SMART data) is not a native Nexthink field. Use Remote Action: *Get Windows Disk Configuration* to retrieve disk type (SSD/HDD). |
| **GPU pressure** | GPU usage is available in `execution.events` per binary but not as a device-level sustained pressure metric in `device_performance.events`. |
| **VDI-specific signals** | VDI session lag (`score.endpoint.virtual_session_lag_score_impact`) and VM storage health (`vdi_event.health.*`) are available but only for VDI-enrolled devices. |
| **Mobile devices** | Mobile health signals (`mobile_event.memory.free`, `mobile_event.storage.free`, `mobile_event.wifi.signal_strength`) are available in separate mobile tables and must not be mixed with desktop device queries. |
| **DEX score retention** | `dex.scores` is retained for 30 days in NQL. Trend analysis beyond 30 days requires the Digital Experience module dashboards (not queryable via NQL). |
| **Boot type context** | Always report `boot.type` alongside `boot.duration`. Comparing `fast_startup` and `full_boot` durations without context produces misleading severity assessments. |
```
