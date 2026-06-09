# SaaS Experience Correlation Model

## Objective

Determine the most probable causes of SaaS degradation by correlating endpoint, network, and application telemetry.

---

# Correlation Signals

## Endpoint Signals

Increase endpoint-side probability when:
- CPU usage spikes
- memory pressure becomes persistent
- browser instability increases
- devices overheat
- disk latency increases
- crashes amplify

---

## Connectivity Signals

Increase connectivity probability when:
- VPN reconnects spike
- Wi-Fi quality fluctuates
- packet loss increases
- latency becomes unstable
- DNS failures appear

---

## Collaboration Signals

Increase collaboration degradation probability when:
- Teams call quality deteriorates
- webcam instability appears
- audio interruptions recur
- conferencing freezes amplify

---

## Browser Signals

Increase browser-related probability when:
- rendering slows
- browser crashes increase
- extension conflicts emerge
- memory saturation affects sessions

---

# Root Cause Categories

## Endpoint-Induced
Device instability or resource saturation causes degradation.

## Connectivity-Induced
VPN, Wi-Fi, ISP, or network instability impacts user experience.

## Browser-Induced
Browser instability or extension conflicts degrade performance.

## Application-Induced
Application-specific degradation observed consistently.

## Mixed Environment
Multiple contributing degradation factors identified.

---

# Confidence Levels

## Low Confidence
Limited correlation evidence.

## Medium Confidence
Several aligned degradation indicators.

## High Confidence
Strong multi-dimensional telemetry correlation.

---

# Prioritization Logic

Prioritize:
1. broadest user impact
2. collaboration-critical degradation
3. recurring instability
4. highest productivity impact
5. easiest remediation opportunities