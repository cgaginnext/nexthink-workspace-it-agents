# report-template-and-quality-standards.md

## Purpose
This file defines the mandatory structure, writing standards, and quality gates for all RCA reports produced by the DEX RCA agent. Every report must follow this template exactly. Sections marked **[REQUIRED]** must always be present. Sections marked **[CONDITIONAL]** are included only when the relevant evidence exists. Do not include a section if there is no supporting data.

---

## Report Template

---

### [REQUIRED] 1. Executive Summary

**Format:** 3–5 sentences maximum. No bullet points. Plain prose.

**Must contain:**
- The scope of the analysis (device, cohort, department, or organization)
- The analysis window (explicit dates, not relative expressions)
- The primary finding in one sentence
- The confidence level of the root cause attribution (Confirmed / Probable / Candidate / Insufficient evidence)

**Example:**
> This report covers the DEX score degradation observed on 47 devices in the Paris Finance department between 2026-06-01 and 2026-06-30. The Technology score dropped from 68 to 41 over this period, driven primarily by a sharp increase in application crashes affecting Microsoft 365: Outlook. Root cause is classified as Probable: a package update deployed on 2026-06-14 correlates strongly with crash onset, though a controlled rollback has not yet been performed to confirm causality.

---

### [REQUIRED] 2. Score Overview

**Format:** Structured table followed by 2–3 sentences of interpretation.

**Table structure:**

| Metric | Value at period start | Value at period end | Delta | Classification |
|---|---|---|---|---|
| DEX Score (overall) | | | | Good / Average / Frustrating |
| Technology Score | | | | |
| Endpoint subscore | | | | |
| Applications subscore | | | | |
| Collaboration subscore | | | | |
| Sentiment Score | | | | |

**Classification scale (mandatory):**
- Good: 71–100
- Average: 31–70
- Frustrating: 0–30

**Interpretation rules:**
- If Sentiment score is low but Technology score is normal, do not attribute the DEX drop to device or application issues. Flag as a Sentiment investigation.
- If all subscores are within 5 points of each other, the issue is systemic, not subscore-specific.
- Always state which subscore contributed most to the overall delta.

---

### [REQUIRED] 3. Scope and Population

**Format:** Bullet list.

**Must contain:**
- Number of affected devices
- Operating system distribution (Windows / macOS / Linux split)
- Location or department if scoped
- Collector version range (min and max observed)
- Any devices excluded from analysis and the reason (e.g., no data, Collector below minimum version)

---

### [REQUIRED] 4. Evidence Summary

**Format:** One sub-section per signal category investigated. Each sub-section contains a findings table and a 1–2 sentence interpretation.

**Signal categories to cover (include only those with data):**

#### 4a. Device Resource Utilization
| Metric | P50 | P95 | Threshold breached | Affected device count |
|---|---|---|---|---|
| CPU usage | | | Yes / No | |
| Memory usage | | | Yes / No | |
| Disk I/O latency | | | Yes / No | |
| System drive free space | | | Yes / No | |

#### 4b. Application Stability
| Application | Crash count | Crash rate (per device/day) | Freeze count | Severity |
|---|---|---|---|---|
| | | | | Isolated / Recurring / Severe |

#### 4c. Network Quality
| Metric | Value | Threshold breached | Peer comparison |
|---|---|---|---|
| Wi-Fi signal strength | | Yes / No | |
| TCP failed connection ratio | | Yes / No | |
| TCP establishment time | | Yes / No | |

#### 4d. Boot and Login Performance
| Metric | P50 | P95 | POOR events count |
|---|---|---|---|
| Boot duration | | | |
| Login desktop-visible time | | | |

#### 4e. Web Application Performance
| Application | Avg page load time | Avg transaction duration | Incomplete transaction ratio |
|---|---|---|---|
| | | | |

---

### [REQUIRED] 5. Root Cause Attribution

**Format:** Structured classification followed by a narrative paragraph.

**Classification framework:**

| Category | Definition | Required evidence |
|---|---|---|
| **Confirmed** | Causal link established with before/after data and at least one validation point | Score drop + event correlation + change event + metric normalization after change |
| **Probable** | Strong correlation with a plausible mechanism, no contradicting evidence | Score drop + event correlation + change event within 72h window |
| **Candidate** | Correlation present but mechanism unconfirmed or alternative causes not ruled out | Score drop + one correlated signal |
| **Coincidental** | Change event present but no metric correlation | Change event only, no signal degradation |
| **Insufficient evidence** | Data gaps prevent attribution | Missing tables, Collector below minimum version, retention window exceeded |

**Narrative paragraph requirements:**
- State the attributed cause in the first sentence
- Explain the mechanism (why this cause produces this effect)
- Quantify the impact (how many devices, how large a score drop, over what period)
- Acknowledge any alternative causes considered and why they were ruled out or ranked lower

---

### [CONDITIONAL] 6. Contributing Factors

Include this section only when secondary signals are present that did not cause the primary drop but amplified it or affected a subset of devices.

**Format:** Bullet list. One bullet per contributing factor. Each bullet must state:
- The factor
- The affected scope (device count or percentage)
- Why it is classified as contributing rather than causal

---

### [CONDITIONAL] 7. Change Event Correlation

Include this section only when package installs, uninstalls, or OS updates are identified within the correlation window.

**Format:** Table followed by classification.

| Timestamp | Package name | Version | Operation | Source | Time delta to onset | Classification |
|---|---|---|---|---|---|---|
| | | | Install / Uninstall | | hours | Causal / Contributory / Coincidental / Candidate |

**Classification rules** (from `change-correlation-and-update-impact-methodology.md`):
- Causal: change within 72h of onset + metric degradation + no prior degradation
- Contributory: change present + degradation present + other causes also present
- Coincidental: change present + no metric degradation
- Candidate: change present + degradation present + insufficient data to confirm mechanism

---

### [CONDITIONAL] 8. Collector Version Limitations

Include this section only when one or more devices have a Collector version below the minimum required for a signal used in the analysis.

**Format:** Bullet list.

**Must state:**
- Which signal was unavailable or degraded
- The minimum Collector version required
- The version range observed on affected devices
- What fallback was used (e.g., legacy 15-minute freeze aggregates)
- Whether the limitation affects the confidence of the root cause attribution

---

### [REQUIRED] 9. Recommended Actions

**Format:** Numbered list, ordered by priority (highest impact first).

**Each action must include:**
1. **Action:** What to do (specific, not generic)
2. **Target:** Which devices or users (scope)
3. **Rationale:** Why this action addresses the identified cause
4. **Expected outcome:** What metric should improve and by how much (estimate)
5. **Validation method:** How to confirm the action worked (re-query which table, after how many days)

**Quality gate:** Do not recommend an action that is not directly supported by evidence in Section 4 or Section 5. Generic recommendations ("update all software", "restart devices") are not acceptable unless they are the specific remediation for the identified cause.

---

### [REQUIRED] 10. Data Quality and Confidence Statement

**Format:** Short paragraph (3–5 sentences).

**Must address:**
- Completeness of data (were all relevant tables available and within retention?)
- Any gaps that could affect the conclusion
- Overall confidence in the root cause attribution (repeat the classification from Section 5)
- What additional data collection or action would increase confidence

---

## Writing Standards

### Language and tone
- Write in clear, professional English (or the language of the requesting user)
- Use precise technical terms; do not substitute vague language for specificity
- Avoid hedging language unless genuine uncertainty exists; state findings plainly
- Do not use filler phrases ("it is worth noting that", "as mentioned above", "it should be highlighted")

### Numbers and units
- Always include units: ms, GB, %, dBm
- Use P50 and P95 consistently; do not use "average" when a percentile is available
- Express time windows as explicit dates, never as "recently" or "last week"
- Round to two significant figures for percentages; one decimal place for latency values

### Confidence calibration
- Do not claim Confirmed status without before/after metric data and a validation point
- Do not claim Probable status without a change event within the 72-hour window
- When data is missing, state Insufficient Evidence — do not fill gaps with assumptions
- A single correlated signal is never sufficient for Confirmed or Probable; it is a Candidate

### Prohibited patterns
- Do not generalize from a single device to the fleet without population-level data
- Do not attribute a Sentiment score drop to device or application causes
- Do not recommend hardware replacement without sustained multi-day evidence and supporting signals (e.g., SMART errors, repeated P95 spikes)
- Do not include a section with no supporting data — omit it entirely
- Do not use relative time expressions in the report body (yesterday, last week, recently)

---

## Quality Gates — Pre-Delivery Checklist

Before delivering any RCA report, verify all of the following:

| Gate | Check |
|---|---|
| All REQUIRED sections present | ✅ / ❌ |
| Score table uses correct 0–100 scale with Good/Average/Frustrating bands | ✅ / ❌ |
| Root cause classification uses the five-tier framework | ✅ / ❌ |
| Every recommended action has a validation method | ✅ / ❌ |
| No relative time expressions in the report body | ✅ / ❌ |
| No section included without supporting data | ✅ / ❌ |
| Collector version limitations documented if applicable | ✅ / ❌ |
| Confidence statement present and consistent with evidence | ✅ / ❌ |
| Units present on all numeric values | ✅ / ❌ |
| Sentiment score not attributed to device/application causes | ✅ / ❌ |

A report that fails any gate must be revised before delivery.

---

*End of file 5 — report-template-and-quality-standards.md*

---

Les cinq fichiers sont maintenant produits. Récapitulatif final :

| # | Fichier | Statut |
|---|---|---|
| 1 | `dex-score-structure-and-data-model.md` | ✅ |
| 2 | `platform-constraints-and-data-availability.md` | ✅ |
| 3 | `change-correlation-and-update-impact-methodology.md` | ✅ |
| 4 | `device-and-application-performance-analysis.md` | ✅ |
| 5 | `report-template-and-quality-standards.md` | ✅ |