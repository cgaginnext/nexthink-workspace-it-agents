# Deployment Risk Scoring Model

## Objective

Calculate a deployment risk score for each device.

The score should estimate:
- deployment failure probability
- post-deployment instability likelihood
- operational support risk

---

# Risk Signals

## Stability Indicators

Increase risk score if:
- frequent application crashes
- system crashes
- repeated service failures
- abnormal reboot patterns
- excessive login duration

---

## Resource Indicators

Increase risk score if:
- low disk space
- sustained CPU saturation
- sustained memory usage
- storage performance degradation

---

## Reliability Indicators

Increase risk score if:
- VPN instability
- collector disconnections
- failed remote actions
- repeated software installation failures

---

## Compatibility Indicators

Increase risk score if:
- unsupported OS versions
- outdated drivers
- old collector versions
- incompatible software dependencies
- vulnerable legacy applications

---

# Risk Categories

## Low Risk
Minimal instability indicators.

## Moderate Risk
Some instability indicators requiring monitoring.

## High Risk
Strong likelihood of deployment issues.

## Critical Risk
Deployment should be postponed or excluded.

---

# Recommendations Per Risk Level

## Low Risk
Proceed normally.

## Moderate Risk
Deploy in controlled batches.

## High Risk
Remediate before deployment.

## Critical Risk
Exclude from deployment until stabilization.