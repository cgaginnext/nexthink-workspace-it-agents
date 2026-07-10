# Risk Scoring Framework — Executive Risk & Exposure Intelligence Agent

## Purpose

This document defines the complete risk scoring methodology used by the agent
to qualify, prioritize, and communicate IT risk to executive stakeholders.
It governs how the agent reasons about every risk signal: what it measures,
how it weights it, how it trends it, and — critically — who is affected.

---

## 1. Risk Score Components Overview

The agent computes a **Composite Risk Score (CRS)** across six components.
Each component is scored 0–100. The CRS is their weighted combination.

| # | Component                  | Weight | Core Question                                              |
|---|----------------------------|--------|------------------------------------------------------------|
| 1 | **Exposure Level**         | 25%    | How severe and direct is the threat on this device/user?   |
| 2 | **Population Impact**      | 20%    | How many employees are affected?                           |
| 3 | **Business Criticality**   | 20%    | Are business-critical applications or roles involved?      |
| 4 | **Trend Direction**        | 15%    | Is the situation improving, stable, or deteriorating?      |
| 5 | **Duration**               | 10%    | How long has the risk been active?                         |
| 6 | **Executive Population**   | 10%    | Are executives or high-sensitivity roles affected?         |

CRS = (Exposure × 0.25) + (Population × 0.20) + (Criticality × 0.20) + (Trend × 0.15) + (Duration × 0.10) + (Executive × 0.10)


> **Agent instruction:** Compute all six components before issuing any risk
> statement. Never collapse this into a single-dimension assessment.

---

## 2. Severity Classification

| CRS Range | Label        | Color | Executive Meaning                                          |
|-----------|--------------|-------|------------------------------------------------------------|
| 75–100    | **Critical** | 🔴    | Immediate escalation required; board-level visibility      |
| 50–74     | **High**     | 🟠    | Remediation within 48h; include in weekly risk report      |
| 25–49     | **Medium**   | 🟡    | Plan remediation; include in monthly review                |
| 0–24      | **Low**      | 🟢    | Acceptable; no immediate action required                   |

> **Agent instruction:** Always lead with the severity label and color when
> addressing an executive audience. Never open with raw metric values.

---

## 3. Component 1 — Exposure Level (Weight: 25%)

*How severe and direct is the threat on the affected device or user?*

### Signals and Nexthink Data Sources

| Signal | Nexthink Field | Score |
|--------|---------------|-------|
| Antivirus not up to date | `device.antiviruses.is_up_to_date` = `no` | +35 |
| Antivirus not reported (blind spot) | `device.antiviruses.is_up_to_date` = `not_reported` | +25 |
| Firewall RTP disabled | `device.firewalls.real_time_protection` = `disabled` | +30 |
| Firewall RTP partially enabled | `device.firewalls.real_time_protection` = `partially_enabled` | +15 |
| UAC at risk | `device.user_account_control_status` = `at_risk` | +20 |
| Active Critical/High monitor alert | `alert.monitors.priority` = `critical` or `high` + `alert.status` = `open` | +40 |
| Active Medium monitor alert | `alert.monitors.priority` = `medium` + `alert.status` = `open` | +20 |
| System crashes ≥ 3 in 7 days | `device_performance.system_crashes` | +25 |
| Hard resets ≥ 1 in 7 days | `device_performance.hard_resets` | +20 |
| DEX Endpoint score Frustrating (≤ 30) | `dex.scores` — `score.endpoint.value` | +20 |
| Remote device location | `device.location.type` = remote | +10 (multiplier on AV/FW gaps) |

**Scoring:** `Exposure_Score = min(100, sum of applicable signals)`

> A remote device with antivirus not reported is structurally more exposed than
> an onsite device with the same gap — the +10 remote multiplier applies on top
> of the AV/FW signal, not independently.

---

## 4. Component 2 — Population Impact (Weight: 20%)

*How many employees are experiencing this risk right now?*

### Signals and Nexthink Data Sources

| Affected Population | Nexthink Source | Score |
|---------------------|----------------|-------|
| ≥ 500 devices / users impacted | `alert.impacts` — count of distinct devices | 100 |
| 100–499 devices / users | `alert.impacts` | 75 |
| 20–99 devices / users | `alert.impacts` | 50 |
| 5–19 devices / users | `alert.impacts` | 25 |
| 1–4 devices / users | `alert.impacts` | 10 |

For fleet-wide risk signals (not tied to a specific alert), use:

| Fleet Metric | Nexthink Source | Score |
|---|---|---|
| > 20% of fleet with DEX ≤ 30 | `dex.scores` — `score.value` aggregated | 100 |
| 10–20% of fleet with DEX ≤ 30 | `dex.scores` | 65 |
| 5–10% of fleet with DEX ≤ 30 | `dex.scores` | 35 |
| < 5% of fleet with DEX ≤ 30 | `dex.scores` | 15 |

**Scoring:** `Population_Score = highest applicable band above`

> **Agent instruction:** Population Impact is the primary amplifier. A Low
> Exposure issue affecting 500 employees outranks a High Exposure issue on a
> single device. Always state the affected count explicitly in the executive
> summary.

---

## 5. Component 3 — Business Criticality (Weight: 20%)

*Are business-critical applications or organizational functions involved?*

### 5.1 Application Criticality Tiers

The agent uses `application.category` and `application.name` from
`application.applications` to classify applications:

| Tier | Definition | Examples | Score |
|------|-----------|----------|-------|
| **Tier 1 — Mission Critical** | Revenue-generating or compliance-mandatory apps | ERP, CRM, trading platforms, ITSM | 100 |
| **Tier 2 — Business Essential** | Collaboration and productivity backbone | Microsoft Teams, Outlook, Zoom, SharePoint | 65 |
| **Tier 3 — Standard** | General-purpose tools | Browsers, PDF readers, utilities | 25 |

> **Agent instruction:** Tier 1 and Tier 2 applications map to
> `application.category` = `Collaboration` or `Connectivity` as a proxy.
> For Tier 1 specifically, the IT team must maintain a named list of
> mission-critical application names to be embedded in this knowledge source
> (see Section 5.2).

### 5.2 Named Mission-Critical Applications (to be configured per organization)

Replace with your organization's actual Tier 1 application names
TIER_1_APPS:

""
""
"<Core banking / trading platform name>"
""

### 5.3 Organizational Criticality

| Condition | Nexthink Field | Score |
|-----------|---------------|-------|
| Affected user in Finance, Legal, or Compliance dept | `user.ad.department` | +20 |
| Affected user in Executive / C-Suite OU | `user.ad.organizational_unit` | +30 |
| Affected device is a shared/critical infrastructure device | `device.entity` (org-defined) | +20 |

**Scoring:** `Criticality_Score = min(100, App_tier + Org_contribution)`


### 5.4 Risk Concentration Factor

Concentrated risks receive additional priority.

Examples:

- Single site
- Single factory
- Single business unit
- Single executive team
- Single critical application

---

## 6. Component 4 — Trend Direction (Weight: 15%)

*Is the situation improving, stable, or deteriorating over time?*

### Signals and Nexthink Data Sources

The agent compares the **current period** (last 7 days) against the
**prior period** (days 8–30) using `dex.scores` — `score.time` as the
time axis.

| Trend Pattern | Definition | Score |
|---------------|-----------|-------|
| **Rapidly Deteriorating** | Score dropped > 15 pts period-over-period | 100 |
| **Deteriorating** | Score dropped 5–15 pts | 70 |
| **Stable (at risk)** | Score unchanged, remains in Frustrating/Average band | 40 |
| **Stable (healthy)** | Score unchanged, remains in Good band | 10 |
| **Improving** | Score improved > 5 pts | 0 |

### Applicable DEX Score Fields for Trend

| Scope | Field |
|-------|-------|
| Overall DEX | `score.value` |
| Endpoint health | `score.endpoint.value` |
| Application health | `score.applications.value` |
| Collaboration health | `score.collaboration.value` (if available) |

**Scoring:** `Trend_Score = score from table above, applied to the most
relevant scope for the risk being assessed`

> **Agent instruction:** Always state the direction explicitly:
> *"The endpoint score has dropped from 58 to 41 over the past 30 days —
> this is a deteriorating trend."* Never present a score in isolation without
> its direction.

---

## 7. Component 5 — Duration (Weight: 10%)

*How long has this risk been active without resolution?*

### Signals and Nexthink Data Sources

| Duration of Active Risk | Nexthink Field | Score |
|-------------------------|---------------|-------|
| Alert open ≥ 7 days | `alert.duration` (from `alert.alerts`) | 100 |
| Alert open 3–6 days | `alert.duration` | 70 |
| Alert open 1–2 days | `alert.duration` | 40 |
| Alert open < 24h | `alert.duration` | 15 |
| No active alert but chronic pattern (≥ 3 occurrences in 30 days) | `alert.number_of_alerts` | 60 |

For non-alert risks (e.g., persistent AV gap):

| Duration | Proxy Signal | Score |
|----------|-------------|-------|
| AV outdated ≥ 14 days | Cross-reference `device.antiviruses.is_up_to_date` with `user.days_since_last_seen` as proxy | 80 |
| AV outdated 7–13 days | Same | 50 |
| AV outdated < 7 days | Same | 20 |

**Scoring:** `Duration_Score = highest applicable band above`

> **Agent instruction:** Duration transforms a snapshot risk into a chronic
> risk. An alert open for 7+ days with no recovery (`alert.recovery_time`
> is null) must be flagged as *unresolved chronic risk* in the executive
> summary, not merely as an active alert.

---

## 8. Component 6 — Executive Population Impact (Weight: 10%)

*Are executives or high-sensitivity roles among the affected population?*

### Signals and Nexthink Data Sources

| Condition | Nexthink Field | Score |
|-----------|---------------|-------|
| C-suite / VP affected | `user.ad.job_title` contains CEO, CFO, CTO, CIO, CISO, COO, VP, SVP, EVP | 100 |
| Director level affected | `user.ad.job_title` contains Director | 70 |
| Manager level in sensitive dept | `user.ad.job_title` contains Manager + `user.ad.department` in Finance/Legal/HR/Compliance | 50 |
| Board member device | `user.ad.organizational_unit` (org-defined OU for board) | 100 |

**Scoring:** `Executive_Score = highest applicable band above`

> **Agent instruction:** Executive exposure is a **priority override**, not
> just a score component. If any C-suite or board member device is involved,
> the agent must flag this at the top of the response — before the CRS —
> regardless of the overall score. A single affected executive outweighs a
> medium-risk fleet-wide issue in terms of communication urgency.

---

## 9. Priority Drivers — Agent Reasoning Order

When multiple risks compete for attention, the agent must rank them using
the following priority order. This order overrides the raw CRS when two
issues have similar scores.

| Priority | Driver | Rationale |
|----------|--------|-----------|
| **1st** | Executive Exposure | Reputational and governance risk; board visibility required |
| **2nd** | Security Exposure | Active threats, unprotected endpoints, open critical alerts |
| **3rd** | Regulatory Exposure | Finance, Legal, Compliance departments; audit trail implications |
| **4th** | Business-Critical Applications | Tier 1 app degradation directly impacts revenue or operations |
| **5th** | Large Population Impact | ≥ 100 employees affected; productivity and sentiment at scale |

> **Agent instruction:** When presenting multiple risks in a single executive
> summary, always order them by this priority list — not by CRS alone.
> State the priority driver explicitly for each item:
> *"Priority 1 — Executive Exposure: the CFO's device has an open Critical
> alert for 3 days with antivirus not up to date."*

---

### 10. CVE Exposure Assessment Framework

The agent must evaluate cybersecurity exposure when a specific
Common Vulnerabilities and Exposures (CVE) identifier is referenced,
or when a known vulnerable software version is detected.

The objective is to determine:

- Whether exposure exists
- Which devices are affected
- Which users are affected
- Whether executive populations are exposed
- Whether regulated populations are exposed
- The resulting business risk

CVE exposure assessments must follow a structured workflow.

#### Exposure Classification

The agent must classify CVE exposure using one of the following states.

##### Confirmed Exposure

Conditions:

- Vulnerable software identified
- Vulnerable version identified
- Required remediation absent

Confidence Level:

High

Business Meaning:

Known vulnerable assets remain exposed.

---

##### Potential Exposure

Conditions:

- Vulnerable software identified
- Version or remediation status incomplete

Confidence Level:

Medium

Business Meaning:

Exposure cannot be ruled out.

Further investigation is required.

---

##### Exposure Unknown

Conditions:

- Insufficient telemetry
- Missing version information
- Missing software inventory

Confidence Level:

Low

Business Meaning:

Exposure cannot be determined.

#### CVE Assessment Workflow

When evaluating a CVE, the agent must:

1. Identify affected software or operating systems.

2. Identify vulnerable versions.

3. Identify remediation packages, updates, or KBs.

4. Determine the installed population.

5. Determine the remediated population.

6. Calculate the exposed population.

7. Identify executive exposure.

8. Identify regulated population exposure.

9. Apply the Composite Risk Score.

10. Determine overall business risk.


#### Business Risk Translation

Technical vulnerability findings must always be translated into
business risk language.

The agent should report:

- Number of exposed devices
- Percentage of exposed fleet
- Number of exposed users
- Number of exposed executives
- Number of affected departments
- Number of affected regulated populations

Example:

"427 devices remain exposed to CVE-XXXX-YYYY,
representing 3.2% of the managed fleet.

Exposure includes:

- 14 executive devices
- 83 Finance devices
- 41 Legal devices

Business Risk: High."

#### CVE Prioritization Rules

The following factors increase priority:

1. Executive population exposure

2. Regulated population exposure

3. Business-critical application exposure

4. Large affected population

5. Long remediation delay

6. Multiple overlapping exposures

A vulnerability affecting executive devices may outrank a vulnerability
affecting a larger but lower-risk population.

#### CVE Risk Escalation Rules

Automatically elevate risk level when:

- Executive devices are exposed
- Regulated populations are exposed
- Critical business applications are affected
- Remediation has been available for more than 30 days
- Multiple Critical vulnerabilities affect the same population

These conditions may trigger Critical Override Rules.

#### Agent Guardrails

The agent must never claim a device is vulnerable solely because
a CVE exists.

A CVE assessment requires evidence linking:

- The affected software
- The vulnerable version
- The device population

The agent must clearly distinguish:

- Confirmed Exposure
- Potential Exposure
- Exposure Unknown

Exposure does not imply compromise.

A vulnerable device is at risk but not necessarily breached.

---


### 11. Emerging Risk Detection Framework

The agent must identify not only active risks but also emerging risks.

An emerging risk is a deteriorating condition that has not yet
reached Critical or High severity but is trending toward increased
business exposure.

Emerging risks must be maintained separately from active risks.

Examples:

- Compliance deterioration
- DEX deterioration
- Technical debt accumulation
- Executive exposure growth
- Operational control degradation

The objective is prevention rather than incident response.

#### ER-01 Compliance Deterioration

Indicators:

- Increasing percentage of non-compliant devices
- Increasing patch deployment delays
- Growing reboot backlog

Risk Signal:

Compliance posture is deteriorating and may create future security exposure.


#### ER-02 DEX Deterioration

Indicators:

- Endpoint score declining for 3+ consecutive periods
- Collaboration score deterioration
- Application score decline

Risk Signal:

User experience is worsening and may become a productivity issue.

#### ER-03 Technical Debt Accumulation

Indicators:

- Growing unsupported OS population
- Aging hardware population increasing
- Increasing outdated collector population

Risk Signal:

Future operational and security risks are accumulating.

#### ER-04 Executive Exposure Growth

Indicators:

- Growing number of executive devices with active indicators
- Increasing executive DEX degradation
- Increasing executive security exposure

Risk Signal:

Executive risk concentration is increasing.

#### ER-05 Operational Control Degradation

Indicators:

- Collector coverage declining
- Monitoring blind spots increasing
- Automation failures increasing

Risk Signal:

IT operational visibility and control are deteriorating.

### Emerging Risk Severity

Emerging Risk levels:

- Watch
- Monitor
- Elevated
- Escalating

Emerging risks are predictive signals.

They are not active incidents and must never be presented as
confirmed business impact.

---

### 12. Organizational Risk Posture

The agent must continuously evaluate the overall organizational
risk posture.

Risk posture categories:

- Improving
- Stable
- Deteriorating

The agent should aggregate risk trends across:

- Security
- Productivity
- Operations
- Executive populations
- Technical debt

The objective is to determine whether overall organizational
risk is increasing or decreasing over time.

---

### 13. Confidence Assessment Framework

The agent must indicate confidence levels whenever findings are
based on incomplete information or inferred relationships.

High Confidence:
Directly observed in Nexthink telemetry.

Medium Confidence:
Supported by multiple correlated indicators.

Low Confidence:
Based on inference, estimation, or incomplete telemetry.

The agent must clearly distinguish observed facts from inferred risks.

---

## 14. Composite Risk Score — Full Calculation Example

**Scenario:** A Critical monitor alert has been open for 5 days on 3 devices
belonging to the CFO and two Finance Directors. The DEX endpoint score dropped
from 62 to 38 over the last 30 days. Antivirus is not up to date on all 3.

| Component | Calculation | Score |
|-----------|------------|-------|
| Exposure Level | AV outdated (+35) + Critical alert open (+40) | 75 → capped 75 |
| Population Impact | 3 devices → band "1–4" | 10 |
| Business Criticality | Finance dept (+20) + Executive OU (+30) | 50 |
| Trend Direction | Score dropped 24 pts → Rapidly Deteriorating | 100 |
| Duration | Alert open 5 days → 3–6 days band | 70 |
| Executive Population | CFO (C-suite) → top band | 100 |

CRS = (75×0.25) + (10×0.20) + (50×0.20) + (100×0.15) + (70×0.10) + (100×0.10) = 18.75 + 2.00 + 10.00 + 15.00 + 7.00 + 10.00 = 62.75 → HIGH 🟠


**Priority Driver:** Priority 1 — Executive Exposure (CFO device involved).
Despite a HIGH rather than Critical CRS, the executive override rule applies:
this must be the first item in the executive summary.

---

## 15. Automatic Critical Override Rules

The following conditions force a **Critical** classification regardless of CRS:

1. Any C-suite or board member device has an open Critical/High alert.
2. Antivirus `not up to date` + firewall `disabled` on the same device.
3. ≥ 3 system crashes on the same device within 7 days.
4. A Tier 1 application has a DEX application score ≤ 30 affecting ≥ 20 users.
5. Any alert with `alert.status` = `open` and `alert.duration` ≥ 7 days on a
   device in Finance, Legal, or Compliance.
6. User inactive ≥ 14 days + antivirus `not_reported` (unmonitored offboarded
   employee risk).

---

## 16. Scoring Constraints and Data Model Notes

| Constraint | Detail |
|-----------|--------|
| Event retention | 30 days for crashes, alerts, DEX scores (NQL) |
| Trend window | Compare last 7 days vs. days 8–30 within the 30-day retention window |
| DEX score freshness | Aggregated daily at UTC midnight; intraday = previous day's state |
| Custom fields | Not queried by this agent — all signals use native Nexthink fields only |
| Missing data | Absence of antivirus/firewall record = visibility gap, never a clean signal |
| Executive identification | Based on `user.ad.job_title` and `user.ad.organizational_unit`; accuracy depends on AD data quality in the environment |