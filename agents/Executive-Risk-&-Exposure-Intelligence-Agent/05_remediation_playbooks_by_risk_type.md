# Remediation Playbooks — Executive Risk & Exposure Intelligence Agent

---

## ⚙️ Customization Manifest

| Ref | Section | Placeholder | Required / Optional | Impact if missing |
|-----|---------|-------------|--------------------|--------------------|
| CFG-P01 | All playbooks | `CHANGE_MANAGEMENT_REQUIRED` | **Required** | Agent cannot determine whether a remediation needs a change ticket before execution |
| CFG-P02 | P2, P5 | `PATCH_DEPLOYMENT_TOOL` | Optional | Agent describes action generically (Intune / SCCM / manual) |
| CFG-P03 | P3 | `REBOOT_NOTIFICATION_LEAD_TIME_HOURS` | Optional | Default: 2 hours user notice before forced reboot |
| CFG-P04 | P6 | `UNAUTHORIZED_SOFTWARE_POLICY` | Optional | Default: notify user + IT; do not auto-remove |
| CFG-P05 | All | `EXECUTIVE_DEVICE_APPROVAL_REQUIRED` | **Required** | Agent must know whether executive devices require additional approval before any remediation |
| CFG-P06 | All | `REMEDIATION_EVIDENCE_RETENTION_DAYS` | Optional | Default: 30 days (aligned with Nexthink event retention) |

### Configuration Status

```
CFG-P01 CHANGE_MANAGEMENT_REQUIRED:          [ ] Not configured  [ ] Yes  [ ] No
CFG-P02 PATCH_DEPLOYMENT_TOOL:               [ ] Not configured  [ ] Configured: ____________
CFG-P03 REBOOT_NOTIFICATION_LEAD_TIME_HOURS: [ ] Not configured  [ ] Using default: 2h
CFG-P04 UNAUTHORIZED_SOFTWARE_POLICY:        [ ] Not configured  [ ] Using default: notify only
CFG-P05 EXECUTIVE_DEVICE_APPROVAL_REQUIRED:  [ ] Not configured  [ ] Yes  [ ] No
CFG-P06 REMEDIATION_EVIDENCE_RETENTION_DAYS: [ ] Not configured  [ ] Using default: 30 days
```

### Agent Behavior When Configuration Is Missing

| Situation | Agent behavior |
|-----------|---------------|
| CFG-P01 missing | States: *"Change management policy is not configured. The agent will describe the remediation steps but will not recommend execution until this is defined."* |
| CFG-P05 missing | Treats all executive devices as requiring additional approval. States assumption explicitly before any remediation recommendation. |

---

## Purpose

This document defines the step-by-step remediation playbooks the agent
uses to respond to active indicators and patterns identified in
`02_security_exposure_models.md` and `03_fleet_health_risk_signals.md`.

Each playbook follows a fixed structure:
1. **Trigger** — which indicator(s) or pattern(s) activate this playbook
2. **Pre-flight checks** — what to verify before acting
3. **Remediation steps** — ordered, with Nexthink capability mapped to each step
4. **Verification** — how to confirm the remediation succeeded
5. **Escalation** — when to stop and hand off to a human
6. **Evidence** — what to record for audit purposes

---

## Remediation Principles

**1. Diagnose before acting.**
The agent never recommends a remediation step without first confirming
the indicator is still active. A finding from 6 hours ago may already
be resolved. Always re-query the relevant field before proposing action.

**2. Sequence matters.**
Some remediations have hard dependencies. P3 (patch deployment) requires
P4 (disk space) to be resolved first. P7 (system crash investigation)
must precede any driver update. Never present steps out of order.

**3. Executive devices require explicit approval.**
If `CFG-P05 = Yes`, the agent must state: *"This device belongs to an
executive. Remediation requires approval from [role] before execution."*
It will describe the steps but will not recommend triggering any Remote
Action or Workflow until approval is confirmed in the conversation.

**4. Reversibility governs urgency.**
Irreversible actions (file deletion, software removal, account
modification) require explicit user confirmation regardless of severity.
Reversible actions (service restart, data collection, notification
campaign) can be recommended immediately.

**5. Record everything.**
Every remediation the agent recommends must be traceable via
`remote_action.executions` or `workflow.executions`. The agent must
confirm that execution results are queryable before closing a playbook.

---

## Playbook Index

| ID | Playbook | Triggers | Reversible |
|----|----------|---------|------------|
| P1 | **Antivirus Remediation** | A1, A2, A3 | ✅ Yes |
| P2 | **Firewall Remediation** | A4, A5, A6 | ✅ Yes |
| P3 | **OS Patch & Reboot Enforcement** | C2, C3 (no reboot), D2 | ⚠️ Partial |
| P4 | **Disk Space Recovery** | H5.1, H5.2, H-S3 | ⚠️ Partial |
| P5 | **System Crash Investigation** | H1.1, H1.2, H-S1 | ✅ Yes |
| P6 | **Unauthorized Software Response** | C4, G1 | ⚠️ Partial |
| P7 | **Remote Worker Connectivity** | H6.1, H6.2, H-S4 | ✅ Yes |
| P8 | **Privileged Access Remediation** | B1, B2, F3 | ❌ No — human only |
| P9 | **Dormant & Visibility Gap Response** | D1, D3, F1 | ✅ Yes |
| P10 | **Resource Pressure Response** | H4.1, H4.2, H-S2 | ✅ Yes |

---

## P1 — Antivirus Remediation

### Triggers
- A1: AV definitions not up to date
- A2: AV status not reported (visibility gap)
- A3: AV real-time protection disabled

### Pre-flight Checks

Before acting, re-query:
```
device.antiviruses.is_up_to_date
device.antiviruses.enabled
device.antiviruses.real_time_protection_enabled
device.last_seen
```

If `device.last_seen` > 24 hours ago: do not proceed — the device
is offline. Log as "pending — device offline" and re-check at next
heartbeat.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Confirm AV product name and version | Query `device.antiviruses` | — |
| 2 | If AV service is stopped: restart AV service | Remote Action: *Restart Service* (WinDefend / WdNisSvc / MDCoreSvc) | ✅ |
| 3 | If definitions outdated: trigger AV update via MDM | Workflow or `PATCH_DEPLOYMENT_TOOL` | ✅ |
| 4 | If AV not reporting after 48h: escalate to endpoint security team | Human escalation | — |

> **Agent instruction — A2 (not reporting):** A device where AV
> status is not reported may have the AV product uninstalled, the
> WMI service broken, or the Collector not receiving data. Do not
> assume the device is unprotected — state the ambiguity explicitly:
> *"AV status cannot be confirmed on this device. This may indicate
> a missing product, a WMI reporting failure, or a Collector gap.
> Manual verification is required."*

### Verification

Re-query `device.antiviruses.is_up_to_date` and
`device.antiviruses.real_time_protection_enabled` after 1 hour.
Confirm `remote_action.executions.status` = `success` for any
Remote Action triggered.

### Escalation

Escalate to endpoint security team if:
- AV service restart fails (status = `failure` in `remote_action.executions`)
- AV product is absent (not just outdated) — requires re-deployment
- Device is a C-suite device and AV is absent (Pattern S1 override)

### Evidence

```
Query: remote_action.executions
Filter: remote_action.name = "Restart Service"
        execution.status = success
        execution.time within remediation window
Fields: device.name, execution.time, execution.status, execution.outputs
```

---

## P2 — Firewall Remediation

### Triggers
- A4: Firewall disabled
- A5: Firewall partially enabled (not all profiles)
- A6: No firewall product registered

### Pre-flight Checks

Re-query `device.firewalls` fields before acting.
Confirm `device.operating_system.platform` — firewall remediation
steps differ between Windows and macOS.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Confirm firewall product and profile status | Query `device.firewalls` | — |
| 2 | If Windows Firewall disabled: enable via GPO push or MDM policy | `PATCH_DEPLOYMENT_TOOL` / MDM | ✅ |
| 3 | If third-party firewall disabled: restart firewall service | Remote Action: *Restart Service* | ✅ |
| 4 | If A5 (partial): identify which profiles are disabled and push profile-specific policy | MDM / GPO | ✅ |
| 5 | If A6 (no product): escalate — firewall must be deployed, not just enabled | Human escalation | — |

> **Agent instruction — A4 + A1 simultaneously (Pattern S2):**
> This combination triggers a Critical override. State it explicitly:
> *"This device has both AV out of date and firewall disabled
> simultaneously. This is a Critical security pattern (S2).
> Both must be remediated in parallel, not sequentially."*
> Recommend P1 and P2 concurrently.

### Verification

Re-query `device.firewalls.enabled` after MDM policy application
(allow up to 4 hours for policy propagation). Confirm all three
Windows Firewall profiles (Domain, Private, Public) are enabled
for A5 cases.

### Escalation

Escalate if:
- Firewall cannot be enabled remotely (policy conflict, MDM enrollment issue)
- A6 persists after 24 hours — requires manual software deployment
- Device is executive and firewall is absent (T3 alert required)

### Evidence

```
Query: device.devices
Filter: device.firewalls.enabled = true
        device.name in [remediated device list]
Fields: device.name, device.firewalls.enabled,
        device.firewalls.product_name, device.last_seen
```

---

## P3 — OS Patch & Reboot Enforcement

### Triggers
- C2: Missing security updates (OS update > 30 days)
- C3: No reboot > 30 days (patches pending activation)
- D2: Device not rebooted > 30 days (visibility + patch gap)

### Pre-flight Checks

**Mandatory sequence check — run P4 first if H5.1 is also active.**
A device with < 5 GB free disk cannot download or install patches.
If H5.1 is active on the same device, P4 must complete before P3.

Re-query:
```
device.operating_system.days_since_last_update
device.boot.days_since_last_full_boot
event.system_drive_free_space  (confirm ≥ 10 GB before proceeding)
```

Also run Remote Action: *Test Pending Reboot* to confirm whether
a reboot is already pending and what is blocking it.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Check pending reboot status and reason | Remote Action: *Test Pending Reboot* | ✅ |
| 2 | If patches not yet downloaded: trigger patch scan via `PATCH_DEPLOYMENT_TOOL` | MDM / SCCM / Intune | ✅ |
| 3 | Notify user of upcoming reboot with `CFG-P03` lead time | Campaign: reboot notification | ✅ |
| 4 | Schedule or enforce reboot | Workflow: *Device Restart Enforcement* | ⚠️ |
| 5 | Confirm patch installation post-reboot | Re-query `device.operating_system.days_since_last_update` | — |

> **Agent instruction — reboot enforcement on executive devices:**
> If `CFG-P05 = Yes` and the device belongs to an executive, do not
> recommend Step 4 (forced reboot) without explicit approval.
> State: *"A forced reboot on an executive device requires approval
> from [role] before execution. Steps 1–3 can proceed immediately."*

> **Agent instruction — C3 without C2:**
> A device that has not rebooted in > 30 days but has no missing
> updates may have patches downloaded but not yet activated. The
> reboot is still required. State: *"Patches appear current but
> the device has not rebooted in [N] days. A reboot is required
> to activate any pending updates and clear accumulated system
> state."*

### Verification

After reboot:
- Re-query `device.boot.days_since_last_full_boot` — should reset to 0
- Re-query `device.operating_system.days_since_last_update` — should reflect post-patch state
- Confirm `workflow.executions.outcome` = `action_taken` for restart workflow

### Escalation

Escalate if:
- Reboot enforcement fails after 2 attempts
- Device remains offline > 48 hours after reboot trigger
- Patch deployment fails post-reboot (requires `PATCH_DEPLOYMENT_TOOL` investigation)

### Evidence

```
Query: workflow.executions
Filter: workflow.name contains "restart"
        execution.outcome = action_taken
        execution.time within remediation window
Fields: device.name, execution.time, execution.status,
        execution.outcome, execution.trigger_method
```

---

## P4 — Disk Space Recovery

### Triggers
- H5.1: System drive free space < 10 GB
- H5.2: Non-system drive near capacity
- H-S3: Patch delivery blocked (disk + no reboot + no updates)

### Pre-flight Checks

Re-query `event.system_drive_free_space` and
`event.system_drive_capacity` to confirm current state.

Confirm `device.operating_system.platform` — cleanup behavior
differs between Windows and macOS.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Assess reclaimable space from old user profiles | Remote Action: *Get Old User Profiles* | ✅ |
| 2 | Notify user and offer cleanup choice (light / deep) | Remote Action: *Disk Cleanup* (with campaign) | ⚠️ |
| 3 | If user unresponsive > 4h and severity Critical: run silent deep cleanup | Remote Action: *Disk Cleanup* (silent, Deep level) | ⚠️ |
| 4 | Clear recycle bin if oversized | Remote Action: *Invoke Recycle Bin Cleanup* | ⚠️ |
| 5 | Re-query free space — if still < 5 GB: escalate | Human escalation | — |

> **Agent instruction — irreversibility warning:**
> Steps 2–4 permanently delete files. Before recommending Step 3
> (silent deep cleanup), the agent must state:
> *"Silent deep cleanup will permanently delete temporary files
> and recycle bin content. This action cannot be undone.
> Confirm before proceeding."*
> This confirmation is required regardless of severity level.

> **Agent instruction — H-S3 sequencing:**
> When P4 is triggered as part of H-S3 (Patch Delivery Blocked),
> state the full sequence explicitly:
> *"Remediation must follow this order: (1) Free disk space [P4],
> (2) Enforce reboot [P3 Step 4], (3) Trigger patch deployment
> [P3 Step 2]. Executing these out of order will not resolve
> the patch delivery block."*

### Verification

Re-query `event.system_drive_free_space` after cleanup.
Confirm `remote_action.executions.outputs` ("Cleanup space")
to quantify space recovered.

### Escalation

Escalate if:
- Free space remains < 2 GB after cleanup (structural storage issue)
- Large files identified that cannot be auto-deleted (user data, application data)
- Device requires storage expansion or hardware refresh

### Evidence

```
Query: remote_action.executions
Filter: remote_action.name in ["Disk cleanup", "Invoke recycle bin cleanup"]
        execution.status = success
Fields: device.name, execution.time, execution.outputs (Cleanup space),
        execution.trigger_method
```

---

## P5 — System Crash Investigation

### Triggers
- H1.1: System crashes ≥ 1 in 7 days
- H1.2: Hard resets ≥ 1 in 7 days
- H-S1: Hardware failure signature (crashes + hard resets combined)

### Pre-flight Checks

Re-query:
```
system_crash.error_code_hexadecimal
system_crash.label
system_crash.time
hard_reset.number_of_hard_resets
hard_reset.time
device.operating_system.platform
```

Confirm platform before selecting investigation tool:
- Windows → *Get BSOD Crash Driver Minidump Analysis* or *Read Mini Dump*
- macOS → *Analyse Kernel Panic Reports* (Remote Action)

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Retrieve crash error code and label from telemetry | Query `device_performance.system_crashes` | ✅ |
| 2 | Run minidump analysis to identify faulty driver | Remote Action: *Get BSOD Crash Driver Minidump Analysis* (Windows) | ✅ |
| 3 | Cross-reference driver identified with recent package installs | Query `package.installed_packages` (last 14 days) | ✅ |
| 4 | If driver identified: trigger driver update | Workflow: *Driver Update Management* | ✅ |
| 5 | If no driver identified and hard resets present: escalate to hardware team | Human escalation | — |
| 6 | If H-S1 pattern (crashes + hard resets): flag for device replacement assessment | Human escalation | — |

> **Agent instruction — crash error code interpretation:**
> Always report `system_crash.error_code_hexadecimal` and
> `system_crash.label` together. The hex code is the primary
> diagnostic lead for the hardware/driver team. Do not attempt
> to interpret the error code yourself — surface it and let the
> specialist team act on it. Example output:
> *"The device has experienced 3 system crashes in 7 days.
> Most recent error: 0x0000007E (SYSTEM_THREAD_EXCEPTION_NOT_HANDLED).
> Minidump analysis identified driver: [driver name], version [X],
> installed [date]."*

> **Agent instruction — hard resets without crash codes:**
> Hard resets produce no error code. When H1.2 is active without
> H1.1, state: *"Hard resets do not generate diagnostic codes.
> Possible causes include power supply failure, thermal shutdown,
> or a crash too severe for the OS to write a dump. Physical
> inspection is required to rule out hardware failure."*

### Verification

After driver update:
- Monitor `device_performance.system_crashes` for 7 days
- If crash count = 0 for 7 days post-remediation: playbook closed
- If crashes recur: escalate to hardware team with full crash history

### Escalation

Escalate immediately if:
- H-S1 pattern is active (crashes + hard resets)
- Minidump analysis returns no driver (hardware-level failure suspected)
- Device belongs to executive population

### Evidence

```
Query: remote_action.executions
Filter: remote_action.name = "Get BSOD Crash Driver Minidump Analysis"
        execution.status = success
Fields: device.name, execution.time,
        execution.outputs (Hardware driver name, Hardware driver version,
        Crashing file name, Error, Mini dump date)
```

---

## P6 — Unauthorized Software Response

### Triggers
- C4: Package from vulnerable/unauthorized list detected
- G1: Software from unapproved publisher installed in regulated department

### Pre-flight Checks

Re-query `package.installed_packages` to confirm the package is
still present. Confirm `CFG-P04` (unauthorized software policy)
before acting — the default is notify only, not auto-remove.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Confirm package name, version, publisher, and install date | Query `package.installed_packages` | — |
| 2 | Cross-reference with `VULNERABLE_PACKAGES` list (CFG-02) | Agent knowledge base lookup | — |
| 3 | Notify user that unauthorized software was detected | Campaign: notification | ✅ |
| 4 | If policy = remove: assess replacement availability | Workflow: *Vulnerable Application Removal Assessment* | ⚠️ |
| 5 | If no replacement available: notify IT and user; do not auto-remove | Campaign + human escalation | — |
| 6 | If CVE-linked and Critical: escalate immediately regardless of policy | Human escalation | — |

> **Agent instruction — auto-removal gate:**
> The agent must never recommend automatic software removal without
> first confirming `CFG-P04 = remove` AND that a replacement is
> available (Step 4). Removing software without a replacement
> may break a business-critical workflow. State:
> *"Unauthorized software detected: [package name] v[version].
> Per policy, removal requires confirmation that a replacement
> is available. Running assessment now."*

> **Agent instruction — G1 in regulated departments:**
> When G1 is active in Finance, Legal, HR, or Compliance, elevate
> severity to High regardless of the software's CVE status.
> Unapproved software in a regulated department is a compliance
> risk independent of its technical vulnerability profile.

### Verification

Re-query `package.installed_packages` after removal to confirm
the package is no longer present. Confirm
`workflow.executions.outcome` = `action_taken`.

### Escalation

Escalate if:
- Package cannot be removed remotely (requires local admin rights)
- Package is business-critical and no replacement exists
- CVE is actively exploited (check `VULNERABLE_PACKAGES` list for exploit status)

### Evidence

```
Query: workflow.executions
Filter: workflow.name = "Vulnerable application removal assessment"
        execution.outcome = action_taken
Fields: device.name, execution.time, execution.outcome,
        execution.outcome_details, execution.inputs
```

---

## P7 — Remote Worker Connectivity

### Triggers
- H6.1: Wi-Fi signal < −75 dBm (sustained)
- H6.2: TCP connection failure ratio > 20%
- H-S4: Remote worker connectivity failure pattern

### Pre-flight Checks

Re-query:
```
event.wifi.signal_strength
event.wifi.ssid
event.primary_physical_adapter.type
event.failed_connection_ratio
device.location.type  (confirm = remote)
```

Confirm the device is currently remote (`device.location.type` =
`remote`). If the device is onsite, poor Wi-Fi points to corporate
AP infrastructure — escalate to network team directly rather than
following this playbook.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Collect current Wi-Fi signal, SSID, band, and nearby networks | Remote Action: *Get Wi-Fi Signal Strength* | ✅ |
| 2 | Notify user with Wi-Fi improvement guidance | Campaign: connectivity guidance | ✅ |
| 3 | If TCP failure ratio > 20%: check VPN/proxy configuration | Remote Action: *Analyse Zscaler Logs* (if Zscaler in use) | ✅ |
| 4 | If signal < −85 dBm (unusable): recommend user relocate or use Ethernet | Campaign: escalation guidance | ✅ |
| 5 | If issue persists > 48h: escalate to IT support for home network assessment | Human escalation | — |

> **Agent instruction — SSID context:**
> Always report `event.wifi.ssid` alongside signal strength.
> If the SSID is not a recognized corporate network, note:
> *"The device is connected to an unrecognized network ([SSID]).
> This may be a personal or public Wi-Fi network. In addition
> to the connectivity issue, this may represent a security
> exposure — cross-reference with indicator A5 (firewall status)."*

> **Agent instruction — H6.2 without H6.1:**
> High TCP failure ratio without poor Wi-Fi signal points to a
> path issue (VPN, DNS, proxy, captive portal) rather than a
> physical signal problem. Do not recommend Wi-Fi improvement
> guidance in this case. State: *"TCP connection failures are
> elevated but Wi-Fi signal is adequate. This pattern suggests
> a VPN, DNS, or proxy configuration issue rather than a
> physical connectivity problem."*

### Verification

Re-query `event.wifi.signal_strength` and
`event.failed_connection_ratio` 24 hours after campaign delivery.
Confirm `remote_action.executions.status` = `success` for
Wi-Fi signal collection.

### Escalation

Escalate if:
- Signal remains < −85 dBm after user guidance (physical environment issue)
- TCP failures persist after VPN/proxy check (infrastructure investigation required)
- User reports inability to work — immediate IT support engagement

### Evidence

```
Query: remote_action.executions
Filter: remote_action.name = "Get Wi-Fi Signal Strength"
        execution.status = success
Fields: device.name, execution.time, execution.outputs,
        execution.trigger_method
```

---

## P8 — Privileged Access Remediation

### Triggers
- B1: UAC at risk
- B2: Unauthorized local admin account
- F3: Privileged user risk (executive + elevated access)

### ⚠️ Human-Only Playbook

**This playbook contains no automated remediation steps.**
Privilege and access control changes carry the highest risk of
unintended consequences and must be executed by a human with
appropriate authorization. The agent's role is to surface the
finding, provide the evidence, and recommend the human action.

### Agent Output for P8

When B1, B2, or F3 is active, the agent produces the following
structured finding for the IT security team:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PRIVILEGED ACCESS FINDING — Requires Human Action
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Indicator: [B1 / B2 / F3]
Device: [device.name]
User: [last_login_user_name]
Finding: [plain-language description]

Evidence:
- [field]: [value]
- [field]: [value]

Required human action:
1. [specific action — e.g., "Remove unauthorized local admin
   account [account name] via Active Directory"]
2. [specific action]

Escalation path: [IT Security team / IAM team / CISO]
Change ticket required: [Yes / No — per CFG-P01]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

> **Agent instruction — F3 (executive + elevated access):**
> F3 is the highest-priority access finding. Always generate a
> T3 alert (Critical Incident Executive Alert) in parallel with
> the P8 finding. Do not wait for the IT team to escalate — the
> CISO must be notified immediately.

---

## P9 — Dormant & Visibility Gap Response

### Triggers
- D1: Device not seen ≥ 7 days
- D3: Remote device with multiple security gaps (A-category)
- F1: Dormant user account (no login ≥ 30 days)

### Pre-flight Checks

Re-query `device.last_seen` to confirm the device is still
offline. A device that has since reconnected may have resolved
its own gaps via policy enforcement.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Confirm last seen date and last login user | Query `device.devices` | — |
| 2 | Send user notification requesting device check-in | Campaign: device check-in request | ✅ |
| 3 | If no response in 48h: escalate to manager or HR | Human escalation | — |
| 4 | If device offline > 30 days: flag for asset management review | Human escalation | — |
| 5 | For F1 (dormant account): notify IAM team for account review | Human escalation | — |

> **Agent instruction — D1 vs D3 distinction:**
> D1 is a visibility gap — the device may be fine but is not
> reporting. D3 is a compounded risk — the device was last seen
> with active security gaps and has since gone dark. Always
> distinguish these in the output:
> - D1: *"Device has not reported in [N] days. Security posture
>   at last contact was [status]."*
> - D3: *"Device has not reported in [N] days and had [N] active
>   security gaps at last contact. Current posture is unknown
>   but the last known state was High/Critical risk."*

### Verification

Monitor `device.last_seen` daily. Close playbook when device
reconnects and security indicators are re-evaluated.

### Escalation

Escalate if:
- Device offline > 14 days with no user response
- Device belongs to executive population
- D3 with Critical security gaps at last contact

---

## P10 — Resource Pressure Response

### Triggers
- H4.1: Sustained CPU pressure > 30 min/day
- H4.2: Critical memory pressure > 20 min/day
- H-S2: Resource starvation loop (CPU + memory + freezes)

### Pre-flight Checks

Re-query:
```
event.cpu.duration_with_high_usage
event.duration_with_high_memory_pressure
event.memory_swap_rate
event.number_of_freezes (execution.events)
device.hardware.memory
```

Confirm whether H4.2 is accompanied by active swap
(`event.memory_swap_rate` > 0) — swap activity confirms the
OS is compensating for insufficient RAM, not just reporting
a transient spike.

### Remediation Steps

| Step | Action | Nexthink Capability | Reversible |
|------|--------|--------------------|-----------:|
| 1 | Identify top CPU/memory-consuming processes | Query `execution.events` (cpu_time, memory by binary) | ✅ |
| 2 | If a single process is dominant: investigate binary | Cross-reference with `execution.crashes` and `binary.binaries` | ✅ |
| 3 | If runaway process identified: notify user and recommend restart | Campaign: process guidance | ✅ |
| 4 | If memory swap active and RAM < threshold: flag for hardware refresh | Human escalation | — |
| 5 | If H-S2 pattern (chronic): initiate hardware refresh assessment | Human escalation | — |

> **Agent instruction — freeze attribution:**
> When H4.2 (memory pressure) and H2.2 (application freezes) are
> active on the same device, always state the likely causal link
> before recommending application-level remediation:
> *"Application freezes are present alongside critical memory
> pressure. The freezes are likely caused by resource starvation,
> not by the application itself. Resolving memory pressure should
> be the first remediation step. Do not recommend application
> reinstallation or update until memory pressure is resolved."*

> **Agent instruction — CPU spike vs sustained overload:**
> Always distinguish between the two CPU patterns before acting:
> - High `event.cpu.duration_with_high_usage` + low
>   `event.normalized_cpu_usage` average = spike pattern.
>   A single process is periodically consuming all CPU.
>   Identify the process via `execution.events.cpu_time`.
> - High duration + high average = sustained overload.
>   The device is structurally under-resourced for its workload.
>   Process-level remediation will not resolve this — hardware
>   refresh assessment is the correct next step.

### Verification

Re-query `event.cpu.duration_with_high_usage` and
`event.duration_with_high_memory_pressure` over the 7 days
following any process-level intervention. If pressure metrics
return to below threshold: playbook closed. If they persist:
escalate to hardware refresh assessment.

### Escalation

Escalate if:
- Memory swap is active and `device.hardware.memory` is below
  the organization's `RAM_MINIMUM_THRESHOLD` (defined in H4.3)
- CPU pressure persists after the dominant process is addressed
- H-S2 pattern is active and the device is > 3 years old
  (hardware refresh is the only durable fix)

### Evidence

Query: execution.events Filter: device.name = [affected device] event.cpu_time > [threshold] during past 7d Fields: binary.product_name, binary.name, binary.version, event.cpu_time, event.memory, event.number_of_freezes


---

## Cross-Playbook Sequencing Rules

Some indicators co-occur in patterns that require a specific
remediation order. Executing steps out of sequence either fails
or creates new problems. The agent must always check for these
combinations before presenting a remediation plan.

### Sequencing Matrix

| Active indicators | Required order | Reason |
|-------------------|---------------|--------|
| H5.1 + C2 + C3 (H-S3) | P4 → P3 | Patches cannot install without disk space |
| H1.1 + H1.2 (H-S1) | P5 before any other | Hardware failure must be ruled out before software remediation |
| A1 + A4 (S2 pattern) | P1 ∥ P2 (parallel) | Both are Critical; neither depends on the other |
| H4.2 + H2.2 | P10 before app-level action | Memory pressure causes freezes; fixing the app first is futile |
| D3 + any security indicator | P9 first | Device is offline; no Remote Action can reach it |
| B2 + F3 | P8 only | Privilege changes are human-only; no automated step applies |

> **Agent instruction — sequencing communication:**
> When multiple playbooks apply to the same device, always state
> the sequence explicitly before listing steps:
> *"This device has [N] active indicators requiring remediation.
> The correct sequence is: [P4] → [P3] → [P1]. Executing these
> out of order will not resolve the underlying issues."*
> Never present a flat list of remediation steps when sequencing
> constraints apply.

---

## Remediation Tracking

Every remediation the agent recommends must be traceable.
The following queries confirm execution status for each
Nexthink capability type.

### Remote Action Execution Status

Query: remote_action.executions Filter: execution.request_time >= [remediation start time] device.name in [target device list] Fields: remote_action.name, device.name, execution.status, execution.status_details, execution.time, execution.trigger_method, execution.outputs


Status values to monitor:
| Status | Meaning | Agent action |
|--------|---------|-------------|
| `success` | Completed successfully | Proceed to verification |
| `failure` | Script failed on device | Check `execution.status_details`; escalate if repeated |
| `in_progress` | Running | Wait; re-query after 15 minutes |
| `waiting_on_device` | Device offline | Re-queue when device reconnects |
| `waiting_on_user` | Campaign response pending | Wait for `CFG-P03` lead time |
| `expired` | Timed out | Re-trigger; if repeated, escalate |
| `cancelled` | Manually cancelled | Log reason; do not re-trigger without human instruction |

### Workflow Execution Status

Query: workflow.executions Filter: execution.time >= [remediation start time] device.name in [target device list] Fields: execution.status, execution.outcome, execution.outcome_details, execution.trigger_method, execution.duration_seconds


Outcome values to monitor:
| Outcome | Meaning | Agent action |
|---------|---------|-------------|
| `action_taken` | Workflow completed and acted | Proceed to verification |
| `no_action_taken` | Workflow ran but condition not met | Re-evaluate indicator; may be self-resolved |
| `failed` | Workflow encountered an error | Check `execution.outcome_details`; escalate |

### Campaign Response Status

Query: campaign.responses Filter: response.first_targeted >= [campaign send time] campaign.name = [campaign name] Fields: user.name, response.state, response.state_details, response.time, response.answers


Response states to monitor:
| State | Meaning | Agent action |
|-------|---------|-------------|
| `answered` | User responded | Read answers; proceed accordingly |
| `declined` | User dismissed | Escalate if remediation is Critical |
| `targeted` | Delivered, not yet answered | Wait for `CFG-P03` lead time |
| `expired` | Campaign timed out | Re-send once; if no response, escalate |

---

## Evidence Retention

All remediation evidence is subject to Nexthink data retention:

| Table | Retention | Implication |
|-------|-----------|-------------|
| `remote_action.executions` | 30 days | Evidence window for audit purposes |
| `remote_action.executions_summary` | 13 months | Trend-level evidence only (no per-device outputs) |
| `workflow.executions` | 30 days | Execution outcomes available for 30 days |
| `workflow.executions_summary` | 13 months | Trend-level only |
| `campaign.responses` | Indefinite | Campaign evidence is permanently retained |

> **Agent instruction — evidence export:**
> If `CFG-P06` (evidence retention) is set to > 30 days, the agent
> must note: *"Nexthink retains Remote Action and Workflow execution
> records for 30 days. For audit evidence beyond this window,
> results must be exported to an external system (ITSM ticket,
> SIEM, or compliance platform) at the time of remediation.
> Campaign responses are retained indefinitely and do not require
> export."*

---

## Playbook Closure Criteria

A playbook is closed when all of the following are true:

1. **Indicator resolved:** The triggering indicator no longer
   meets its severity threshold when re-queried.
2. **Execution confirmed:** At least one `remote_action.executions`
   or `workflow.executions` record shows `status = success` /
   `outcome = action_taken` within the remediation window.
3. **Verification passed:** The post-remediation re-query confirms
   the metric has returned to within acceptable range.
4. **Evidence recorded:** Execution records are confirmed queryable
   within the 30-day retention window, or exported if required by
   `CFG-P06`.

If any criterion is not met, the playbook remains open and the
agent re-evaluates at the next scheduled check.

> **Agent instruction — partial resolution:**
> If an indicator is reduced in severity but not fully resolved
> (e.g., system crashes dropped from 5 to 1 in 7 days), do not
> close the playbook. State: *"Severity has reduced from Critical
> to Medium. The playbook remains open. Continue monitoring for
> 7 days before closure."*