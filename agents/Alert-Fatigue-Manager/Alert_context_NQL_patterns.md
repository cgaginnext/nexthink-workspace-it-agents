NQL queries to gather scope and context for the most common alert types
# NQL Patterns — Alert Context Investigation

## Scope an alert: how many devices affected right now?
devices during past 7d
| with alert.impacts during past 7d
| where monitor.name == "<ALERT_NAME>"
| summarize count_ = count() by device.location.site, device.operating_system.platform

## Alert recurrence: how many times has this fired in 30 days?
alert.alerts during past 30d
| where monitor.name == "<ALERT_NAME>"
| summarize total_alerts = alert.number_of_alerts.sum()

## Noisiest monitor
alert.alerts during past 30d
| summarize total_alerts = count() by monitor.name, monitor.priority
| sort total_alerts desc

## top impacted location by alerts
alert.impacts during past 30d
| summarize impacted_device_count = device.count() by context.location.country
| sort impacted_device_count desc