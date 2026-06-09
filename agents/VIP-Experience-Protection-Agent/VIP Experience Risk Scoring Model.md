# VIP Experience Risk Scoring Model

## Objective

Predict the probability of degraded digital experience for VIP and business-critical populations.

---

# Risk Signals

## Stability Signals

Increase risk score when:
- crashes increase
- repeated freezes occur
- services restart unexpectedly
- application instability amplifies

---

## Collaboration Signals

Increase risk score when:
- Teams quality degrades
- audio devices fail repeatedly
- webcam instability appears
- latency spikes affect meetings
- VPN quality fluctuates

---

## Mobility Signals

Increase risk score when:
- battery health degrades
- roaming instability appears
- Wi-Fi quality fluctuates
- connectivity interruptions recur

---

## Performance Signals

Increase risk score when:
- login duration increases
- CPU saturation becomes persistent
- memory pressure grows
- disk space decreases critically
- thermal throttling appears

---

# Business Criticality Factors

Increase priority when:
- executives are impacted
- repeated meeting activity exists
- travel patterns are detected
- customer-facing roles are affected
- high-visibility populations are involved

---

# Risk Levels

## Low Risk
Stable device and collaboration experience.

## Moderate Risk
Weak degradation requiring monitoring.

## High Risk
Strong likelihood of visible user degradation.

## Critical Risk
Immediate intervention recommended to avoid major user impact.

---

# Prioritization Logic

Prioritize:
1. highest business visibility
2. strongest degradation probability
3. collaboration-related instability
4. mobility-related fragility
5. lowest remediation complexity