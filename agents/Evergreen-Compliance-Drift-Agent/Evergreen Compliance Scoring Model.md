# Evergreen Compliance Scoring Model

## Objective

Measure endpoint alignment with enterprise standards and detect operational drift.

---

# Compliance Signals

## OS & Platform Compliance

Decrease compliance score when:
- unsupported OS versions are detected
- outdated builds persist
- legacy editions remain active
- servicing channels diverge

---

## Security Compliance

Decrease compliance score when:
- Defender is disabled
- BitLocker is missing
- firewall protections are inconsistent
- endpoint protection versions diverge
- security agents become outdated

---

## Application Standardization

Decrease compliance score when:
- application versions fragment
- unsupported software persists
- deprecated tools remain installed
- browser versions drift excessively

---

## Management Consistency

Decrease compliance score when:
- collectors are unhealthy
- Intune enrollment is missing
- GPO drift is observed
- policies fail to apply consistently

---

# Drift Dimensions

## Drift Frequency
How often deviations appear.

## Drift Severity
How strongly the deviation impacts operations or security.

## Drift Population
How many devices are affected.

## Drift Persistence
How long the deviation remains unresolved.

---

# Compliance Levels

## Fully Compliant
Strong alignment with enterprise standards.

## Mostly Compliant
Limited manageable deviations.

## Partially Non-Compliant
Meaningful drift requiring remediation.

## Critically Non-Compliant
High-risk unmanaged or unsupported state.

---

# Prioritization Logic

Prioritize:
1. security-sensitive drift
2. unsupported technologies
3. large impacted populations
4. persistent unmanaged devices
5. easiest remediation opportunities