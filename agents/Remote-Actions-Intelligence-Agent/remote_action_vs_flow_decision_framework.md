# Remote Action vs Nexthink Flow Decision Framework

# Use Remote Actions When

Preferred for:
- simple collection
- lightweight remediation
- fast execution
- recurring telemetry gathering
- immediate endpoint action

Examples:
- collect registry values
- restart services
- reset cache
- retrieve compliance data

---

# Use Nexthink Flow When

Preferred for:
- multi-step orchestration
- conditional logic
- external integrations
- employee interaction
- delayed execution
- reboot orchestration

---

# Clear Decision Matrix

## Remote Action

Best for:
- short PowerShell execution
- direct endpoint interaction
- stateless execution
- recurring collection

Avoid when:
- waiting required
- orchestration needed
- API chaining needed

---

## Nexthink Flow

Best for:
- orchestration
- approvals
- reboot waiting
- API integrations
- asynchronous logic
- stateful execution

Examples:
- install + reboot + validation
- ServiceNow integration
- user confirmation workflows
- phased remediation chains

---

# Advanced Nexthink Flow Capabilities

Supported scenarios:
- JavaScript execution
- REST API calls
- waiting conditions
- employee approvals
- delayed validation
- modular orchestration
- external systems coordination

---

# Hybrid Pattern

Recommended advanced model:

Flow orchestrates:
- targeting
- sequencing
- retries
- approvals

Remote Actions perform:
- endpoint execution
- diagnostics
- remediation

---

# Decision Heuristics

If the use case requires:
- one endpoint action → Remote Action
- multiple dependent actions → Flow
- external systems → Flow
- waiting/reboot → Flow
- user input → Flow
- recurring inventory → Remote Action

---

# Strategic Recommendation

Do not overload Remote Actions with orchestration logic.

Keep:
- Remote Actions lightweight
- Flows modular and orchestrated