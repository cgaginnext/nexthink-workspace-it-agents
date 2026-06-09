Structured triage for common Live Dashboard problems — data, config, permissions, performance
# Live Dashboard Troubleshooting Guide

## TRIAGE CHECKLIST
For every issue, ask:
1. Is it affecting one widget or the whole dashboard?
2. Is the issue data-related (wrong values) or display-related (not rendering)?
3. Did it work before? If so, what changed? (new filter, tab, NQL edit, update)
4. Is it for one user or multiple users?

---

## PROBLEM: Widget shows no data

Cause A — NQL returns no results for the selected timeframe
Fix: Expand timeframe. Check if events exist via Investigations.
     Run NQL directly in Investigations to confirm data presence.

Cause B — 'Data updates with Global timepicker' is ON but timeframe is too narrow
Fix: Widen the timeframe picker, or disable the global timepicker for this widget
     and hardcode a relative time range in the NQL (e.g. during past 30d).

Cause C — Global filter is excluding all results
Fix: Clear all dashboard-level filters. If data returns, the filter is too restrictive.
     Deselect 'Data updates with Global filters' for this widget if needed.

Cause D — NQL syntax error or references wrong table
Fix: Copy NQL into Investigations and run it. Check for error messages.
     Correct entity name, field name, or aggregation function.

---

## PROBLEM: Line chart not rendering / shows flat line

Cause A — Missing 'by <granularity>' in summarize
Fix: Add: | summarize metric = count() by 1d

Cause B — Missing '| list end_time, metric'
Fix: Add the list clause after summarize. Both end_time and metric must be listed.

Cause C — Granularity too fine for the selected timeframe
Fix: Use 1d granularity for timeframes > 7d. Use 1h for timeframes < 48h.

Cause D — Single data point only (range = granularity bucket)
Fix: Expand the timeframe so multiple granularity buckets are covered.

---

## PROBLEM: Gauge shows 0% or 100% unexpectedly

Cause A — Affected count and total computed incorrectly
Fix: Ensure both come from same summarize. Affected = device.count().sum(),
     Total = count() at device level (not event level).

Cause B — Missing | include clause
Fix: Start with 'devices | include <event_table>' to join event data to device scope.

Cause C — Timeframe mismatch between include and overall query
Fix: Add 'during past Xd' on the include line: | include <table> during past 7d

---

## PROBLEM: Filters not applying to a widget

Cause — 'Data updates with Global filters' is disabled for that widget
Fix: Edit widget -> enable 'Data updates with Global filters'.
Note: Intentionally disable for fixed-context KPIs (e.g. total device count).

---

## PROBLEM: Reporting widget shows no data beyond 30 days

Cause — Raw event tables don't retain data beyond 30 days
Fix: Create a Custom Trend in Administration -> Content management -> Custom trends.
     Reference the custom trend in the widget NQL instead of the raw event table.
     Allow 24h after trend creation for data to populate.

---

## PROBLEM: User cannot see or edit the dashboard

Cause A — Dashboard is Private (not shared)
Fix: Dashboard owner must go to Manage live dashboards -> Share the dashboard.

Cause B — User has Limited View Domain role
Fix: Limited View Domain users can view all shared dashboards but cannot edit.
     To grant edit rights: Administration -> Roles -> assign Full View Domain.

Cause C — User is on wrong tenant or environment
Fix: Confirm URL matches the correct Nexthink Infinity instance.

---

## PROBLEM: Dashboard is slow to load

Cause A — Too many tabs with heavy NQL running simultaneously
Fix: Only the active tab runs. Move heavy widgets to separate tabs.
     Consider splitting into multiple dashboards.

Cause B — NQL scans too broad a timeframe or too many records
Fix: Add | top N to limit output. Narrow timeframe. Add | where filters early.
     Use | summarize before | project to reduce data volume.

Cause C — Custom trend not yet populated
Fix: Trends populate on a schedule. Wait 24h after creation.
     Check Administration -> Custom trends for status.

---

## PROBLEM: Deleting a tab removed all its widgets

This is expected behaviour — no undo available.
Prevention: Before deleting a tab, move all widgets to other tabs.
Recovery: Recreate the widgets manually. Store NQL queries in resource files
          to speed up recreation.