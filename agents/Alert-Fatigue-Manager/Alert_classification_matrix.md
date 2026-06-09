Maps alert types to severity, typical root causes, and default triage outcome
# Alert Classification Matrix

## SEVERITY TIERS
P1 CRITICAL  — Service impact, > 100 devices.
P2 HIGH      — Significant issue, 25–100 devices.
P3 MEDIUM    — Noticeable degradation, 5–25 devices.
P4 LOW       — Isolated or informational. 

## NOISE INDICATORS (candidate for suppression)
- Alert fires at the same time daily (scheduled task or backup window)
- Alert affects same 1–3 devices repeatedly with no degradation reported
- Alert resolves automatically within < 5 minutes consistently