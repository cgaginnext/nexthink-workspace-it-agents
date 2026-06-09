# Major Incident Detection Prompts

## Prompt — Fleet-Wide Incident Detection

Analyze endpoint telemetry over the last 24 hours to detect emerging or ongoing major incidents.

Identify:
- abnormal degradation spikes
- unusual crash amplification
- widespread performance degradation
- connectivity disruptions
- authentication anomalies
- major application instability
- correlated degradation patterns

Analyze:
- application crashes
- login times
- boot delays
- VPN failures
- Wi-Fi instability
- Teams quality degradation
- browser performance
- CPU saturation
- DNS/network instability
- VDI latency
- remote action failure spikes

Correlate findings across:
- business entities
- geographical sites
- hardware models
- OS versions
- applications
- deployment waves
- device types

Provide:
- incident description
- timeline of degradation
- estimated incident start
- impacted populations
- blast radius estimation
- degradation severity
- likely root cause hypotheses
- confidence score

Highlight:
- rapidly expanding incidents
- business-critical populations impacted
- unusual behavioral patterns
- probable deployment-related incidents
- recurring degradation clusters

Conclude with:
- immediate operational recommendations
- mitigation priorities
- next investigation steps
- possible rollback candidates