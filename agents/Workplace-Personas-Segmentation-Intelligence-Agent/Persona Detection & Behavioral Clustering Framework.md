# Persona Detection & Behavioral Clustering Framework

## Objective

Identify operational workplace personas based on observed digital behaviors and endpoint telemetry.

---

# Detection Signals

## Mobility Signals

Analyze:
- VPN usage frequency
- roaming behavior
- geographic movement
- Wi-Fi switching
- battery dependency
- laptop docking usage
- remote connectivity duration

Typical personas:
- Field workers
- Hybrid users
- Fully remote users
- Highly mobile executives

---

## Collaboration Signals

Analyze:
- Teams call intensity
- webcam usage
- audio device dependency
- meeting duration
- collaboration hours
- conferencing recurrence

Typical personas:
- Collaboration-heavy users
- Management populations
- Call-center workers
- External-facing employees

---

## Application Usage Signals

Analyze:
- application launch frequency
- concurrent application count
- browser intensity
- SaaS dependency
- CPU/GPU-intensive workloads
- VDI usage

Typical personas:
- Developers
- Creative/GPU users
- Light office users
- VDI-reliant populations

---

## Behavioral Stability Signals

Analyze:
- support dependency
- frequent remediations
- repeated friction
- crash exposure
- low digital confidence indicators

Typical personas:
- Fragile IT users
- Self-sufficient users
- High-support populations

---

# Persona Confidence Scoring

## High Confidence
Strong recurring and stable behavior patterns.

## Medium Confidence
Behavioral convergence observed with moderate consistency.

## Low Confidence
Partial or inconsistent telemetry visibility.

---

# Segmentation Principles

Always:
- prioritize operational usefulness
- keep personas explainable
- avoid excessive granularity
- focus on actionable differences

---

# Expected Outputs

Provide:
- persona name
- behavioral profile
- population size
- dominant technologies
- operational sensitivities
- likely optimization opportunities
``