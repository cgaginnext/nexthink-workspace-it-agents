# PC Slowness Investigation Patterns

## Purpose

This file defines reusable investigation patterns for analyzing PC slowness across a managed device estate.

The objective is to help the IT Agent move from broad symptoms to specific and actionable root cause hypotheses.

The original investigation model includes patterns to identify impacted users, recurring issues, top loss locations, chronic slow devices, and correlation between several degradation signals.

---

## Investigation pattern 1 — Identify impacted devices in scope

### Question

Which devices in the defined scope show signs of slowness?

### Filters

- Device name starts with the agreed pattern
- Exclude servers
- Include active Windows devices
- Analyze the selected period
- Separate working hours from non-working hours if possible

### Signals to check

- Device performance score
- CPU usage
- CPU Queue Length
- Memory usage
- Memory Swap Rate
- Disk Queue Length
- Available memory
- Disk usage
- Free disk space
- Boot duration
- Logon duration
- Reboot age
- User complaints or sentiment if available

### Output

- Number of devices analyzed
- Number of degraded devices
- Percentage of impacted devices
- Top impacted models
- Top impacted locations
- Top impacted user groups

---

## Investigation pattern 2 — Detect chronic slow devices

### Question

Which devices are degraded repeatedly over the period?

### Signals to check

- Repeated poor performance score
- Repeated CPU saturation
- Repeated memory pressure
- Repeated long boot or logon time
- Repeated high CPU Queue Length
- Repeated disk pressure
- Recurring user-visible slowness

### Classification

- Chronic: degraded on several days
- Intermittent: degraded during repeated episodes
- Isolated: degraded once or rarely

### Output

- Top 10 chronic devices
- Recurrence count
- Main recurring symptom
- Likely technical cause
- Recommended action

---

## Investigation pattern 3 — Identify quick wins

### Question

Which devices can be improved with low-effort operational actions?

### Quick win indicators

- Low disk space
- Pending reboot
- Very high reboot age
- Update pending or update loop
- OneDrive sync backlog
- Excessive startup applications
- Known outdated driver or firmware
- High browser cache or profile bloat
- Security scan running during active hours
- Endpoint management client consuming abnormal resources
- Old hardware model with insufficient memory

### Output

For each device:

- Quick win category
- Evidence
- Recommended action
- Expected benefit
- Effort level
- Risk level
- Need for user confirmation

---

## Investigation pattern 4 — Analyze startup slowness

### Question

Are users mainly impacted when starting the PC?

### Signals to check

- Boot duration
- Logon duration
- Startup application activity
- Security agent activity at startup
- Endpoint management activity at startup
- Windows Update activity
- Disk saturation after boot
- CPU Queue Length after boot
- Time until device becomes usable

### Common hypotheses

- Too many startup applications
- Security tools scanning at startup
- Endpoint management tasks running at logon
- Pending updates
- Slow disk or aging hardware
- User profile or OneDrive initialization issue

### Output

- Devices with repeated startup slowness
- Main startup contributors
- Actions to reduce startup load
- Expected user benefit

---

## Investigation pattern 5 — Analyze slowness after lunch or idle periods

### Question

Do devices become slow after users return from lunch or after long idle periods?

### Signals to check

- Resume from sleep duration
- CPU activity immediately after idle
- Security scans after idle
- OneDrive resynchronization
- Outlook synchronization
- Teams activity
- VPN or Zscaler reconnection
- Network change
- Windows maintenance tasks

### Common hypotheses

- Background scans resume after idle
- OneDrive or Outlook resync after inactivity
- Network security tunnel reconnects slowly
- Device wakes with resource contention
- Scheduled tasks trigger during idle or resume

### Output

- Devices affected after idle
- Repeated timing pattern
- Main resource consumers
- Recommended action

---

## Investigation pattern 6 — Analyze slowness after location or network change

### Question

Does the slowness occur after the user changes location or network?

### Signals to check

- Network change events
- VPN or Zscaler reconnection
- Wi-Fi signal or adapter changes
- DNS or proxy changes
- Application reconnect behavior
- OneDrive resync
- Teams reconnect
- Browser latency
- Authentication or conditional access delays

### Common hypotheses

- Network tunnel reconnection
- Proxy or security stack reinitialization
- Cloud application resynchronization
- Wi-Fi or driver issue
- Location-specific network degradation

### Output

- Devices affected by mobility-related slowness
- Locations or networks involved
- Recurring pattern
- Actions to confirm or remediate

---

## Investigation pattern 7 — Correlate multiple degradation signals

### Question

Which devices cumulate several causes of slowness?

### Signals to correlate

- High CPU usage
- High CPU Queue Length
- Memory pressure
- Low disk space
- Long boot duration
- High reboot age
- Pending updates
- OneDrive sync issues
- Security tool CPU usage
- Management tool CPU usage
- User complaints
- Application crashes

### Output

- Devices with multiple concurrent issues
- Strongest root cause hypothesis
- Quick wins available
- Whether issue is likely structural
- Recommended treatment order

The reference productivity pattern recommends correlating multiple weak signals to build a single productivity narrative, instead of treating each signal independently.

---

## Investigation pattern 8 — Compare degraded and healthy devices

### Question

What is different between degraded devices and healthy devices?

### Compare by

- Model
- CPU usage
- Memory usage
- CPU Queue Length
- Available memory
- Memory Swap Rate
- Disk space
- Disk Queue Length
- Boot time
- Security tool consumption
- Endpoint management tool consumption
- User application consumption
- Operating system version
- Location
- Department

### Output

- Number of degraded devices
- Number of healthy devices
- Differences by category
- Most meaningful gaps
- Structural causes to investigate
- Recommended estate-level actions