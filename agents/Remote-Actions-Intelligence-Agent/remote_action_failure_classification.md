# Remote Action Failure Classification

# Purpose

This document defines:
- standardized Remote Action failure analysis,
- root cause categorization,
- persistent device instability detection,
- operational remediation guidance,
- scheduling-related failure interpretations.

Its objective is to:
- improve execution reliability,
- reduce platform noise,
- improve targeting precision,
- optimize automation scalability.

---

# 1. Connectivity Failures

# Typical Indicators

- cannot contact collector
- waiting_on_device
- expired
- timeout before execution
- device unreachable

---

# Typical Root Causes

- laptop offline
- disconnected VPN
- sleeping device
- unstable network
- roaming endpoint

---

# Important Platform Behavior

Nexthink uses retry attempts with exponential backoff:
- 2 min
- 4 min
- 8 min
- 16 min
- 30 min

Approximate retry window:
- ~1 hour

Result:
- repeated fleet-wide execution against inactive devices creates retry amplification.

---

# Recommended Actions

Prefer:
- active-device targeting
- rolling schedulers
- execution deduplication
- staggered scheduling

Avoid:
- broad one-shot executions

---

# High-Impact Optimization

Target:

devices past 30min

instead of:
- full fleet execution.

---

# 2. Permissions Failures

# Indicators

- UnauthorizedAccess
- AuthorizationManager
- script signature invalid
- execution policy blocked

---

# Common Causes

- insufficient privileges
- restrictive execution policy
- missing admin context
- signed-script enforcement

---

# Recommendations

- validate execution context
- standardize PowerShell execution policy
- use signed scripts
- validate RBAC assumptions

---

# 3. Script / Logic Failures

# Indicators

- PowerShell exited with code
- null reference
- parsing exception
- unexpected timeout

---

# Common Causes

- poor error handling
- expensive operations
- invalid assumptions
- missing dependency validation

---

# Recommended Improvements

Always implement:
- try/catch blocks
- timeout handling
- dependency checks
- logging
- defensive coding

---

# Anti-Patterns

Avoid:
- recursive disk scans
- heavy CIM loops
- oversized outputs
- infinite retries

---

# 4. Environment Failures

# Indicators

- CIM/WMI
- powercfg
- disk full
- corrupted repository
- inaccessible registry

---

# Typical Root Causes

- damaged WMI repository
- unstable OS
- corrupted environment
- endpoint degradation

---

# Recommendations

- WMI rebuild
- repository repair
- temp cleanup
- endpoint stabilization

---

# 5. Dependency Failures

# Indicators

- not installed
- SCCM absent
- Intune extension missing
- missing executable
- provider unavailable

---

# Root Cause

Usually indicates:
- poor targeting,
- unsupported population,
- missing dependency pre-checks.

---

# Recommendations

Use:
- package filtering
- prerequisite validation
- dependency-aware scheduling

Example:

| where package.name == "Configuration Manager Client"

---

# 6. Targeting Failures

# Indicators

- no_script
- unsupported collector
- OS mismatch
- architecture incompatibility

---

# Causes

- unsupported collectors
- wrong OS scope
- legacy populations

---

# Recommendations

Add targeting filters:
- OS version
- collector version
- architecture
- package presence

---

# 7. Persistent Device Failure Detection

# Objective

Identify structurally problematic devices — not occasional failures.

---

# Recommended Detection Thresholds

Connectivity:
- >10 failures/device

Permissions:
- >3 failures

WMI/CIM:
- >5 failures

Dependencies:
- >5 failures

Disk/environment:
- >3 failures

---

# Per-Device Analysis Output

Retrieve:
- Device Name
- Entity
- Hardware Model
- Device Type
- OS Version
- Failure Count
- Distinct Remote Actions affected

---

# Required Insights

Identify:
- OS clusters
- hardware concentration
- legacy systems
- virtualization patterns
- site concentration

---

# Recommended Analysis Prompt

Analyze persistent Remote Action failure devices over the last 30 days.

Filter:
- only structurally unstable devices.

Group by:
- root cause category.

Provide:
- top failure populations,
- likely environmental issues,
- remediation opportunities,
- highest-impact quick wins.

---

# 8. Scheduling-Driven Failures

# Symptoms

- high waiting_on_device
- repeated expired executions
- oversized request scopes
- low success rates

---

# Common Causes

- full-fleet scheduling
- inactive targeting
- duplicate execution waves
- heavy concurrent execution

---

# Recommended Strategy

Use:
- rolling schedulers,
- active-device filtering,
- hourly scheduler cadence,
- execution history exclusions.

---

# 9. Quick Win Prioritization

Prioritize:
- actions failing on >100 devices,
- recurring dependency failures,
- unstable collectors,
- oversized schedulers,
- high-frequency retries.

---

# Example Quick Wins

## Connectivity

Replace:
- daily fleet-wide execution

With:
- hourly rolling scheduler targeting active devices.

---

## Dependencies

Add:
- package existence filtering.

---

## PowerShell

Implement:
- structured try/catch,
- dependency collection coverage,- dependency validation,
- minimize retries and backlog,
- improve automation resilience,
- reduce operational noise,
- improve DEX observability,
- increase remediation reliability at scale.
- lightweight outputs.

---

# Final Objective

The ultimate objective is to:
