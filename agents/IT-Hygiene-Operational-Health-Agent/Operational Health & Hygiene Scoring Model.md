# Operational Health & Hygiene Scoring Model

## Objective

Measure operational hygiene maturity and detect hidden technical debt.

---

# Hygiene Signals

## Reboot & Uptime Signals

Decrease hygiene score when:
- uptime becomes excessive
- pending reboot states persist
- reboot frequency becomes abnormally low

---

## Stability Signals

Decrease score when:
- crashes recur frequently
- services fail repeatedly
- remediation retries increase
- collectors become unstable

---

## Performance Hygiene Signals

Decrease score when:
- startup duration grows
- background process count explodes
- cache growth becomes excessive
- disk saturation persists

---

## Maintenance Signals

Decrease score when:
- pending updates accumulate
- cleanup never occurs
- temporary files grow continuously
- remediation history repeats constantly

---

# Hygiene Dimensions

## Stability
General endpoint reliability.

## Maintainability
Ease of operational maintenance.

## Performance Cleanliness
Absence of accumulated degradation.

## Operational Debt
Accumulated unresolved maintenance issues.

## Automation Readiness
Suitability for proactive self-healing.

---

# Hygiene Levels

## Excellent Hygiene
Stable and proactively maintained environment.

## Acceptable Hygiene
Limited operational debt.

## Degraded Hygiene
Recurring maintenance and instability concerns.

## Critical Hygiene Debt
Severe accumulated degradation generating operational instability.

---

# Prioritization Logic

Prioritize:
1. recurring unresolved degradation
2. support-heavy environments
3. hidden technical debt
4. high-friction populations
5. safest automation opportunities