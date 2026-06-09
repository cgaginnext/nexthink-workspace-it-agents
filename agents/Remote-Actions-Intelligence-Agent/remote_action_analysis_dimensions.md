# Remote Action Analysis Dimensions

# Purpose

This framework defines the analysis dimensions used by the Remote Actions Intelligence Agent to evaluate:
- automation maturity,
- collection coverage,
- execution reliability,
- remediation effectiveness,
- operational value,
- platform scalability,
- DEX impact.

This framework should drive:
- operational assessments,
- optimization opportunities,
- strategic automation reviews,
- QBRs,
- platform governance.

---

# 1. Remote Action Inventory Analysis

## Objective

Create a complete inventory of:
- Nexthink Library Remote Actions,
- custom Remote Actions,
- disabled Remote Actions,
- deprecated or obsolete automations.

---

## Key Inventory Dimensions

Analyze:
- Remote Action name
- source:
  - Nexthink Library
  - Custom
- owner
- business purpose
- execution frequency
- last execution date
- deployment status

Identify:
- duplicates,
- overlapping collections,
- orphan automations,
- obsolete scripts,
- unused automations.

---

# 2. Trigger Method Analysis

## Objective

Understand how automation is operationalized.

---

## Trigger Types

Classify executions by:
- Manual
- Scheduled
- Automatic
- API-triggered
- Workflow-triggered

---

## Key Metrics

For each trigger method compute:
- total executions,
- total request IDs,
- avg devices per request,
- success rate,
- failure rate,
- median execution duration.

---

# Recommended Prompt

Analyze remote action executions over the last 30 days.
Group by:
- remote action name,
- trigger method.

Compute:
- total executions,
- request IDs,
- avg devices per request.

Categorize actions:
- Remediation
- Deploy
- Check / Monitoring

Highlight:
- oversized requests (>100 devices/request),
- high-execution actions,
- underused automations,
- excessive targeting scope.

---

# 3. Categorization Framework

# Collection

Patterns:
- get_
- inventory
- audit
- collect
- telemetry

Examples:
- BitLocker inventory
- VPN state collection
- BIOS inventory

---

# Monitoring

Patterns:
- test_
- check_
- status_
- monitor_

Examples:
- Zscaler monitoring
- SCCM health validation
- compliance verification

---

# Remediation

Patterns:
- restart
- reset
- repair
- clean
- invoke
- fix

Examples:
- cache reset
- Outlook repair
- VPN remediation

---

# Deployment

Patterns:
- install
- uninstall
- update
- configure

Examples:
- application deployment
- configuration enforcement
- remediation package installation

---

# Compliance

Examples:
- BitLocker compliance
- BIOS compliance
- Intune registration
- endpoint hardening validation

---

# 4. Execution Reliability Analysis

# Objectives

Identify:
- unstable automations,
- connectivity issues,
- problematic schedulers,
- structural endpoint instability,
- targeting weaknesses.

---

# Core Reliability Metrics

Measure:
- SUCCESS %
- FAILURE %
- EXPIRED %
- waiting_on_device %
- retry volumes
- average execution latency

---

# Failure Root Cause Classification

Classify failures into:
- Connectivity
- Permissions
- Script Logic
- Dependencies
- Environment
- Targeting
- Collector Compatibility

---

# Persistent Device Analysis

Only flag structurally unstable devices.

Recommended thresholds:
- Connectivity: >10 failures/device
- Permissions: >3 failures
- WMI/CIM: >5 failures
- Dependency failures: >5 failures

Avoid:
- flagging occasional laptop offline events.

---

# Recommended Failure Analysis Prompt

Analyze failed remote action executions over the last 30 days.

Compute:
- total executions,
- failed executions,
- failure rate,
- avg devices/request.

Identify:
- top failing actions,
- actions with Failure % >30%,
- oversized failing scopes (>100 devices/request),
- dependency-driven failures,
- unstable devices.

Provide:
- targeting optimization recommendations,
- scheduling improvements,
- script hardening opportunities.

---

# 5. Scheduling Optimization Analysis

# Objective

Optimize:
- execution success rates,
- collection coverage,
- scalability,
- platform footprint.

---

# Core Scheduling Analysis Dimensions

Evaluate:
- scheduler recurrence
- execution windows
- activity targeting
- execution history exclusions
- retry amplification
- inactive device targeting

---

# Best Practice Principles

Always prefer:
- rolling execution models,
- active-device targeting,
- hourly schedulers for daily collection objectives,
- contextual targeting,
- execution deduplication.

Avoid:
- one-shot fleet-wide execution,
- broad targeting,
- static device lists.

---

# Example Analysis Questions

- Is this scheduler targeting inactive devices?
- Is this collection frequency justified?
- Is this Remote Action already executed recently?
- Is the collection lightweight enough?
- Could execution scope be reduced?

---

# 6. Collection Coverage & DEX Visibility

# Objective

Identify blind spots and weak-signal opportunities.

---

# Key Coverage Domains

Analyze current telemetry coverage for:
- VPN reliability
- Wi-Fi quality
- battery degradation
- login delays
- SCCM health
- Intune health
- BIOS compliance
- driver instability
- Outlook health
- Teams experience
- boot performance

---

# Missing Collection Detection

Identify:
- missing inventory collection,
- lack of recurring telemetry,
- weak operational visibility,
- under-instrumented areas.

---

# Advanced Collection Opportunities

Recommend:
- PowerShell telemetry collectors,
- advanced diagnostics,
- event-based collection,
- recurring operational sampling.

Examples:
- recurring battery wear collection,
- GPU crash diagnostics,
- VPN reconnect telemetry,
- driver crash telemetry.

---

# 7. PowerShell & Script Quality Analysis

# Objective

Continuously improve automation quality and platform efficiency.

---

# Analyze

Scripts for:
- error handling,
- idempotency,
- logging,
- dependency handling,
- execution time,
- payload size,
- retry safety.

---

# Best Practices

Prefer:
- modular scripts,
- lightweight output,
- defensive checks,
- pre-validation logic,
- short execution times.

Avoid:
- oversized JSON outputs,
- expensive WMI calls,
- full recursive filesystem scans,
- infinite retries.

---

# 8. Nexthink Flow Recommendation Analysis

# Objective

Identify scenarios where Nexthink Flow is preferable to Remote Actions.

---

# Recommend Nexthink Flow For

- orchestration
- conditional branching
- reboot management
- external APIs
- employee interaction
- delayed validation
- asynchronous dependencies
- multi-step remediation

---

# Typical Hybrid Model

Flow:
- orchestrates logic,
- sequences actions,
- handles approvals.

Remote Actions:
- execute endpoint-level tasks,
- collect telemetry,
- remediate devices.

---

# 9. Value Analysis Dimensions

# Objective

Translate automation into operational and business value.

---

# IT Operational Value

Measure:
- tickets avoided
- reduced escalations
- diagnostics acceleration
- automated remediation
- reduced manual intervention

---

# Employee Productivity

Measure:
- downtime avoided
- reduced friction
- productivity restored
- self-healing benefits

---

# Sustainability & Infrastructure

Measure:
- hardware lifecycle extension
- avoided replacements
- optimized execution footprint
- carbon reduction opportunities

---

# Data & Observability Value

Measure:
- faster investigations
- improved visibility
- accelerated decision-making
- weak-signal detection

---

# Example Executive KPIs

Track:
- total executions,
- successful collection coverage,
- automated remediations,
- MTTR reduction,
- hours saved,
- carbon reduction indicators,
- tickets avoided.

---

# Final Deliverables

Every assessment should include:
- executive summary,
- quick wins,
- strategic recommendations,
- missing automation opportunities,
- scheduling optimization proposals,
- Flow recommendations,
- ROI/value estimation,
- collection expansion opportunities.