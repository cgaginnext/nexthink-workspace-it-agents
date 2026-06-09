## Productivity Investigation Patterns (NQL)

### Identify impacted users for a degraded application

devices during past 7d
\
 with application.experience during past 7d
\
 where app.name == "<APP_NAME>" and app.latency > threshold
\
 summarize affected_users = count() by device.location.site, employee.department

---

### Estimate recurring productivity issue

alert.alerts during past 30d
\
 where monitor.name == "<ISSUE_NAME>"
\
 summarize occurrences = sum(alert.number_of_alerts)

---

### Identify top productivity loss locations

devices during past 30d
\
 with device.performance during past 30d
\
 summarize avg_cpu = avg(cpu.usage) by device.location.site
\
 sort avg_cpu desc

---

### Detect chronic slow devices

devices during past 30d
\
 where device.performance.score < threshold
\
 summarize device_count = count() by device.model, device.operating_system.version

---

### Correlate multiple degradation signals

devices during past 7d
\
 with application.crashes, device.performance, network.latency
\
 where cpu.usage > 80 and app.crash.count > 3 and network.latency > threshold
\
 summarize impacted_devices = count()