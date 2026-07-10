# Security Exposure Models — Executive Risk & Exposure Intelligence Agent

## Purpose

This document is the authoritative catalogue of security exposure indicators
used by the agent. It is organized into seven exposure models, each covering
a distinct risk domain. Each indicator specifies its detection logic, its
Nexthink data source, its data availability status, its severity, its
regulatory relevance, and the remediation path available in the platform.

At the end of the document, Security Risk Patterns combine multiple indicators
into named composite threat signatures for executive communication.

---

## ⚙️ Customization Manifest

This file contains configuration placeholders that MUST be populated by the
IT security team before the agent can produce accurate results. The agent
will explicitly state when a required configuration is missing — it will
never silently assume a default.

| Ref | Section | Placeholder | Required / Optional | Impact if missing |
|-----|---------|-------------|--------------------|--------------------|
| CFG-01 | D1 | `APPROVED_OS_BASELINES` | **Required** | Agent cannot assess OS compliance — will declare configuration gap |
| CFG-02 | C4 | `VULNERABLE_PACKAGES` | **Required** | Agent cannot perform CVE-level package detection |
| CFG-03 | G1 | `APPROVED_PUBLISHERS` | **Required** | Agent cannot identify shadow IT — all software treated as unknown |
| CFG-04 | F2 | `SHARED_DEVICE_THRESHOLD` | **Required** | Agent cannot flag shared device exposure |
| CFG-05 | B2 | Authorized local admin account list | **Required** | Agent uses fallback rule (> 2 accounts) — lower precision |
| CFG-06 | F1 | Dormant account thresholds (30 / 90 / 180 days) | Optional | Defaults apply if not overridden |
| CFG-07 | F3 | Executive population definition | **Required** | Inherited from `01_risk_scoring_framework.md` Section 8 — must be consistent |
| CFG-08 | All F/G | Regulated departments list (Finance, Legal, HR, Compliance) | Optional | Defaults apply — organization may add or remove departments |

### Configuration Status

CFG-01 APPROVED_OS_BASELINES: [ ] Not configured [ ] Configured — date: ____ CFG-02 VULNERABLE_PACKAGES: [ ] Not configured [ ] Configured — date: ____ CFG-03 APPROVED_PUBLISHERS: [ ] Not configured [ ] Configured — date: ____ CFG-04 SHARED_DEVICE_THRESHOLD: [ ] Not configured [ ] Configured — date: ____ CFG-05 LOCAL_ADMIN_ALLOWLIST: [ ] Not configured [ ] Configured — date: ____ CFG-06 DORMANCY_THRESHOLDS: [ ] Not configured [ ] Using defaults CFG-07 EXECUTIVE_POPULATION: [ ] Not configured [ ] Inherited from 01_risk_scoring_framework.md CFG-08 REGULATED_DEPARTMENTS: [ ] Not configured [ ] Using defaults


### Agent Behavior When Configuration Is Missing

| Situation | Agent behavior |
|-----------|---------------|
| CFG-01 missing | States: *"OS baseline policy has not been configured. The agent cannot determine OS compliance status."* |
| CFG-02 missing | Skips C4 entirely. Does not attempt CVE detection. |
| CFG-03 missing | States: *"No approved publisher list is defined. Shadow IT detection is unavailable."* |
| CFG-04 missing | Applies default threshold of > 3 distinct users per device in 30 days. States assumption explicitly. |
| CFG-05 missing | Applies fallback rule: flags devices with > 2 local admin accounts. States assumption explicitly. |
| CFG-07 missing | States: *"Executive population is not defined. F3 and Pattern S3 cannot be evaluated."* |


---

## Data Availability Convention

Each indicator carries one of three availability tags:

| Tag | Meaning |
|-----|---------|
| ✅ **Native** | Directly queryable from a standard Nexthink field |
| ⚠️ **Partial** | Signal exists but requires inference, cross-referencing, or a Remote Action |
| ❌ **Out of scope** | Not available in the Nexthink data model; requires an external IAM/SIEM source |

> **Agent instruction:** Never present a ❌ indicator as a Nexthink-derived
> finding. When an out-of-scope indicator is relevant, state explicitly:
> *"This signal requires data from [external system] — Nexthink cannot
> confirm or deny this exposure."*

---

## Exposure Model Index

| Model | Domain | Indicators |
|-------|--------|-----------|
| **A** | Endpoint Protection | A1–A6 |
| **B** | Privilege & Access Control | B1–B2 |
| **C** | Patch Exposure | C1–C4 |
| **D** | Unsupported Platforms | D1–D3 |
| **E** | Security Control Drift | E1–E4 |
| **F** | Identity & Governance Exposure | F1–F3 |
| **G** | Shadow IT & Encryption Gaps | G1–G4 |

---

## Model A — Endpoint Protection

### A1 — Antivirus Not Up To Date
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 · SOC2 |
| **Field** | `device.antiviruses.is_up_to_date` = `no` |
| **Table** | `device.antiviruses` |
| **Remediation** | Remote Action: trigger AV definition update |

Device is running with stale threat definitions. Direct, confirmed exposure.
Escalate to Critical override if device is also remote (`device.location.type`
= `remote`).

---

### A2 — Antivirus Status Not Reported
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 · GDPR |
| **Field** | `device.antiviruses.is_up_to_date` = `not_reported` |
| **Table** | `device.antiviruses` |
| **Remediation** | Remote Action: Get Windows Security Center Health Status |

Visibility failure. Treat as exposed until proven otherwise. Never interpret
as a clean state.

---

### A3 — Antivirus Real-Time Protection Disabled
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 · SOC2 · HIPAA |
| **Field** | `device.antiviruses.real_time_protection` = `disabled` |
| **Table** | `device.antiviruses` |
| **Remediation** | Remote Action: re-enable RTP / escalate to endpoint team |

Engine present but scanning disabled. A3 + A1 simultaneously = automatic
Critical override (Pattern S1).

---

### A4 — Firewall Real-Time Protection Disabled
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 · SOC2 |
| **Field** | `device.firewalls.real_time_protection` = `disabled` |
| **Table** | `device.firewalls` |
| **Remediation** | Remote Action: re-enable firewall / escalate to network security |

All host-level traffic filtering bypassed. Always Critical.

---

### A5 — Firewall Partially Enabled
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 |
| **Field** | `device.firewalls.real_time_protection` = `partially_enabled` |
| **Table** | `device.firewalls` |
| **Remediation** | Investigate inactive firewall profiles (Domain / Private / Public) |

Highest risk for remote employees on untrusted networks. Escalate to Critical
if `device.location.type` = `remote`.

---

### A6 — No Firewall Record (Windows)
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 · GDPR |
| **Field** | No entry in `device.firewalls` for device |
| **Table** | `device.firewalls` |
| **Remediation** | Verify platform; escalate if confirmed Windows |

> Only apply to `device.operating_system.platform` = `Windows`. macOS and
> Linux do not report firewall state via this table.

---

## Model B — Privilege & Access Control

### B1 — User Account Control At Risk
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 · SOC2 |
| **Field** | `device.user_account_control_status` = `at_risk` |
| **Table** | `device.devices` |
| **Remediation** | Enforce UAC policy via GPO / Intune |

UAC misconfigured or disabled. Applications — including malware — can silently
acquire administrator rights. Escalate to Critical if executive user is
affected.

---

### B2 — Unauthorized Local Administrator Account
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 · SOC2 · GDPR |
| **Field** | `device.local_admins.name` — non-standard accounts present |
| **Table** | `device.local_admins` |
| **Remediation** | Remote Action: audit and remove unauthorized local admin accounts |

Each unauthorized local admin is a persistent backdoor bypassing central
identity governance. "Authorized" account list must be defined by the
organization. In absence of that list, flag any device with > 2 local admin
accounts as a review candidate.

---

## Model C — Patch Exposure

### C1 — Operating System Last Update Stale
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High → 🔴 Critical (by age) |
| **Regulatory** | ISO27001 · GDPR · SOC2 · HIPAA |
| **Field** | `device.operating_system.last_update` |
| **Table** | `device.devices` |
| **Remediation** | Workflow: trigger OS update via Intune / WSUS |

Measures elapsed time since the last OS-level update was applied.

| Days since last OS update | Severity |
|--------------------------|----------|
| 15–30 days | 🟡 Medium |
| 31–60 days | 🟠 High |
| > 60 days | 🔴 Critical |

---

### C2 — No Security Package Updates in Observation Window
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🔴 Critical (by age) |
| **Regulatory** | ISO27001 · SOC2 |
| **Fields** | `package.type` = `Update`, `installation.time` |
| **Tables** | `package.installed_packages`, `package.installations` |
| **Remediation** | Workflow: trigger patch deployment |

No `Update`-type package installed in the observation window. Proxy for
patch cycle dropout — does not enumerate specific CVEs but identifies
devices that have fallen out of the patching process entirely.

| Days since last Update package | Severity |
|-------------------------------|----------|
| 15–30 days | 🟡 Medium |
| 31–60 days | 🟠 High |
| > 60 days | 🔴 Critical |

---

### C3 — Device Not Rebooted (Pending Patches Unapplied)
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium |
| **Regulatory** | ISO27001 |
| **Field** | `device.boot.days_since_last_full_boot` ≥ 30 |
| **Table** | `device.devices` |
| **Remediation** | Remote Action: schedule reboot / campaign to notify user |

Patches installed but not yet active. A device not rebooted for 30+ days
is likely running with pending security patches unapplied. Escalate to High
when combined with C1 or C2 (Pattern S2).

---

### C4 — Specific Vulnerable Package Version Detected
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🟠 High → 🔴 Critical (CVE-dependent) |
| **Regulatory** | ISO27001 · SOC2 |
| **Fields** | `package.name`, `package.version`, `package.publisher` |
| **Tables** | `package.installed_packages` |
| **Remediation** | Remote Action: uninstall / update specific package |

Nexthink can identify which version of a package is installed on which
device. It cannot natively cross-reference CVE databases. The agent must
be provided with a list of known-vulnerable package versions to flag.

> **Agent instruction:** C4 requires a `VULNERABLE_PACKAGES` list to be
> maintained in this knowledge source by the IT security team. Without it,
> the agent cannot perform CVE-level detection. Do not invent vulnerable
> version numbers.

```
VULNERABLE_PACKAGES:
  # Format: "package_name": ["vulnerable_version_1", "vulnerable_version_2"]
  # Example: "OpenSSL": ["3.0.0", "3.0.1"]
  # Must be maintained by the IT security team.
```
### CVE Exposure Evaluation

When evaluating a CVE, the agent must:

1. Identify the affected software.
2. Identify affected package versions.
3. Identify installed vulnerable versions.
4. Identify available remediation packages or updates.
5. Determine affected devices.
6. Determine affected users.
7. Determine affected executive populations.
8. Determine affected regulated populations.
9. Report exposure according to the Risk Scoring Framework.

Exposure classifications:

#### Confirmed Exposure

The vulnerable software version is present and remediation is absent.

#### Potential Exposure

The affected software is present but version information is incomplete.

#### Exposure Unknown

Available telemetry is insufficient to determine exposure status.

Agent instruction:

The agent must never claim CVE exposure without identifying
the affected software and version.

The agent must clearly distinguish:

- Confirmed Exposure
- Potential Exposure
- Exposure Unknown
---

## Model D — Unsupported Platforms

### D1 — End-of-Life Operating System
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 · GDPR · SOC2 · HIPAA |
| **Field** | `device.operating_system.name` |
| **Table** | `device.devices` |
| **Remediation** | Workflow: OS upgrade campaign / Intune compliance enforcement |

Devices running an OS version that no longer receives security patches.
This is a permanent, unresolvable exposure until the OS is upgraded.
The only remediation is migration — no patch or configuration change
can close this gap.

```
APPROVED_OS_BASELINES:
  # Must be defined and maintained by the organization.
  # Update at each OS release cycle.
  # Example structure only — do not use these values without validation:
  # Windows: "<minimum approved Windows version>"
  # macOS:   "<minimum approved macOS version>"
  # Linux:   "<organization-defined distribution and version>"
```

> **Agent instruction:** Never hardcode OS version names in this file.
> The `APPROVED_OS_BASELINES` block above must be populated by the IT
> team. Until it is, the agent must state: *"OS baseline policy has not
> been configured — the agent cannot determine OS compliance status."*

---

### D2 — Collector Assigned to Unsupported OS Group
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 |
| **Field** | `device.collector.update_group` = `Unsupported OS` |
| **Table** | `device.devices` |
| **Remediation** | Escalate to endpoint management team for OS upgrade planning |

Nexthink itself classifies this device as running an OS it can no longer
fully support. This is a platform-confirmed signal, not an inference.
Treat as corroborating evidence for D1.

---

### D3 — Outdated Nexthink Collector Version
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium |
| **Regulatory** | ISO27001 |
| **Fields** | `device.collector.version`, `device.collector.target_version` |
| **Table** | `device.devices` |
| **Remediation** | Collector auto-update via update group scheduling |

A device running a Collector version behind the target version has reduced
telemetry fidelity. Security signals from this device may be incomplete
or missing. Not a direct security exposure, but a visibility degradation
that compounds other indicators.

---

## Model E — Security Control Drift

*Security control drift occurs when a device's security posture degrades
over time from a previously compliant state — not due to a single event
but through gradual erosion of controls.*

### E1 — Open Monitor Alert (Critical or High Priority, Unresolved)
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 |
| **Fields** | `alert.status` = `open`, `monitor.priority` = `critical` or `high`, `alert.duration` |
| **Tables** | `alert.alerts`, `alert.monitors` |
| **Remediation** | Immediate investigation via Device View; trigger relevant workflow |

Always report `alert.duration` alongside the alert. An alert open > 24h
with `alert.recovery_time` null must be described as *unresolved*, not
merely *active*. An alert open ≥ 7 days is a chronic unresolved risk.

---

### E2 — Repeated Security Alerts on Same Device (Chronic Pattern)
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 |
| **Fields** | `alert.number_of_alerts` ≥ 3 within 30 days, same device |
| **Tables** | `alert.alerts` |
| **Remediation** | Root cause investigation; escalate if remediation has already been attempted |

A device that repeatedly triggers the same alert after recovery is not
being remediated — it is being managed. This is a drift pattern, not an
isolated incident. Flag it as a systemic control failure.

---

### E3 — High Volume of Failed Outbound Connections (Anomaly)
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 |
| **Fields** | `event.failed_connection_ratio` > 0.20, `event.destination.type` = `internet` |
| **Table** | `connection.events` |
| **Remediation** | Investigate via Network View; check DNS, VPN, C2 activity |

Anomaly signal only. Do not present as a confirmed security incident.
Frame as: *"Device X is generating an unusually high rate of failed
internet connections — investigation required to rule out network
misconfiguration or malicious activity."*

---

### E4 — Binary Executed from Suspicious Path
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High |
| **Regulatory** | ISO27001 |
| **Fields** | `event.binary_path_root` = `temp`, `removable_drive`, `recycle_bin`, or `net_drive` |
| **Table** | `execution.events` |
| **Remediation** | Investigate binary identity; escalate to security team if unrecognized |

Legitimate enterprise software is rarely executed from temp directories,
removable drives, or network drives. Execution from these paths is a
behavioral anomaly consistent with malware staging, lateral movement,
or policy bypass.

> **Agent instruction:** Always report `binary.name`, `binary.company`,
> and `binary.product_name` alongside E4. An unknown company with no
> product name is a stronger signal than a known vendor binary in an
> unusual path.

---

## Model F — Identity & Governance Exposure

*This model covers identity and governance risks that are frequently
discussed at risk committee level. Nexthink provides partial but
meaningful signals for each indicator. Where IAM system data would
provide a more complete picture, the agent must state the gap explicitly.*

### F1 — Dormant Account Exposure
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟠 High → 🔴 Critical (with A2) |
| **Regulatory** | ISO27001 · GDPR · SOC2 |
| **Fields** | `user.days_since_last_seen` ≥ 90, `user.type` = `domain` |
| **Table** | `user.users` |
| **Remediation** | Escalate to IT and HR: verify employment status; initiate account deactivation review |

A domain user account that has not been seen active for 90+ days but
remains in the Nexthink inventory. This is a strong indicator of an
offboarded employee whose account has not been deprovisioned — a
persistent unauthorized access vector.

**Severity escalation:**
- 30–89 days inactive → 🟡 Medium (monitor)
- 90–179 days inactive → 🟠 High (review required)
- ≥ 180 days inactive → 🔴 Critical (likely offboarded; immediate action)

> **Agent instruction:** Nexthink confirms the account was active on
> the endpoint. It cannot confirm whether the account is still enabled
> in Active Directory or Entra ID — that requires an IAM system query.
> State this gap: *"Nexthink shows this account has not been active for
> X days. Whether the account is still enabled in the directory requires
> verification in [AD/Entra ID]."*

---

### F2 — Shared Device Exposure
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🟡 Medium → 🟠 High (in sensitive departments) |
| **Regulatory** | ISO27001 · GDPR |
| **Fields** | `session.logins` — count of distinct `user.name` per device over 30 days |
| **Table** | `session.logins`, `device.devices` |
| **Remediation** | Review device assignment policy; enforce per-user device allocation where required |

A device used by more than a configurable threshold of distinct users
within 30 days. Shared devices complicate audit trails, make per-user
security posture assessment unreliable, and increase the blast radius
of a compromised credential.

```
SHARED_DEVICE_THRESHOLD:
  # Must be defined by the organization.
  # Suggested default: > 3 distinct users in 30 days = shared device candidate
  # Kiosk / training room devices should be excluded via device.entity tagging
```

Escalate to High if any of the sharing users belongs to Finance, Legal,
HR, or Compliance (`user.ad.department`).

---

### F3 — Privileged User Risk Concentration
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 · SOC2 · GDPR |
| **Fields** | `user.ad.job_title`, `user.ad.department`, cross-referenced with any active A/B/C/D/E indicator |
| **Tables** | `user.users`, joined with indicator-specific tables |
| **Remediation** | Immediate: executive security review; prioritize remediation of all active indicators |

A user belonging to the executive population or a privileged role
(C-suite, VP, Director, Finance, Legal, Compliance) who has one or
more active Critical or High security indicators on their device.
This is the highest-priority pattern in the entire framework — a
single compromised executive device has disproportionate organizational
impact.

**Detection logic:** F3 is not a standalone indicator. It is triggered
when:
1. `user.ad.job_title` matches the executive population definition
   (see `01_risk_scoring_framework.md` Section 8), AND
2. At least one indicator from Models A, B, C, D, or E is active
   on the same device.

> **Agent instruction:** F3 must always be the first item in any
> executive risk summary when triggered. State it as:
> *"[Role] [Name/device] has [N] active security indicators including
> [most severe]. This is a Priority 1 — Executive Security Exposure."*

---

## Model G — Shadow IT & Encryption Gaps

### G1 — Unauthorized Software Installed (Shadow IT)
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🟡 Medium → 🟠 High (sensitive dept) |
| **Regulatory** | ISO27001 · GDPR |
| **Fields** | `package.name`, `package.publisher`, `package.type` = `Program` |
| **Tables** | `package.installed_packages` |
| **Remediation** | Campaign: software governance survey / Remote Action: uninstall package |

Software installed outside the approved application catalogue. Introduces
unvetted attack surface, potential data exfiltration vectors, and license
compliance risk. Requires an `APPROVED_PUBLISHERS` list maintained by IT.

```
APPROVED_PUBLISHERS:
  # Must be defined and maintained by the organization.
  # Flag any package.publisher not in this list as a review candidate.
```

---

### G2 — Binary Executed from Unknown Publisher
| | |
|---|---|
| **Availability** | ✅ Native |
| **Severity** | 🟡 Medium → 🟠 High (if also E4) |
| **Regulatory** | ISO27001 |
| **Fields** | `binary.company` is empty or null, `binary.product_name` is empty |
| **Table** | `binary.binaries`, `execution.events` |
| **Remediation** | Investigate binary identity; block via application control policy |

An unsigned or unattributed binary being actively executed. Not all
unsigned binaries are malicious — internal scripts and legacy tools
often lack publisher metadata — but the combination of unknown publisher
+ unusual execution path (E4) is a strong compound signal.

---

### G3 — Disk Encryption Gap (Mobile Devices)
| | |
|---|---|
| **Availability** | ✅ Native (mobile only) |
| **Severity** | 🔴 Critical |
| **Regulatory** | ISO27001 · GDPR · HIPAA |
| **Field** | `mobile_device.governance.is_encrypted` = `false` |
| **Table** | `device.mobile_devices` |
| **Remediation** | Enforce encryption via MDM policy |

An unencrypted mobile device containing corporate data is a direct
GDPR breach risk if lost or stolen. No other compensating control
substitutes for at-rest encryption.

---

### G4 — Disk Encryption Gap (Windows Desktops — Partial Coverage)
| | |
|---|---|
| **Availability** | ⚠️ Partial |
| **Severity** | 🔴 Critical (if confirmed unencrypted) |
| **Regulatory** | ISO27001 · GDPR · HIPAA |
| **Field** | Not natively available in standard Nexthink device inventory for Windows |
| **Table** | N/A — requires Remote Action |
| **Remediation** | Remote Action: *Enable BitLocker Encryption* (Windows) |

BitLocker encryption status is not exposed as a native inventory field
for Windows desktops in the standard Nexthink data model. The agent
cannot confirm or deny encryption status from inventory data alone.

> **Agent instruction:** When G4 is relevant, state the limitation
> explicitly: *"Windows disk encryption status is not available as a
> native Nexthink field. To assess BitLocker status at scale, the
> 'Enable BitLocker Encryption' Remote Action can be used to both
> detect and remediate unencrypted devices."*

---

## Security Risk Patterns

Patterns are named composite threat signatures. They combine multiple
indicators into a single, executive-ready risk label. The agent must
detect patterns before surfacing individual indicators — a pattern
carries more weight and communicates more clearly to a non-technical
audience than a list of separate findings.

**Detection rule:** A pattern is triggered when ALL of its constituent
indicators are simultaneously active on the same device or user.

---

### Pattern S1 — Critical Risk Cluster
**Trigger:** A1 (AV outdated) + A3 (AV RTP disabled) or A4 (Firewall disabled)

| Attribute | Value |
|-----------|-------|
| **Severity** | 🔴 Critical — Automatic Override |
| **Risk Label** | *"Endpoint with no active protection"* |
| **Regulatory** | ISO27001 · SOC2 · HIPAA |
| **Priority Driver** | Security Exposure (Priority 2) |

**Executive framing:**
> *"[N] devices have both outdated antivirus definitions and disabled
> real-time protection [or firewall]. These endpoints are operating
> with no active security layer. Immediate remediation is required."*

**Recommended action:** Trigger AV remediation Remote Action fleet-wide
on affected devices; escalate to CISO if count > 10.

---

### Pattern S2 — Technical Debt Exposure
**Trigger:** C1 (OS update stale > 60 days) + C2 (no security packages > 30 days) + C3 (no reboot > 30 days)

| Attribute | Value |
|-----------|-------|
| **Severity** | 🟠 High |
| **Risk Label** | *"Endpoint frozen in time — patch debt accumulating"* |
| **Regulatory** | ISO27001 · SOC2 |
| **Priority Driver** | Security Exposure (Priority 2) |

**Executive framing:**
> *"[N] devices have not received OS updates, security patches, or a
> reboot in over 30 days. These endpoints are accumulating unmitigated
> vulnerabilities with each passing day. This is a patch governance
> failure, not an isolated incident."*

**Recommended action:** Workflow: force reboot + patch deployment;
campaign to notify users of mandatory maintenance window.

---

### Pattern S3 — Executive Security Exposure
**Trigger:** F3 (Privileged User Risk) + any Pattern S1 or S2, OR any single Critical indicator from Models A–E

| Attribute | Value |
|-----------|-------|
| **Severity** | 🔴 Critical — Automatic Override |
| **Risk Label** | *"Executive endpoint with active security exposure"* |
| **Regulatory** | ISO27001 · SOC2 · GDPR |
| **Priority Driver** | Executive Exposure (Priority 1) |

**Executive framing:**
> *"[Role] [device/user identifier] has [pattern or indicator] active.
> An executive endpoint with unresolved security exposure represents
> disproportionate organizational risk — both operational and
> reputational. This requires immediate, dedicated remediation."*

**Recommended action:** Direct escalation to CISO and IT leadership;
dedicated remediation track separate from fleet-wide patching.

---

### Pattern S4 — Dormant Identity with Active Device
**Trigger:** F1 (dormant account ≥ 90 days) + device still active (`device.days_since_last_seen` < 7)

| Attribute | Value |
|-----------|-------|
| **Severity** | 🔴 Critical |
| **Risk Label** | *"Ghost account — active device, inactive user"* |
| **Regulatory** | ISO27001 · GDPR · SOC2 |
| **Priority Driver** | Regulatory Exposure (Priority 3) |

**Executive framing:**
> *"[N] devices remain active in the environment but their associated
> user accounts have not been seen for 90+ days. This is consistent
> with offboarded employees whose access was not fully revoked.
> Each represents an unauthorized access vector and a potential
> GDPR data residency issue."*

**Recommended action:** Escalate to IT and HR; cross-reference with
HR offboarding records; initiate device retrieval and account
deprovisioning process.

---

### Pattern S5 — Shadow IT in Regulated Environment
**Trigger:** G1 (unauthorized software) + user in Finance, Legal, HR, or Compliance (`user.ad.department`)

| Attribute | Value |
|-----------|-------|
| **Severity** | 🟠 High |
| **Risk Label** | *"Unvetted software in regulated function"* |
| **Regulatory** | ISO27001 · GDPR · SOC2 |
| **Priority Driver** | Regulatory Exposure (Priority 3) |

**Executive framing:**
> *"[N] employees in [department] are running software not approved
> by IT. In a regulated function, unvetted applications may process
> or transmit sensitive data outside of approved, auditable channels.
> This is both a security and a compliance risk."*

**Recommended action:** Campaign: software governance survey to
understand usage intent; Remote Action: uninstall if confirmed
unauthorized.

---

#### Pattern S6 — Critical Vulnerability Exposure

Trigger:

C4 (Specific Vulnerable Package Version Detected)
+
Critical or High severity vulnerability

Attribute | Value
--------- | ------
Severity | 🔴 Critical
Risk Label | "Known vulnerable software actively present"
Priority Driver | Security Exposure (Priority 2)

Executive framing:

"[N] devices are running software versions associated with a known
critical vulnerability. Remediation is available but has not yet
been applied."

Recommended action:

- Deploy available update
- Remove vulnerable version
- Prioritize executive and regulated populations

---

## Indicator → Pattern → Risk: Reasoning Chain

The agent must always reason in this order:

```
1. DETECT indicators
   └─ Query Nexthink fields for each active indicator (Models A–G)
   └─ When a CVE is referenced:
      - identify affected software
      - identify affected version
      - check remediation availability
      - determine exposure population

2. IDENTIFY patterns
   └─ Check if any combination of active indicators matches S1–S5
   └─ A pattern supersedes its constituent indicators in the summary

3. APPLY priority drivers
   └─ Order findings by: Executive (P1) > Security (P2) > Regulatory (P3)
      > Business-Critical Apps (P4) > Population Impact (P5)

4. COMMUNICATE risk
   └─ Lead with pattern label and severity
   └─ State affected population count
   └─ Append regulatory tag if regulated department is involved
   └─ Close with recommended action
```

---

## Data Model Constraints Summary

| Constraint | Detail |
|-----------|--------|
| **BitLocker (Windows)** | Not a native inventory field. Use Remote Action for assessment. |
| **CVE cross-reference** | Not available natively. Requires `VULNERABLE_PACKAGES` list from IT. |
| **AD account enabled status** | Not queryable. Nexthink shows endpoint activity, not directory state. |
| **macOS/Linux firewall** | Not reported via `device.firewalls` (WMI-based). |
| **Approved OS baselines** | Must be defined by the organization. Agent cannot determine compliance without this. |
| **Approved software catalogue** | Must be defined by the organization. Agent cannot flag shadow IT without this. |
| **Custom fields** | Not queried. All indicators use native Nexthink fields only. |
| **Absence ≠ clean** | Missing records in antiviruses, firewalls, or packages = visibility gap, never a clean state. |
```

---
