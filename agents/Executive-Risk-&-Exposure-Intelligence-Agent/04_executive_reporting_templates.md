# Executive Reporting Templates — Executive Risk & Exposure Intelligence Agent

---

## ⚙️ Customization Manifest

| Ref | Section | Placeholder | Required / Optional | Impact if missing |
|-----|---------|-------------|--------------------|--------------------|
| CFG-R01 | All templates | `ORGANIZATION_NAME` | Optional | Agent uses "the organization" as fallback |
| CFG-R02 | All templates | `FLEET_TOTAL_DEVICES` | **Required** | Agent cannot compute exposure percentages without total fleet size |
| CFG-R03 | T1, T2 | `REPORTING_PERIOD` | Optional | Default: last 7 days |
| CFG-R04 | T3 | `CISO_NAME` / `CIO_NAME` | Optional | Agent uses role title as fallback |
| CFG-R05 | T1 | `CRITICAL_ALERT_SLA_HOURS` | Optional | Default: 24 hours |
| CFG-R06 | T2 | `PEER_BENCHMARK_SOURCE` | Optional | If absent, no benchmark comparison is included |

### Configuration Status

```
CFG-R01 ORGANIZATION_NAME:        [ ] Not configured  [ ] Configured: ____________
CFG-R02 FLEET_TOTAL_DEVICES:      [ ] Not configured  [ ] Configured: ____________
CFG-R03 REPORTING_PERIOD:         [ ] Not configured  [ ] Using default: 7 days
CFG-R04 CISO/CIO NAME:            [ ] Not configured  [ ] Using role title fallback
CFG-R05 CRITICAL_ALERT_SLA_HOURS: [ ] Not configured  [ ] Using default: 24h
CFG-R06 PEER_BENCHMARK_SOURCE:    [ ] Not configured  [ ] Benchmark section omitted
```

---

## Purpose

This document defines the structured output templates the agent uses
when producing executive-facing reports. Templates are not cosmetic —
they enforce a consistent reasoning order that moves from headline
risk to evidence to action, which is the only sequence that works
for a non-technical executive audience.

Each template has a defined trigger condition, a mandatory structure,
field-by-field population rules, and explicit agent instructions for
handling missing data, zero-count results, and ambiguous findings.

---

## Reporting Principles

Before any template, the agent must internalize these four rules:

**1. Lead with impact, not with data.**
An executive does not need to know that `device.antiviruses.is_up_to_date`
= `no` on 47 devices. They need to know that 47 devices have no active
threat protection and what that means for the organization. Translate
every technical finding into a business consequence before presenting it.

**2. Percentages anchor context; absolute numbers anchor urgency.**
Always provide both. "3% of the fleet" sounds manageable. "47 devices"
sounds concrete. "47 devices — 3% of the fleet, including 2 executive
endpoints" sounds urgent. Use all three.

**3. Trend direction matters more than point-in-time state.**
A score of 45 improving from 30 is a different story from a score of
45 declining from 65. Always include direction when `score.time` data
is available over the reporting period.

**4. Every finding must close with an action.**
An executive report that ends with a problem statement and no action
is a liability document, not a management tool. Every section must
close with: who does what, by when, and what the expected outcome is.

---

## Template Index

| ID | Template | Trigger | Audience |
|----|----------|---------|----------|
| T1 | **Weekly Security & Fleet Risk Summary** | Scheduled weekly | CISO / CIO / IT Leadership |
| T2 | **DEX Trend & Population Health Briefing** | Scheduled weekly or on-demand | CIO / HR / Business Leaders |
| T3 | **Critical Incident Executive Alert** | Triggered: Critical pattern detected | CISO / CIO (immediate) |
| T4 | **Executive Device Risk Profile** | On-demand: single executive device | CISO / IT Support Lead |
| T5 | **Regulatory Exposure Summary** | On-demand or quarterly | CISO / Legal / Compliance |

---

## T1 — Weekly Security & Fleet Risk Summary

### Trigger Condition
Scheduled weekly, or when the agent detects ≥ 1 new Critical security
pattern (S1–S5 from `02_security_exposure_models.md`) since the last report.

### Audience
CISO, CIO, IT Leadership. Assumes familiarity with IT concepts but
not with Nexthink-specific terminology.

### Data Sources
`device.devices`, `device.antiviruses`, `device.firewalls`,
`device_performance.system_crashes`, `device_performance.hard_resets`,
`alert.alerts`, `alert.impacts`, `dex.scores`, `execution.crashes`,
`package.installed_packages`, `user.users`

---

### T1 Structure

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[ORGANIZATION_NAME] — IT Risk & Fleet Health Summary
Week of [DATE_RANGE] | Generated [TIMESTAMP]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Executive Summary
[2–3 sentences. State the single most important finding, its
business impact, and whether the situation is improving or
deteriorating. No bullet points here — prose only.]

---

## 🔴 Critical Findings  [omit section if count = 0]

### [Pattern or Indicator Label]
- Affected: [N] devices ([X]% of fleet)
- Since: [first detected date]
- Status: [New / Ongoing / Escalating / Recovering]
- Business impact: [1 sentence — what this means operationally]
- Action required: [specific action, owner role, deadline]

[Repeat block for each Critical finding, ordered by Priority Driver:
Executive (P1) → Security (P2) → Regulatory (P3)]

---

## 🟠 High-Priority Findings  [omit section if count = 0]

[Same block structure as Critical, max 5 items.
If > 5 High findings exist, list top 5 by affected device count
and note: "X additional High findings available on request."]

---

## 📊 Fleet Health Snapshot

| Metric | This Week | Last Week | Trend |
|--------|-----------|-----------|-------|
| Devices with outdated AV | [N] ([X]%) | [N] ([X]%) | [↑↓→] |
| Devices with firewall disabled | [N] ([X]%) | [N] ([X]%) | [↑↓→] |
| Devices with OS update > 30 days | [N] ([X]%) | [N] ([X]%) | [↑↓→] |
| System crashes (fleet total) | [N] | [N] | [↑↓→] |
| Hard resets (fleet total) | [N] | [N] | [↑↓→] |
| Open Critical alerts | [N] | [N] | [↑↓→] |
| Avg DEX Technology score | [X]/100 | [X]/100 | [↑↓→] |

Trend key: ↑ Worsening · ↓ Improving · → Stable (±2%)

---

## 🟢 Resolved This Week  [omit section if count = 0]

- [Finding label]: resolved on [date] — [N] devices remediated
[Repeat for each resolved item]

---

## Recommended Actions This Week

| Priority | Action | Owner | Deadline | Expected Outcome |
|----------|--------|-------|----------|-----------------|
| P1 | [action] | [role] | [date] | [outcome] |
[Max 5 rows. Order by Priority Driver.]

---

## Appendix: Methodology Note
Findings are derived from Nexthink endpoint telemetry across
[FLEET_TOTAL_DEVICES] managed devices. Security indicators follow
the definitions in the Security Exposure Models document.
DEX scores use the standard scale: Good 71–100 · Average 31–70 ·
Frustrating 0–30.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### T1 Population Rules

| Field | How to populate |
|-------|----------------|
| Executive Summary | Write last, after all sections are complete. Summarize the single highest-priority finding and its trend direction. |
| Critical Findings count | Count distinct devices affected by any Pattern S1–S5 or any standalone Critical indicator. |
| "Since" date | Use `alert.alerts.time` for alert-based findings; `system_crash.time` or `hard_reset.time` for stability findings. |
| Status: Escalating | Use when affected device count increased > 10% week-over-week. |
| Status: Recovering | Use when affected device count decreased > 10% week-over-week AND at least one remediation action was executed. |
| Trend arrows | ↑ = metric worsened by > 2% · ↓ = improved by > 2% · → = within ±2% |
| "Available on request" | When > 5 High findings exist, do not truncate silently — always state the count of omitted items. |

### T1 Agent Instructions

> **Zero Critical findings:** Do not omit the report. Replace the
> Critical section with: *"No Critical findings detected this week.
> Fleet security posture is within acceptable parameters."* Then
> proceed to High findings and Fleet Health Snapshot as normal.
>
> **Missing last-week data:** If prior-week data is unavailable
> (e.g., first run, or data retention gap), populate Last Week
> column with "—" and note: *"Prior-week baseline not available
> for this metric."* Never fabricate a trend.
>
> **CFG-R02 missing (no fleet total):** Do not compute percentages.
> Report absolute numbers only and append: *"Fleet total not
> configured — percentage of fleet cannot be calculated."*

---

## T2 — DEX Trend & Population Health Briefing

### Trigger Condition
Scheduled weekly, or when `score.value` (fleet average) drops > 5
points week-over-week, or when any population segment drops into
the Frustrating band (≤ 30).

### Audience
CIO, HR leadership, Business unit leaders. May have no IT background.
Avoid all technical field names in the output.

### Data Sources
`dex.scores` (with `score.time` for trend), `device.devices`
(`device.location.type`, `device.location.site`, `device.location.country`),
`user.users` (`user.ad.department`), `alert.impacts`

---

### T2 Structure

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[ORGANIZATION_NAME] — Digital Employee Experience Briefing
[DATE_RANGE] | Generated [TIMESTAMP]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## What This Report Measures
This briefing summarizes how well employees' digital tools are
working across the organization. Scores range from 0 to 100:
· 71–100 (Good): tools are working well
· 31–70 (Average): some friction — employees may be experiencing
  slowdowns or reliability issues
· 0–30 (Frustrating): significant issues — employees are likely
  experiencing daily disruption

---

## Overall Score

| | This Period | Last Period | Change |
|--|-------------|-------------|--------|
| **Overall DEX Score** | [X]/100 | [X]/100 | [+/-X] [↑↓→] |
| Technology Score | [X]/100 | [X]/100 | [+/-X] [↑↓→] |
| — Device Performance | [X]/100 | [X]/100 | [+/-X] |
| — Application Quality | [X]/100 | [X]/100 | [+/-X] |
| — Collaboration Tools | [X]/100 | [X]/100 | [+/-X] |
| Sentiment Score | [X]/100 | [X]/100 | [+/-X] [↑↓→] |

[1–2 sentence interpretation of the overall score and its direction.
Plain language — no field names.]

---

## Populations Needing Attention  [omit if none in Average or below]

[List any department, location, or cohort with score ≤ 50,
ordered by score ascending (worst first).]

| Population | Segment type | Score | Primary issue | Affected employees |
|------------|-------------|-------|--------------|-------------------|
| [name] | [Dept / Site / Remote] | [X]/100 | [1 phrase] | [N] |

[For each population in Frustrating band (≤ 30), add:]
> ⚠️ **[Population name]** is in the Frustrating band. Employees
> here are likely experiencing daily disruption to their work.
> Immediate investigation is recommended.

---

## What Is Driving the Score

[Identify the 1–2 subscores with the largest negative impact.
Translate into plain language. Examples:]

- *"Device startup times are the main drag on the Technology score
  — employees are waiting an average of [X] seconds for their
  computers to be ready."*
- *"Collaboration tool quality has declined, likely linked to
  connectivity issues for remote employees."*
- *"Application reliability improved this week — fewer crashes
  were recorded across the fleet."*

[Do not use field names. Do not use dBm, milliseconds, or
technical thresholds in this section.]

---

## Trend: Last 4 Weeks

| Week | Overall DEX | Technology | Sentiment |
|------|-------------|------------|-----------|
| [W-3] | [X] | [X] | [X] |
| [W-2] | [X] | [X] | [X] |
| [W-1] | [X] | [X] | [X] |
| [This week] | [X] | [X] | [X] |

[1 sentence on trend direction. Flag if any score crossed a
band boundary (e.g., moved from Average to Frustrating).]

---

## Recommended Actions

| Action | Who | Expected impact |
|--------|-----|----------------|
| [plain-language action] | [role] | [plain-language outcome] |
[Max 3 rows. Prioritize actions with the highest score-impact
potential. No technical jargon.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### T2 Population Rules

| Field | How to populate |
|-------|----------------|
| Population segments | Segment by `device.location.site`, `device.location.type` (remote vs onsite), and `user.ad.department`. Report any segment with ≥ 10 devices and score ≤ 50. |
| "Primary issue" | Use the highest `score.endpoint.*_score_impact` value for that population to identify the dominant driver. Translate to plain language (e.g., "slow startup" not "boot_speed_score_impact"). |
| 4-week trend | Query `dex.scores` with `score.time` over the past 28 days, grouped by week. If fewer than 4 weeks of data exist, populate available weeks and note the gap. |
| Sentiment score | `score.sentiment.value` is campaign-derived. If no campaign has run in the period, state: *"Sentiment data is not available for this period — no survey was conducted."* Never impute a sentiment score from technology data. |

### T2 Agent Instructions

> **Non-technical audience rule:** This template is the only one
> where field names, dBm values, millisecond thresholds, and
> Nexthink-specific terminology are strictly forbidden in the
> output. Translate everything. If you cannot translate a finding
> into plain language, omit it from T2 and include it in T1 instead.
>
> **Score band crossing:** If any score crossed from Average to
> Frustrating (or Frustrating to Average) during the period, call
> it out explicitly in the trend section. Band crossings are the
> most meaningful signal for a non-technical audience.
>
> **Sentiment vs Technology divergence:** If Sentiment score is
> significantly lower than Technology score (gap > 20 points),
> note: *"Employees are reporting a worse experience than the
> technical metrics suggest. This may indicate issues not captured
> by device telemetry — such as software usability, process
> friction, or unmet expectations."*

---

## T3 — Critical Incident Executive Alert

### Trigger Condition
Immediately when any of the following is detected:
- Pattern S1, S3, or S4 from `02_security_exposure_models.md`
- Pattern H-S1 (hardware failure signature) affecting > 5 devices
- Any open Critical alert exceeding `CRITICAL_ALERT_SLA_HOURS`
  without a recorded remediation action
- F3 (Privileged User Risk) on any C-suite device

### Audience
CISO and/or CIO. Immediate notification. Assumes full technical
literacy. Brevity is more important than completeness here —
the goal is to trigger a decision, not to provide a full analysis.

### Data Sources
Relevant to the specific trigger — see individual pattern definitions
in `02_security_exposure_models.md` and `03_fleet_health_risk_signals.md`.

---

### T3 Structure

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ CRITICAL IT RISK ALERT — [ORGANIZATION_NAME]
[TIMESTAMP] | Requires immediate attention
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## What Was Detected
[Pattern label] — [1 sentence plain-language description]

## Scope
- Affected devices: [N] ([X]% of fleet)
- Affected users: [N]
- Executive population involved: [Yes — [N] executives / No]
- First detected: [TIMESTAMP]
- Duration: [X hours / days]

## Why This Is Critical
[2–3 sentences. State the specific risk — not the indicator.
What can an attacker do? What data is exposed? What is the
operational impact if this is not resolved in the next 24 hours?]

## Evidence
- [Indicator label]: [value] on [N] devices
- [Indicator label]: [value] on [N] devices
[Max 4 bullet points. Most severe first.]

## Immediate Actions Required

1. [Action] — Owner: [role] — By: [time]
2. [Action] — Owner: [role] — By: [time]
3. [Action] — Owner: [role] — By: [time]

## Escalation Path
If not resolved within [CRITICAL_ALERT_SLA_HOURS] hours:
→ Escalate to: [CISO_NAME / CIO_NAME / role fallback]
→ Incident ticket: [create via standard ITSM process]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### T3 Agent Instructions

> **Length discipline:** T3 must fit on a single screen. If the
> evidence section requires more than 4 bullets, select the 4 most
> severe and note: *"Full indicator list available in the detailed
> investigation."* Never expand T3 into a full analysis — that is
> T1's role.
>
> **Executive involvement:** If `user.ad.job_title` matches the
> executive population definition and the device is affected, always
> set "Executive population involved: Yes" and name the count.
> Never name individual executives in the alert body — use count
> and role only (e.g., "2 C-suite devices affected").
>
> **Duplicate suppression:** Do not re-issue T3 for the same
> pattern on the same device population within 24 hours unless
> the scope has materially expanded (> 20% more devices affected).
> Repeated alerts for the same unresolved issue become noise.

---

## T4 — Executive Device Risk Profile

### Trigger Condition
On-demand. Triggered when a specific executive device or user is
named, or when F3 (Privileged User Risk) is detected and a
per-device deep-dive is requested.

### Audience
CISO, IT Support Lead, or the executive's dedicated IT support
contact. Full technical detail — this is an operational document,
not a communication document.

### Data Sources
All device-level tables: `device.devices`, `device.antiviruses`,
`device.firewalls`, `device_performance.system_crashes`,
`device_performance.hard_resets`, `device_performance.boots`,
`device_performance.events`, `session.logins`, `execution.crashes`,
`execution.events`, `connectivity.events`, `connection.events`,
`package.installed_packages`, `alert.alerts`, `dex.scores`

---

### T4 Structure

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Executive Device Risk Profile
Device: [device.name] | User: [last_login_user_name]
Role: [user.ad.job_title] | Dept: [user.ad.department]
OS: [device.operating_system.name] | Model: [device.hardware.model]
Location: [device.location.type] / [device.location.site]
Last seen: [device.last_seen] | Report date: [TIMESTAMP]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Risk Summary
Overall risk level: [🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low]
Active patterns: [list Pattern IDs, e.g., S3, H-S1]
Active indicators: [count] Critical · [count] High · [count] Medium

---

## Security Exposure

| Indicator | Status | Severity | Since |
|-----------|--------|----------|-------|
| Antivirus up to date | [✅ Yes / ❌ No / ⚠️ Not reported] | [sev] | [date] |
| AV real-time protection | [✅ Enabled / ❌ Disabled] | [sev] | [date] |
| Firewall protection | [✅ Enabled / ❌ Disabled / ⚠️ Partial] | [sev] | [date] |
| UAC status | [✅ OK / ❌ At risk] | [sev] | — |
| OS last update | [N] days ago | [sev] | [date] |
| Last reboot | [N] days ago | [sev] | [date] |
| Open alerts | [N] open / [N] Critical | [sev] | [oldest date] |

---

## Fleet Health

| Signal | Value | Severity | Period |
|--------|-------|----------|--------|
| System crashes | [N] | [sev] | Last 7 days |
| Hard resets | [N] | [sev] | Last 7 days |
| App crashes | [N] (top: [binary.product_name]) | [sev] | Last 7 days |
| Last boot duration | [X] s | [sev] | Last event |
| Login desktop visible | [X] s | [sev] | Last event |
| CPU high-usage time | [X] min/day avg | [sev] | Last 7 days |
| Memory pressure time | [X] min/day avg | [sev] | Last 7 days |
| System drive free | [X] GB | [sev] | Current |
| Wi-Fi signal (avg) | [X] dBm | [sev] | Last 7 days |

---

## DEX Score

| Score | Value | Band |
|-------|-------|------|
| Overall DEX | [X]/100 | [Good/Average/Frustrating] |
| Technology | [X]/100 | [band] |
| — Endpoint | [X]/100 | [band] |
| — Applications | [X]/100 | [band] |
| — Collaboration | [X]/100 | [band] |
| Sentiment | [X]/100 | [band] |

Top score impact: [highest score.endpoint.*_score_impact field,
translated to plain language]

---

## Recommended Actions

| Priority | Action | Nexthink capability | Estimated effort |
|----------|--------|--------------------|--------------------|
| [P1–P3] | [action] | [Remote Action / Workflow / Campaign] | [Low/Med/High] |
[Order by severity. Max 5 rows.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### T4 Agent Instructions

> **Privacy rule:** T4 contains device-level data associated with
> a named individual. The agent must never include T4 output in a
> T1 or T2 report. T4 is a point-in-time operational document for
> the IT support team — not for distribution to business leadership.
>
> **Missing signals:** If a field cannot be populated (e.g., no
> connectivity events in the period, or no DEX score recorded),
> populate with "No data — [reason]" rather than leaving blank or
> assuming a clean state.
>
> **Severity column:** Use the thresholds defined in
> `02_security_exposure_models.md` and `03_fleet_health_risk_signals.md`.
> Do not invent new thresholds for T4.

---

## T5 — Regulatory Exposure Summary

### Trigger Condition
On-demand, or quarterly. Triggered when a compliance review is
requested, or when indicators with regulatory tags (GDPR, ISO27001,
SOC2, HIPAA) are active on devices in regulated departments.

### Audience
CISO, Legal, Compliance. Assumes familiarity with regulatory
frameworks but not with Nexthink telemetry.

### Data Sources
`device.devices`, `device.antiviruses`, `device.firewalls`,
`user.users` (`user.ad.department`), `device_performance.system_crashes`,
`package.installed_packages`, `alert.alerts`, `dex.scores`

---

### T5 Structure

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[ORGANIZATION_NAME] — Regulatory Exposure Summary
Period: [DATE_RANGE] | Generated [TIMESTAMP]
Scope: Devices associated with regulated departments
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Scope of This Report
This report covers [N] devices associated with employees in
regulated functions: [list departments from CFG-08].
It identifies active indicators that are relevant to
[GDPR / ISO27001 / SOC2 / HIPAA — list applicable frameworks].

---

## Exposure by Regulatory Framework

### GDPR
| Indicator | Affected devices | % of regulated fleet | Status |
|-----------|-----------------|---------------------|--------|
| Unencrypted mobile devices (G3) | [N] | [X]% | [Active/Resolved] |
| Dormant accounts ≥ 90 days (F1) | [N] | [X]% | [Active/Resolved] |
| Outdated AV on regulated devices (A1) | [N] | [X]% | [Active/Resolved] |
| Shadow IT in regulated depts (G1) | [N] | [X]% | [Active/Resolved] |

### ISO 27001
[Same table structure, filtered to ISO27001-tagged indicators]

### SOC 2
[Same table structure, filtered to SOC2-tagged indicators]

### HIPAA  [omit if not applicable]
[Same table structure, filtered to HIPAA-tagged indicators]

---

## Highest-Risk Regulated Departments

| Department | Active Critical indicators | Active High indicators | Avg DEX score |
|------------|--------------------------|----------------------|---------------|
| [name] | [N] | [N] | [X]/100 |
[Order by Critical count descending. Max 5 rows.]

---

## Audit Readiness Assessment

| Control area | Status | Evidence available | Gap |
|-------------|--------|-------------------|-----|
| Endpoint protection (AV + Firewall) | [✅ Compliant / ⚠️ Partial / ❌ Gap] | Nexthink telemetry | [describe gap if any] |
| OS patch currency | [✅ / ⚠️ / ❌] | Nexthink telemetry | [gap] |
| Account lifecycle (dormant accounts) | [✅ / ⚠️ / ❌] | Nexthink + IAM required | [gap] |
| Disk encryption (mobile) | [✅ / ⚠️ / ❌] | Nexthink telemetry | [gap] |
| Disk encryption (Windows) | [⚠️ Partial] | Remote Action required | BitLocker status not natively available |
| Unauthorized software | [✅ / ⚠️ / ❌] | Nexthink + approved list required | [gap] |

---

## Recommended Actions for Compliance

| Action | Framework | Owner | Deadline | Evidence produced |
|--------|-----------|-------|----------|------------------|
| [action] | [GDPR/ISO/SOC2] | [role] | [date] | [what audit evidence this generates] |
[Max 5 rows. Prioritize actions that close the most framework gaps.]

---

## Data Limitations for Audit Purposes

The following gaps exist in Nexthink's coverage that are relevant
to audit evidence:

- **Windows BitLocker status** is not available as a native field.
  Evidence requires execution of the BitLocker Remote Action.
- **Active Directory account status** (enabled/disabled) is not
  queryable. Nexthink confirms endpoint activity; directory state
  requires an IAM system query.
- **CVE-specific vulnerability mapping** requires a maintained
  VULNERABLE_PACKAGES list (CFG-02). Without it, patch currency
  is assessed by update recency, not by specific CVE exposure.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### T5 Agent Instructions

> **Framework applicability:** Only include framework sections
> (GDPR, SOC2, HIPAA) that are relevant to the organization.
> If no HIPAA-tagged indicators are active and the organization
> is not in a HIPAA-regulated industry, omit the HIPAA section
> entirely. Do not include empty framework tables.
>
> **Audit readiness language:** Use "Compliant", "Partial", or
> "Gap" — never "Pass" or "Fail". Nexthink telemetry provides
> evidence of control