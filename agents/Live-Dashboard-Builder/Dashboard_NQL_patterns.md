Copy-paste NQL templates for every Live Dashboard widget type
# Live Dashboard NQL Patterns

## RULE: Never hardcode absolute timestamps.
## Always use: during past Xd OR  during past Xh

---

## KPI Widget — single scalar metric

# Count of events in last 7 days
<event_table> during past 7d
| summarize total = count()

# Average value
<event_table> during past 7d
| summarize avg_value = <field>.avg()

# Count of unique devices affected
<event_table> during past 7d
| summarize affected_devices = device.count()

---

## Line Chart Widget — trend over time
# REQUIRED: summarize by granularity + list end_time

# Single metric trend
<event_table> during past 7d
| summarize total = count() by 1d
| list end_time, total

# Multi-metric trend
<event_table> during past 7d
| summarize metric1 = <field1>.avg(), metric2 = <field2>.avg() by 1d
| list end_time, metric1, metric2

# Device count trend by day
<event_table> during past 30d
| summarize affected = device.count() by 1d
| list end_time, affected

---

## Bar Chart Widget — comparison by category

# Count by segmentation field
<event_table> during past 7d
| summarize count_ = count() by <segmentation_field>
| sort count_ desc

# Top 10 devices by event count
<event_table> during past 7d
| summarize events = count() by device.name
| sort events desc
| top 10

# Distribution by site
<event_table> during past 7d
| summarize count_ = count() by context.organization.entity
| sort count_ desc

---

## Single-Metric Gauge — ratio of affected vs total

# Devices with a condition vs all devices
devices
| include <event_table> during past 7d
| compute affected = device.count()
| summarize affected = affected.sum(), total = count()

# Example: devices with crashes vs total
devices
| include execution.crashes during past 7d
| compute crash_count = device.count()
| summarize with_crashes = crash_count.sum(), total = count()

---

## Multi-Metric Gauge — distribution across categories

# Good / At Risk / Bad distribution
devices
| include <event_table> during past 7d
| compute issue_count = device.count()
| summarize
    no_issue   = count() - issue_count.sum(),
    with_issue = issue_count.sum()

# DEX Score gauge
devices
| include dex.scores during past 24h
| compute dex_score_per_devices = value.avg()
| summarize dex_score = dex_score_per_devices.avg(), total = 100

---

## Table Widget — detailed list

# Device list with key fields
devices during past 7d
| list device.name, device.entity, device.hardware.model, device.hardware.type, device.operating_system.name

---

## Custom Trends (Reporting — data > 30 days)
# Define the trend in: Administration -> Content management -> Custom trends
# Use the trend as data source in widget NQL

# Example widget NQL using a custom trend
custom.trends.<trend_name>
| summarize total = count() by 1d
| list end_time, total
