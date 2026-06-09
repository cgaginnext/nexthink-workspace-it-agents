Widget types, decision guide, dashboard type rules, and configuration gotchas
# Live Dashboard Widget & Configuration Reference

## Dashboard Types

| Type        | Data retention | Timeframe range      | Use case                        |
|-------------|---------------|----------------------|---------------------------------|
| Operational | Up to 30 days  | Last 1h to Last 30d  | Monitoring, troubleshooting      |
| Reporting   | Up to 13 months| Last 30d to Last year| Trends, quarterly/annual reports |

Both types can coexist in one dashboard.
Reporting widgets > 30d MUST use Custom Trends as data source.

## Widget Decision Guide

GOAL                                      -> WIDGET
Show a single number (count, %, avg)      -> KPI
Show a trend over time                    -> Line Chart
Compare values across categories          -> Bar Chart
Show ratio of affected vs total           -> Single-Metric Gauge
Show distribution across 2+ categories   -> Multi-Metric Gauge
Show a list with multiple fields          -> Table
Add a section label                       -> Heading
Let users filter by a property            -> Filter Widget
Control time range for all widgets        -> Timeframe Picker

## Widget Configuration Fields

### All widgets
- Chart Type: select from dropdown
- NQL query: powers the data
- Title / Description: optional labels
- 'Data updates with Global timepicker': enable for time-sensitive data
- 'Data updates with Global filters': enable to apply dashboard-level filters
  -> Disable BOTH for: fixed inventory queries, threshold metrics, KPIs with
     hardcoded context (e.g. 'total managed devices')

### Line Chart only
- Granularity: 15min / hourly / daily / weekly / monthly
- Controlled by the timeframe picker at runtime
- NQL MUST include: | summarize metric by <granularity> | list end_time, metric

### Bar Chart only
- Optional: default breakdown field (pre-set segmentation)
- Users can click bars to apply contextual page filters

### Gauge (single-metric)
- Requires: affected_count and total in the same summarize
- Renders as percentage = affected / total

### Gauge (multi-metric)
- Requires: 2+ named fields in summarize (e.g. good, at_risk, bad)
- Each field becomes a segment in the gauge

### Table
- Best for: device lists, user lists, detailed event breakdowns
- Use | project to select exactly which fields to display
- Supports drill-down to Device View or Investigate

## Filters
- Added in Edit Mode: 'Add filter widget' (top-right)
- Supported types: string, enumerator, boolean, version
- Apply at dashboard level — affect ALL tabs
- Multi-value selection: configurable per filter
- Max 8 tabs per dashboard

## Key Gotchas
- Deleting a tab (when other tabs exist) removes ALL its widgets — no undo
- Timeframe picker and filters apply across ALL tabs simultaneously
- Only the active tab's NQL runs at any time (performance safe)
- Reporting dashboards: filter options limited to device properties and metrics
- Sharing a URL includes: current timeframe, active filters, active tab
- Max custom timeframe: 395 days

## Permissions Reference
Set via: Administration -> Roles -> Live Dashboards
Full View Domain  : can manage all dashboards (create, edit, delete)
Limited View Domain: view only — cannot edit shared dashboards
