# Deployment Blocker Detection

## Objective

Identify devices that should be excluded from deployments.

---

# Hard Blockers

Devices should be classified as deployment blockers if they present:

- critically low disk space
- unsupported operating systems
- collector instability
- repeated deployment failures
- corrupted update services
- broken VPN access
- critical service failures
- severe boot instability
- missing dependencies
- encryption or security policy conflicts

---

# Soft Blockers

Devices requiring caution:
- aging hardware
- elevated CPU usage
- moderate crash history
- unstable Wi-Fi quality
- low battery health
- sporadic connectivity issues

---

# Required Outputs

Produce:
- blocker category
- impacted population size
- impacted business entities
- impacted hardware models
- blocker severity
- recommended remediation

---

# Prioritization Logic

Prioritize blockers based on:
1. deployment failure probability
2. number of affected devices
3. expected user impact
4. remediation complexity
5. operational urgency