# Deployment Readiness Analysis Prompts

## Prompt 1 — Fleet Deployment Readiness Overview

Analyze fleet readiness for an upcoming deployment over the last 30 days.

Evaluate deployment readiness using:
- device stability
- crash frequency
- disk space
- CPU usage
- memory pressure
- reboot frequency
- VPN reliability
- collector health
- historical deployment failures
- uptime stability

Classify devices into:
- Ready
- Ready With Monitoring
- At Risk
- Exclude From Deployment

Provide:
- total device counts per category
- readiness percentages
- impacted entities/sites
- top hardware models at risk
- principal readiness blockers

Highlight:
- low disk populations
- unstable hardware models
- frequently crashing devices
- unsupported OS versions
- unhealthy collectors

Produce:
- executive summary
- operational remediation recommendations
- quick wins
- deployment exclusion recommendations