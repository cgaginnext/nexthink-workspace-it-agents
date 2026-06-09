# Incident Anomaly Correlation Model

## Objective

Detect major incidents by correlating multiple degradation signals across the fleet.

---

# Correlation Signals

## Endpoint Stability Signals

Increase incident probability when observing:
- crash amplification
- repeated service failures
- application freezes
- abnormal reboot spikes
- sudden instability waves

---

## Performance Signals

Increase incident probability when:
- login duration spikes
- boot times degrade suddenly
- widespread CPU saturation occurs
- memory pressure increases abnormally
- browser responsiveness drops

---

## Connectivity Signals

Increase incident probability when:
- VPN failures spike
- Wi-Fi quality degrades broadly
- DNS failures increase
- packet loss concentration appears
- Teams media quality drops suddenly

---

## Operational Signals

Increase incident probability when:
- support activity spikes
- remote action failures surge
- deployment failures amplify
- authentication prompts increase
- collector instability increases

---

# Correlation Dimensions

## Temporal Correlation

Determine whether:
- degradations start simultaneously
- anomalies amplify together
- signals appear after deployments

---

## Population Correlation

Analyze whether:
- specific entities are impacted
- hardware models concentrate issues
- specific OS versions correlate
- remote users are more impacted
- VPN-dependent populations degrade more

---

## Technical Correlation

Correlate:
- deployments
- drivers
- application versions
- policies
- agent versions
- infrastructure dependencies

---

# Incident Confidence Levels

## Low Confidence
Weak isolated anomaly signals.

## Medium Confidence
Multiple correlated degradations observed.

## High Confidence
Strong multi-dimensional correlation with expanding blast radius.

## Critical Confidence
Massive operational disruption confirmed across populations.