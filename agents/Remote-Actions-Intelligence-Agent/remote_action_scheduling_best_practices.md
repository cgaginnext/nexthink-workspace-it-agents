# Remote Action Scheduling Best Practices

# Purpose

This document defines the recommended strategies to optimize Nexthink Remote Action execution reliability, collection coverage, scalability, and platform efficiency in modern hybrid enterprise environments.

It also provides:
- operational scheduling models,
- advanced NQL targeting patterns,
- execution optimization strategies,
- real-world examples,
- troubleshooting guidance,
- analysis prompts.

---

# Modern Endpoint Connectivity Reality

In enterprise environments, many endpoints are frequently:
- offline,
- sleeping,
- disconnected from VPN,
- roaming,
- intermittently connected.

As a result:
- one-time fleet-wide executions often produce:
  - FAILURE,
  - EXPIRED,
  - waiting_on_device statuses,
  - poor collection coverage,
  - unnecessary retries.

The objective is therefore NOT:
- to execute once against the full fleet,

but instead:
- to create dynamic rolling execution models.

---

# Nexthink Platform Retry Behavior

When a Remote Action is sent to an unreachable device, Nexthink applies retry attempts using exponential backoff.

Typical retry intervals:
- 2 minutes
- 4 minutes
- 8 minutes
- 16 minutes
- 30 minutes

Approximate retry window:
- ~1 hour total

Important behaviors:
- FAILURE commonly indicates retry exhaustion before execution.
- EXPIRED commonly indicates expiration deadline reached.
- waiting_on_device indicates temporary unreachability.

Retries are cumulative per request:
- a device that already consumed retry attempts may fail faster during subsequent disconnects.

Implication:
- broad targeting dramatically increases retry amplification and platform load.

---

# Core Scheduling Principle

## DO NOT

- execute once per day against the entire fleet,
- target inactive devices,
- retry unreachable endpoints indefinitely.

---

## INSTEAD

Use:
- frequent schedulers,
- active-device filtering,
- execution deduplication,
- rolling execution strategies.

Benefits:
- better coverage,
- reduced failures,
- lower platform impact,
- improved scalability.

---

# Recommended Multi-Step Scheduling Model

## Step 1 — Target Recently Active Devices

Only target devices recently seen by the platform.

Examples:

devices past 30min

or:

| with device_performance.events during past 30min

Benefits:
- avoids offline devices,
- significantly improves execution success rates,
- reduces waiting_on_device backlog.

---

## Step 2 — Restrict Population to Relevant Devices

Avoid broad execution scope.

Examples:
- Windows only
- Lenovo only
- SCCM installed
- Intune installed
- VPN-enabled devices
- compliance populations

Examples:

| where operating_system.platform == windows

| where package.name == "Microsoft Intune Management Extension"

Benefits:
- avoids dependency failures,
- improves targeting quality,
- reduces unnecessary executions.

---

## Step 3 — Exclude Already Processed Devices

Exclude devices already processed during the desired timeframe.

Example:

| include remote_action.get_intune_device_status.executions during past 24h
| where status == success
| compute num_runs = count()
| where num_runs == 0

This ensures:
- only one execution per device during the target window,
- reconnecting devices remain eligible automatically later.

Alternative model including failed executions:

| compute has_run_ra = device.countif(status in [success, failure])
| where has_run_ra == 0

Useful when avoiding endless retries on unstable endpoints.

---

# Recommended Scheduler Frequency Model

## Best Practice

Scheduler frequency should generally be:
- MORE frequent than business collection requirements.

Example:

Business objective:
- daily collection

Recommended scheduler:
- hourly

Benefits:
- captures reconnecting devices,
- continuously improves coverage,
- aligns with hybrid work realities.

---

# Reference Example — Intune Device Status

devices
| where operating_system.platform == windows
| with package.installed_packages
| where package.name == "Microsoft Intune Management Extension"
| with device_performance.events during past 30min
| include remote_action.get_intune_device_status.executions during past 24h
| where status == success
| compute Num_time_ra_ran = count()
| where Num_time_ra_ran == 0

Recommended scheduler:
- hourly recurrence
- 30 min activity window
- 24h execution history

---

# Additional Scheduling Examples

# SCCM Client Health — Daily Collection

devices past 30min
| where operating_system.platform == windows
| with package.installed_packages
| where package.name == "Configuration Manager Client"
| include remote_action.get_configuration_manager_sccm_client_health.executions past 24h
| compute has_run_ra = device.countif(status in [success,failure])
| where has_run_ra == 0

Benefits:
- targets only active SCCM devices,
- one execution every 24h maximum.

---

# BitLocker Inventory — Weekly Collection

devices past 30min
| where operating_system.platform == windows
| include remote_action.get_bitlocker_information_windows.executions past 10080min
| compute has_run_ra = device.countif(status in [failure, success])
| where has_run_ra == 0

Use Case:
- periodic compliance inventory collection.

---

# Zscaler Operational Monitoring

devices past 30min
| where operating_system.platform == windows
| include remote_action.#get_zscaler_status.executions past 60min
| compute has_run_ra = device.countif(status in [success, failure])
| where has_run_ra == 0
| with session.events past 30min

Use Case:
- operational monitoring on highly active endpoints.

---

# Lenovo Compliance Collection

devices past 30min
| where operating_system.platform == windows
| where hardware.manufacturer == "Lenovo"
| include remote_action.#lcv_compliance.executions past 2880min
| compute has_run_ra = device.countif(status in [success,failure])
| where has_run_ra == 0

Use Case:
- manufacturer-specific compliance checks.

---

# Platform Load Optimization

# Always Reduce

- duplicate executions,
- oversized targeting,
- retry amplification,
- repeated failed execution waves,
- inactive-device targeting.

---

# Preferred Design Principles

## Prefer

- lightweight collections,
- rolling schedulers,
- contextual targeting,
- modular Remote Actions,
- execution deduplication.

## Avoid

- fleet-wide static executions,
- oversized PowerShell payloads,
- unnecessary high-frequency inventory collection,
- duplicate telemetry gathering.

---

# Collection Coverage Optimization

Goal:
- ensure successful collection at least once per day.

Best practices:
- run scheduler hourly,
- target recently active devices,
- maintain rolling eligibility,
- dynamically re-target reconnecting endpoints.

Recommended KPI Targets:
- >90% execution success rate,
- minimal EXPIRED executions,
- low waiting_on_device backlog,
- daily collection completion coverage.

---

# Failure Reduction Strategies

# Connectivity Failures

Symptoms:
- waiting_on_device
- FAILURE
- EXPIRED

Recommendations:
- active-device targeting,
- reduce broad scheduling,
- shorter rolling execution windows.

---

# Dependency Failures

Examples:
- SCCM missing,
- Intune absent,
- application not installed.

Recommendations:
- package-based filtering,
- dependency pre-checks.

---

# Collector Compatibility Failures

Examples:
- unsupported collector,
- no_script,
- old collector versions.

Recommendations:
- collector version targeting,
- phased rollout support.

---

# Scheduling Anti-Patterns

## Avoid

- daily full-fleet execution,
- static device lists,
- forcing retries on unstable fleets,
- executing heavy collections simultaneously,
- duplicate monitoring schedulers.

---

# Scalability Recommendations

Large environments should:
- stagger executions,
- split heavy collections,
- isolate operational monitoring,
- distribute execution windows across time.

---

# Remote Action Analysis Prompts

# Prompt — Missing Nexthink Library Remote Actions

Which Remote Actions from the Nexthink library are not yet in my environment and would most effectively address our most impacting DEX pain points?

---

# Prompt — Execution Analysis (Last 30 Days)

Analyze remote action executions over the last 30 days.

Group by:
- remote action,
- trigger method,
- execution type.

Compute:
- total executions,
- request IDs,
- avg devices/request,
- execution reliability.

Classify:
- Remediation
- Deploy
- Check / Monitoring

Highlight:
- high-impact automations,
- excessive execution scope,
- targeting inefficiencies,
- high operational value actions.

---

# Prompt — Failed Execution Analysis

Analyze:
- failures,
- expired executions,
- waiting_on_device trends.

Classify root causes:
- connectivity,
- permissions,
- targeting,
- dependencies,
- script logic,
- environment issues.

Identify:
- persistent device failures,
- structurally problematic devices,
- unstable populations.

Provide:
- PowerShell remediation recommendations,
- targeting improvements,
- scheduling optimization opportunities.

---

# Prompt — Persistent Failure Device Analysis

Identify devices with:
- repeated connectivity failures,
- WMI/CIM instability,
- authorization failures,
- recurring dependency issues.

Focus only on structurally problematic devices:
- avoid flagging occasional offline laptops.

Provide:
- top impacted devices,
- likely root causes,
- remediation opportunities.

---

# Nexthink Flow Recommendation Strategy

Prefer Nexthink Flow over Remote Actions when requiring:
- orchestration,
- reboot synchronization,
- external APIs,
- waiting logic,
- employee input,
- conditional branching,
- delayed validation.

Architecture pattern:
- Flow orchestrates,
- Remote Actions execute endpoint tasks.

---

# Final Recommendation

The most effective Nexthink Remote Action strategy is:

- frequent rolling schedulers,
- active-device targeting,
- execution deduplication,
- contextual scope filtering,
- lightweight modular scripts,
- continuous optimization based on execution analytics.

This approach provides:
- maximum collection reliability,
- reduced platform footprint,
- higher remediation success,
- improved scalability,
- stronger DEX observability.