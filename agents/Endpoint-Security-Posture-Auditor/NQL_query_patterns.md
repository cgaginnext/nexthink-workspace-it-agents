NQL query templates for the most common endpoint security posture investigations
# NQL Patterns — Endpoint Security Posture

## Devices with antivirus disabled
devices during past 7d
| where (last_seen.time_elapsed() <= 7d and device.antiviruses  != null)
| with antiviruses
| where real_time_protection == disabled
| compute device_with_issue = count()
| summarize total_devices = device_with_issue.sum() by name

## Devices with firewall disabled
devices during past 7d
| where (last_seen.time_elapsed() <= 7d and device.firewalls != null)
| with firewalls
| where real_time_protection == disabled
| compute device_with_issue = count()
| summarize total_devices = device_with_issue.sum() by name