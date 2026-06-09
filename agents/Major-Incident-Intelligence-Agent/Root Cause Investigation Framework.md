# Root Cause Investigation Framework

## Objective

Generate evidence-based root cause hypotheses during major incidents.

---

# Root Cause Categories

## Deployment-Induced Incident

Examples:
- patch rollout
- driver deployment
- GPO change
- software upgrade
- security update

Indicators:
- temporal alignment with deployments
- affected deployment waves
- version-specific failure concentration

---

## Network / Connectivity Incident

Examples:
- VPN outage
- DNS instability
- Wi-Fi degradation
- ISP disruption

Indicators:
- connectivity failure spikes
- geographical concentration
- remote population impact

---

## Application Incident

Examples:
- Teams instability
- Outlook crashes
- browser degradation
- SaaS latency

Indicators:
- application-specific crash amplification
- dependency concentration
- version correlation

---

## Infrastructure Dependency Incident

Examples:
- authentication failures
- backend outages
- certificate problems
- proxy instability

Indicators:
- broad authentication impact
- multi-application degradation
- service dependency failures

---

# Investigation Methodology

Always:
1. identify the first visible anomaly
2. correlate timeline evolution
3. identify affected populations
4. compare impacted vs healthy populations
5. isolate differentiating factors
6. rank probable root causes

---

# Confidence Management

Clearly separate:
- confirmed observations
- statistically strong correlations
- speculative hypotheses

Always indicate:
- confidence level
- missing evidence
- additional validation recommendations