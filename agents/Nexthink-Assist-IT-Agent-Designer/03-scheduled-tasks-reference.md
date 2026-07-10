# 03 — Scheduled Tasks Reference

Reference guide for Nexthink Assist scheduled tasks: cadences disponibles,
règles de rédaction des instructions autonomes, cas d'usage par fréquence,
contraintes de rétention à respecter, et exemples d'instructions prêtes à
l'emploi.

---

## 1. What is a scheduled task in Nexthink Assist?

A scheduled task is a saved Workspace instruction that runs automatically on a
defined cadence, without human intervention at execution time. The task
executes the instruction as if a user had typed it, retrieves data, and
produces a response that is stored in the conversation history.

Scheduled tasks are the primary mechanism for:
- Recurring monitoring (daily health checks, weekly DEX summaries).
- Proactive alerting (flag conditions that exceed a threshold before a user
  notices).
- Periodic reporting (generate a structured summary on a fixed schedule for
  distribution or review).

A scheduled task is not a workflow, a monitor, or a remote action. It does not
trigger remediation, send notifications to employees, or modify device state.
It produces a Workspace response — a text output the user reviews.

---

## 2. Available cadences

Three schedule types are supported:

| Type       | Configuration                          | Minimum interval |
|------------|----------------------------------------|------------------|
| `daily`    | Runs at a fixed time on selected days  | Once per day     |
| `interval` | Runs every N hours                     | 1 hour           |
| `once`     | Runs once at a specified datetime      | N/A              |

### Cadence selection rules

- Use `interval` for operational monitoring that needs sub-daily frequency
  (e.g., every 4 hours for connectivity issue detection).
- Use `daily` for reporting and trend analysis where daily granularity is
  sufficient (e.g., every morning at 08:00 for a DEX summary).
- Use `once` for a deferred analysis or a one-time report at a specific
  future datetime.
- Do not use `interval` with a 1-hour cadence for tasks that query 7-day or
  30-day trend data — the data does not change at that frequency and the
  executions are wasteful. Match cadence to data freshness.

### Day selection for `daily` tasks

A `daily` task can be restricted to specific days of the week. Common patterns:

| Pattern                  | Days                          | Use case                          |
|--------------------------|-------------------------------|-----------------------------------|
| Weekdays only            | mon, tue, wed, thu, fri       | Operational monitoring            |
| Weekly summary           | mon (or fri)                  | Weekly DEX or incident report     |
| Every day                | (all days)                    | Critical health checks            |
| Mid-week check           | wed                           | Mid-sprint or mid-week review     |

---

## 3. The four rules for scheduled task instructions

A scheduled task instruction is a fully autonomous prompt. It runs without a
user present to clarify, correct, or redirect. These four rules are mandatory.

### Rule 1 — No pronouns, no implicit context
Every entity must be named or scoped explicitly. The task has no conversation
history to draw from at execution time.

❌ `Summarize what happened on my devices today.`
✅ `Summarize notable events (crashes, high CPU, connectivity failures) across
all managed Windows and macOS devices in the last 24 hours.`

❌ `Check if Teams is still having issues.`
✅ `Identify devices where Microsoft Teams reported audio or video quality
issues in the last 24 hours. Show device count, affected locations, and
the most common issue type.`

### Rule 2 — Relative time expressions only
Fixed dates become stale the moment the task runs for the second time. Always
use relative expressions anchored to the execution time.

❌ `Analyze DEX scores from 2026-07-01 to 2026-07-07.`
✅ `Analyze DEX scores for the last 7 days.`

❌ `List crashes recorded on 2026-07-06.`
✅ `List application crashes recorded in the last 24 hours.`

Relative expressions to use: "last 24 hours", "past 7 days", "last 30 days",
"past 4 hours", "this week". Avoid "today" and "yesterday" — they are
ambiguous when the task runs at a non-midnight time.

### Rule 3 — Include an explicit null-result instruction
When no data matches the query, a task without a null-result instruction
produces an ambiguous or empty response. The user cannot tell whether the task
ran correctly or found nothing.

❌ (no null-result instruction)
✅ `If no devices match the criteria, state explicitly: "No devices exceeded
the threshold in the last 24 hours."`

This is especially important for threshold-based monitoring tasks. A clean
"nothing found" result is a meaningful signal.

### Rule 4 — Specify the output format
A scheduled task that produces an unstructured narrative is harder to scan
quickly. Specify the format that matches the consumption pattern.

- **Table** — for comparisons across multiple entities (device name,
  department, metric value).
- **Ranked list** — for top-N results reviewed quickly.
- **Structured summary** — for executive-level reports with a key findings
  section followed by supporting detail.
- **Flag + count** — for threshold monitoring: "X devices exceeded the
  threshold. List them."

---

## 4. Data retention constraints by cadence

The instruction must query data that actually exists at execution time. Querying
a window longer than the retention period produces empty or partial results
without an error message — a silent failure.

| Data type                        | Retention          | Safe query window        |
|----------------------------------|--------------------|--------------------------|
| Inventory objects (devices,      | 30 days from last  | Up to 30 days            |
| users, packages, binaries)       | activity           |                          |
| Operational events               | 30 days            | Up to 30 days            |
| (crashes, performance, boots,    |                    |                          |
| connectivity, executions)        |                    |                          |
| execution.events,                | 14 days            | Up to 14 days            |
| connection.events (high res.)    |                    |                          |
| Software metering trends         | 90 days            | Up to 90 days            |
| Web application trends           | 90 days            | Up to 90 days            |
| Remote action execution summary  | 13 months          | Up to 13 months          |
| Workflow execution summary       | 13 months          | Up to 13 months          |
| Custom trends                    | 13 months          | Up to 13 months          |
| DEX trends                       | 13 months          | Up to 13 months          |
| Campaign responses               | Indefinite         | Any window               |

**Practical rules:**
- For daily tasks querying operational events, use "last 24 hours" or "last
  7 days" — never "last 60 days".
- For weekly summary tasks, "last 7 days" is always safe for operational
  events.
- For trend-based tasks (DEX scores, software metering, remote action
  summaries), windows up to 90 days or 13 months are valid.
- DEX trends and web application trends are not queryable via NQL — they are
  only available in the Digital Experience and Applications module dashboards.
  Do not write scheduled task instructions that attempt to query these via NQL.

---

## 5. Cadence-to-use-case mapping

### Every 1–4 hours (interval)
Best for: operational monitoring where early detection matters.
Data scope: last 1–4 hours of operational events.

Appropriate use cases:
- Connectivity failure spike detection (TCP failures, Wi-Fi drops).
- Application crash surge detection.
- Active alert count monitoring.

Avoid for: DEX score analysis, software inventory, trend reporting — these
data sets do not change at hourly frequency.

### Daily — weekdays, morning (e.g., 08:00 mon–fri)
Best for: start-of-day operational briefing.
Data scope: last 24 hours of operational events.

Appropriate use cases:
- Daily device health summary (CPU, memory, disk pressure).
- Overnight crash and freeze report.
- New active alerts since yesterday.
- Devices that went offline and did not reconnect.

### Daily — Monday morning (weekly summary)
Best for: weekly trend review and planning input.
Data scope: last 7 days of operational events or trend data.

Appropriate use cases:
- Weekly DEX score summary by subscore and department.
- Top 5 applications by crash count over the last 7 days.
- Software metering summary: applications with zero usage in the last 7 days.
- Remote action execution summary: success/failure rates over the last 7 days.

### Monthly (first Monday of the month, or day 1)
Best for: strategic reporting and license/compliance reviews.
Data scope: last 30 days of operational events, or trend data up to 90 days.

Appropriate use cases:
- Monthly DEX score trend with month-over-month comparison.
- Software license utilization: installed vs. actively used applications.
- Device fleet health overview: OS versions, hardware age, disk health.
- Campaign response summary for the last 30 days.

---

## 6. Instruction templates by use case

The following are production-ready instruction templates. Each follows the
four rules. Replace `<placeholders>` before saving.

---

### T-01 — Daily device health briefing
**Cadence:** Daily, 08:00, weekdays
**Data scope:** Last 24 hours

```
Provide a daily health briefing for the managed device fleet covering the
last 24 hours.

Include:
1. Count of devices that experienced high CPU usage (> 90% sustained for
   more than 15 minutes). List the top 5 by duration.
2. Count of devices with available disk space below 10 GB. List them with
   device name, department, and free space value.
3. Count of application crashes across all devices. List the top 3
   applications by crash count.
4. Count of new active alerts triggered in the last 24 hours, grouped by
   severity (critical, high, medium).

Present results as a structured summary with one section per topic.
If no devices match a criterion, state explicitly that no issues were
detected for that category.
```

---

### T-02 — Weekly DEX score summary
**Cadence:** Daily, 08:00, Monday only
**Data scope:** Last 7 days

```
Generate a weekly DEX score summary for the last 7 days.

Include:
1. Organization-wide DEX score: overall value and breakdown by subscore
   (Endpoint, Applications, Collaboration, Sentiment).
2. Week-over-week change for each subscore (compare last 7 days to the
   7 days before that).
3. The subscore with the largest decline: name it, quantify the drop, and
   list the top 3 contributing event types.
4. Departments or locations with a DEX score below 40 (Frustrating range):
   list them with their score and the dominant low subscore.

Present as a structured summary. If no department or location is below 40,
state that explicitly.
```

---

### T-03 — Application crash surge detection
**Cadence:** Interval, every 4 hours
**Data scope:** Last 4 hours

```
Check for application crash surges in the last 4 hours.

Flag any application where the crash count in the last 4 hours exceeds 10.
For each flagged application, show:
- Application name
- Total crash count in the last 4 hours
- Number of affected devices
- Number of affected users

If no application exceeds the threshold, state explicitly:
"No application crash surges detected in the last 4 hours."

Present results as a table if any applications are flagged.
```

---

### T-04 — Weekly software license utilization
**Cadence:** Daily, 07:00, Monday only
**Data scope:** Last 30 days (software metering)

```
Generate a weekly software license utilization report for the last 30 days.

For the top 20 most installed applications:
1. Show the number of devices with the application installed.
2. Show the number of devices where the application was actively used at
   least once in the last 30 days.
3. Calculate the utilization rate (used / installed).
4. Flag any application with a utilization rate below 20%.

Sort by utilization rate ascending (lowest first).
Present as a table with columns: Application, Installed (devices), Used
(devices), Utilization rate, Flag.

If fewer than 20 applications are found, report all available.
```

---

### T-05 — Connectivity failure monitoring
**Cadence:** Interval, every 2 hours
**Data scope:** Last 2 hours

```
Monitor for connectivity failures in the last 2 hours.

Report:
1. Count of devices that experienced TCP connection failures with a failure
   rate above 5%. List the top 5 by failure rate with device name, location,
   and failure rate.
2. Count of devices with Wi-Fi signal strength below -75 dBm. List the top 5
   with device name, location, and signal strength value.

If no devices exceed either threshold, state explicitly:
"No connectivity issues detected in the last 2 hours."

Present each category as a separate section.
```

---

### T-06 — Monthly DEX trend report
**Cadence:** Daily, 07:00, first Monday of the month (or day 1)
**Data scope:** Last 30 days

```
Generate a monthly DEX trend report for the last 30 days.

Include:
1. Organization-wide DEX score trend: weekly averages for each of the last
   4 weeks, broken down by subscore.
2. Top 3 departments with the lowest average DEX score over the period.
   For each, show the dominant low subscore and the primary contributing
   event type.
3. Top 3 departments with the highest DEX score improvement over the period
   (comparing week 1 to week 4).
4. Count of devices that remained in the Frustrating range (score 0–30)
   for the entire 30-day period.

Present as a structured report with one section per topic.
```

---

## 7. Instruction review checklist

Before saving a scheduled task, verify:

- [ ] No pronouns or implicit context references ("my", "this", "the user").
- [ ] All entity references are explicit names or scope filters.
- [ ] Time scope uses relative expressions only (no fixed dates).
- [ ] The time window does not exceed the retention period for the queried
      data type (see section 4).
- [ ] A null-result instruction is present ("If no results, state…").
- [ ] The output format is specified.
- [ ] The cadence matches the data freshness (no hourly tasks on 30-day
      trend data).
- [ ] The task has exactly one intent (not a compound multi-topic
      instruction — split those into separate tasks).

---

## 8. What scheduled tasks cannot do

Scheduled tasks produce Workspace responses. They do not:

- Send notifications or emails to users or IT staff.
- Trigger remote actions or workflows.
- Create or update alerts, monitors, or campaigns.
- Modify any platform configuration.
- Access data outside the Nexthink platform.

If the goal is automated remediation (not just reporting), use a Nexthink
Workflow with a schedule trigger instead of a scheduled task.

---

*This file is a knowledge source for the Nexthink Assist Agent Designer agent.
It does not contain live data and does not reference any specific customer
environment.*
```

---