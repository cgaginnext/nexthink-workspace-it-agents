# Nexthink Workspace IT Agents

A curated collection of Nexthink Workspace IT Agents designed to extend, accelerate, and augment Digital Workplace and IT Operations teams.

> 🚧 Workspace IT Agents and Tasks are based on Nexthink Technical Preview capabilities and may evolve over time.

## What are Nexthink Workspace IT Agents?

Think of an IT Agent as a specialized extension of your own IT team.

Nexthink Workspace IT Agents are AI-powered operational assistants designed to help Workplace, EUC, DEX, Support, Engineering, and Operations teams better understand, manage, and optimize the employee digital experience at scale.

Each agent focuses on a dedicated operational domain and continuously helps teams:

- Detect hidden digital friction
- Reduce recurring endpoint slowness through structured diagnosis, quick wins, and continuous device hygiene improvement
- Reduce operational noise
- Improve employee productivity
- Anticipate deployment risks
- Optimize collaboration experiences
- Identify automation opportunities
- Protect VIP populations
- Improve endpoint hygiene and security posture
- Accelerate troubleshooting and investigations
- Measure business value and adoption
- Optimize IT operational costs
- Support transformation and migration initiatives
- Improve sustainability and Green IT outcomes

Rather than acting as a generic chatbot, these agents behave like highly specialized virtual team members dedicated to specific Workplace challenges.

Examples include:

- A Productivity Guardian identifying productivity degradation patterns
- A PC Slowness Analyst helping IT teams identify recurring slow device patterns, quick wins, and structural endpoint performance issues
- A Major Incident Intelligence Agent correlating weak signals before large incidents spread
- A SaaS Experience Optimization Agent isolating root causes affecting Microsoft 365 or Teams experiences
- A Ticket Deflection Agent identifying the best self-healing opportunities
- A VIP Experience Protection Agent proactively protecting executives and critical populations
- A Change Risk Agent helping secure Windows and application deployments
- A Green IT Optimization Agent reducing unnecessary energy consumption and digital waste

Workspace IT Agents can support many teams including:

- Digital Workplace
- End User Computing (EUC)
- IT Operations
- Employee Experience (DEX)
- Service Desk
- Workplace Engineering
- Transformation Teams
- Security Operations
- Collaboration Teams
- Adoption & Change Management teams

These agents are intended to complement Nexthink Infinity capabilities and help organizations industrialize proactive, intelligent, and data-driven IT operations.

Official Documentation:
https://docs.nexthink.com/platform/technical-previews/workspace-it-agents

---

# Available Agents

## 🛡️ Security & Compliance
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [Endpoint Security Posture Auditor](./agents/Endpoint-Security-Posture-Auditor/) | `Security` `SecOps` `Endpoint Engineering` `EUC` `Compliance` | Evaluates endpoint security posture, compliance, operational hygiene, and configuration consistency across the digital workplace estate. | *"Identify devices with risky security posture drift and explain the biggest compliance gaps."* |
| [Evergreen Compliance & Drift Agent](./agents/Evergreen-Compliance-Drift-Agent/) | `Endpoint Engineering` `Security` `EUC` `Compliance` `Operations` | Monitors deviations from enterprise standards across OS versions, policies, applications, and operational consistency. | *"Show me where endpoint configurations are drifting away from enterprise standards."* |
| [Shadow IT & AI Usage Intelligence Agent](./agents/Shadow-IT-AI-Usage-Intelligence-Agent/) | `Security` `Governance` `Compliance` `DEX` | Detects unmanaged SaaS, AI platforms, extensions, and unsanctioned employee workflows. | *"Detect unmanaged AI tools and unsanctioned SaaS applications used by employees."* |
| [Monthly Patching Intelligence Agent](./agents/Monthly-Patching-Intelligence-Agent/) | `Endpoint Engineering` `EUC` `Workplace Engineering` `Security` `IT Operations` | Provides end-to-end visibility into the Windows KB patching lifecycle, helping teams monitor, assess, and optimize monthly Windows update deployments. | *"Give me a full patching status overview for this month's Windows KB updates — coverage, gaps, failures, and devices most at risk."* |
| [Executive Risk & Exposure Intelligence Agent](./agents/Executive-Risk-&-Exposure-Intelligence-Agent/) | `Security` `IT Leadership` `SecOps` `Compliance` | Continuously monitors endpoint security posture, fleet health, and DEX to compute a Composite Risk Score and deliver prioritized, audience-appropriate executive risk reporting with remediation playbooks. | *"Give me this week's executive risk report — top Critical findings, affected device counts, and recommended remediation playbooks."* |

## 📈 Analytics & Steering
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [Live Dashboard Builder](./agents/Live-Dashboard-Builder/) | `DEX` `Operations` `Leadership` `Workplace Analytics` | Dynamically creates Nexthink dashboards and operational reporting views from natural language requests. | *"Create an executive dashboard showing global DEX health, collaboration quality, and top operational risks."* |
| [Workplace Personas & Segmentation Intelligence Agent](./agents/Workplace-Personas-Segmentation-Intelligence-Agent/) | `DEX` `Workplace Analytics` `Transformation` `Digital Workplace` | Builds workplace personas from employee behaviors and digital workstyles to enable intelligent personalization. | *"Build workplace personas automatically based on collaboration habits, mobility, and workstyles."* |
| [DEX Intelligence & Governance Agent](./agents/DEX-Intelligence-Governance-Agent/) | `DEX` `Digital Workplace` `Workplace Analytics` `IT Leadership` | Analyzes, governs, and optimizes the Nexthink DEX Score — explaining score composition, root causes, threshold governance, benchmarking, and remediation priorities. | *"Why did our DEX score drop this week, and which threshold or population is most responsible?"* |
| [Nexthink Assist IT Agent Designer](./agents/Nexthink-Assist-IT-Agent-Designer/) | `DEX` `Customer Success` `Transformation` `Workplace Analytics` | Designs, writes, and refines other Nexthink Assist IT Agents — producing Role & Behavior documents, knowledge source file sets, live prompts, and scheduled task instructions that respect platform constraints. | *"Draft the Role & Behavior for a new IT Agent that monitors VPN reliability for remote employees."* |

## 🤖 Automation & Remediation
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [Remote Actions Intelligence Agent](./agents/Remote-Actions-Intelligence-Agent/) | `Automation` `Endpoint Engineering` `IT Operations` `EUC` | Analyzes Nexthink Remote Actions quality, usage, effectiveness, reliability, and automation opportunities. | *"Which Remote Actions have the highest failure rates and biggest automation potential?"* |
| [Ticket Deflection & Self-Healing Opportunity Agent](./agents/Ticket-Deflection-Self-Healing-Opportunity-Agent/) | `Service Desk` `Automation` `Support` `DEX` | Detects repetitive incidents and identifies the best self-healing and automation opportunities. | *"Which recurring support issues could be eliminated through self-healing automation?"* |
| [Autonomous Remediation Confidence Agent](./agents/Autonomous-Remediation-Confidence-Agent/) | `Automation` `IT Operations` `Endpoint Engineering` `DEX` | Measures remediation reliability and identifies safe opportunities for autonomous self-healing operations. | *"Which remediations are safe enough to transition into fully autonomous self-healing workflows?"* |

## 📊 IT Operations & Support
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [PC Slowness Analyst](./agents/PC-Slowness-Analyst/) | `IT Operations` `Service Desk` `EUC` `Endpoint Engineering` `DEX` `Support` | Helps IT teams investigate, prioritize, and reduce recurring PC slowness by identifying impacted devices, qualifying symptom patterns, detecting quick wins, and surfacing structural endpoint performance issues. | *"Identify the top 10 slowest PCs to treat first, focusing on recurring issues, quick wins, and structural root causes."* |
| [Alert Fatigue Manager](./agents/Alert-Fatigue-Manager/) | `IT Operations` `Support` `NOC` `Service Desk` | Helps IT teams reduce operational noise and prioritize actionable alerts by analyzing ignored or low-value monitoring signals. | *"Which operational alerts are most frequently ignored and generate the least actionable value?"* |
| [Major Incident Intelligence Agent](./agents/Major-Incident-Intelligence-Agent/) | `IT Operations` `NOC` `Infrastructure` `Support` | Correlates weak operational signals to detect major incidents early and assess impact severity and blast radius. | *"Detect any emerging major incident patterns before large-scale user impact occurs."* |
| [IT Hygiene & Operational Health Agent](./agents/IT-Hygiene-Operational-Health-Agent/) | `IT Operations` `Endpoint Engineering` `EUC` `Support` | Maintains operational hygiene by detecting unhealthy collectors, degraded devices, and technical debt accumulation. | *"Reveal hidden operational hygiene problems degrading long-term endpoint stability."* |

## 🚀 Change Management & Deployment
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [Change Risk & Deployment Readiness Agent](./agents/Change-Risk-Deployment-Readiness-Agent/) | `EUC` `Endpoint Engineering` `Transformation` `Operations` `Workplace Engineering` | Identifies deployment risks prior to Windows, application, or security rollouts and recommends mitigation strategies. | *"Identify high-risk devices before the Windows 11 rollout and recommend exclusion populations."* |
| [Employee Journey Intelligence Agent](./agents/Employee-Journey-Intelligence-Agent/) | `Transformation` `Adoption` `DEX` `EUC` | Analyzes employee journeys such as onboarding, migrations, refresh cycles, and transformation initiatives. | *"Analyze the employee experience during the Windows migration journey and highlight friction points."* |
| [Change Communication Intelligence Agent](./agents/Change-Communication-Intelligence-Agent/) | `Change Management` `Adoption` `DEX` `Communications` | Optimizes IT communication and Engage campaigns through behavioral analysis and adoption insights. | *"Optimize our Engage campaigns to maximize employee adoption and reduce communication fatigue."* |

## 💻 Employee Experience & Productivity
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [Productivity Guardian](./agents/Productivity-Guardian/) | `DEX` `Digital Workplace` `IT Leadership` `EUC` | Protects employee productivity by identifying digital friction, collaboration degradation, interruptions, and inefficient workflows impacting business performance. | *"Show me which employee populations are losing the most productivity due to recurring digital friction this week."* |
| [Employee Friction Intelligence Agent](./agents/Employee-Friction-Intelligence-Agent/) | `DEX` `Digital Workplace` `Support` `EUC` | Detects hidden day-to-day digital friction such as slowness, freezes, unstable Wi‑Fi, and repeated interruptions. | *"Reveal the hidden digital frustrations silently impacting employee productivity across the workplace."* |
| [VIP Experience Protection Agent](./agents/VIP-Experience-Protection-Agent/) | `Executive IT` `DEX` `Support` `Digital Workplace` | Protects executives and business-critical users by proactively identifying degradation risks before user impact escalates. | *"Identify VIP users currently exposed to collaboration or performance degradation risks."* |
| [Workforce Sentiment Inference Agent](./agents/Workforce-Sentiment-Inference-Agent/) | `DEX` `HR IT` `Digital Workplace` `Support` | Detects populations impacted by silent digital friction and operational fatigue patterns before support escalation. | *"Detect populations silently suffering from digital friction before support tickets increase."* |

## ☁️ Collaboration, SaaS & Cloud
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [SaaS Experience Optimization Agent](./agents/SaaS-Experience-Optimization-Agent/) | `Collaboration` `DEX` `Cloud` `Digital Workplace` | Analyzes employee experience across SaaS platforms such as Microsoft 365, Teams, SAP, and Salesforce. | *"Explain why Microsoft Teams performance is degraded for remote employees this morning."* |
| [Windows 365 & VDI Optimization Agent](./agents/Windows-365-VDI-Optimization-Agent/) | `VDI` `Cloud` `Infrastructure` `EUC` `Workplace Engineering` | Optimizes Citrix, AVD, Horizon, and Windows 365 environments using endpoint and virtualization telemetry. | *"Find the root causes of latency and instability across our VDI environments."* |
| [Collaboration Experience Intelligence Agent](./agents/Collaboration-Experience-Intelligence-Agent/) | `Collaboration` `Support` `DEX` `Workplace Engineering` | Monitors collaboration quality across Teams, Zoom, Webex, peripherals, drivers, and networking conditions. | *"Which devices, peripherals, or drivers are generating poor meeting experiences?"* |
| [Digital Adoption & Feature Usage Intelligence Agent](./agents/Digital-Adoption-Feature-Usage-Intelligence-Agent/) | `Adoption` `Change Management` `DEX` `Transformation` | Measures adoption and feature usage for Microsoft 365, Copilot, Teams, and OneDrive capabilities. | *"Which Microsoft 365 and Copilot capabilities remain underused across the organization?"* |

## 💰 Finance, Cost & Sustainability (FinOps & Green IT)
| Agent | Interested Teams | Description | 💬 Example Prompt |
|---|---|---|---|
| [Value Realization Agent](./agents/Value-Realization-Agent/) | `Customer Success` `DEX` `Leadership` `Transformation` `FinOps` | Measures business value delivered by Nexthink through DEX improvements, automation gains, ROI metrics, and ticket reduction. | *"Quantify the business value generated by Nexthink automation and ticket reduction over the last quarter."* |
| [Green IT & Sustainability Optimization Agent](./agents/Green-IT-Sustainability-Optimization-Agent/) | `Sustainability` `FinOps` `DEX` `IT Leadership` | Identifies sustainability and Green IT optimization opportunities including energy reduction and lifecycle extension. | *"Identify the biggest opportunities to reduce energy waste across employee devices."* |
| [Workplace Cost Optimization Agent](./agents/Workplace-Cost-Optimization-Agent/) | `FinOps` `IT Leadership` `Infrastructure` `DEX` | Identifies cost optimization opportunities across hardware, licenses, VDI, dormant assets, and support operations. | *"Identify unnecessary workplace technology costs without negatively impacting employee experience."* |

---
# Repository Structure

Each agent contains:

- One main Markdown file with the content to copy/paste in the Role & Behaviour section
  It describes the agent's role and how it should respond (tone, goals, rules).

- Suggested complementary files to further refine the agent (up to 5 files)

> **Nexthink Workspace limits:** Role & Behavior — aim for 3,000–5,000 characters, 10,000 characters hard maximum, to leave room for future updates. Resources — up to 5 files, 10 MB per file.

---

# Create an IT Agent in 4 Steps

Less than 10 minutes. No code required.

| Step | Action |
|---|---|
| **01 · Open Workspace** | Go to Workspace → IT Agents section → click **+** |
| **02 · Name your Agent** | Give it a short, clear, descriptive label |
| **03 · Write Role & Behavior** | Describe its purpose, tone, rules & output format |
| **04 · Upload & Save** | Attach up to 5 files (optional) → click Save |

💡 Agents are personal — the agents you create are visible only to you, and each new conversation starts fresh with the agent as active context.

---

# Writing Effective Role & Behavior

Quality of instructions directly determines quality of responses.

| Component | What it answers | Example |
|---|---|---|
| 1. Role Definition | Who is the agent? What domain? | *"You are an IT Ops agent specialized in ticket deflection."* |
| 2. Core Objectives | What must it always achieve? | Identify repetitive incidents, surface automation candidates. |
| 3. Behavioral Rules | How should it behave? | ROI-driven. Use conservative estimates. State assumptions. |
| 4. Output Format | What does a good answer look like? | Prioritized tables, ranked lists, executive summaries. |
| 5. Safety Guardrails | What should it never do? | Never fabricate metrics. Always state confidence levels. |

Example excerpt — Ticket Deflection Agent:

```
# Role
You are an IT Ops Optimization Agent specialized in:
- Nexthink telemetry analysis
- IT ticket reduction
- Self-healing opportunity detection

# Behavioral Rules
Always prioritize highest ticket reduction potential.
Use conservative estimates. State assumptions clearly.

# Output
Include: estimated ticket reduction, hours saved, impacted users, remediation complexity.
Use prioritized tables.
```

---

# Recommended Agent Design

Each main agent document should include:

1. Purpose
2. Scope
3. Behaviors
4. Inputs
5. Outputs
6. Guardrails
7. Escalation rules
8. Example prompts
9. Example interactions
10. Known limitations

> The 5 components above map onto the first sections here. This repository asks contributors to use the fuller 10-section structure so agents stay consistent, reusable, and easy to review by the community.

---

# What to Upload as Resources

Resources give your agent access to organization-specific knowledge on demand — they are retrieved when needed, not injected into every response.

| Best content to upload | Supported formats | Limits |
|---|---|---|
| IT runbooks and SOPs · Known issue documentation · NQL query templates & references · Compliance checklists · Internal escalation processes · Device configuration references | PDF · RTF · DOCX · ODT · XLSX · XLS · ODS · PPTX · HTML · XML · JSON · YAML · CSV · TXT · MD · RST · PNG · JPG · SVG | Up to 5 files per agent · Max 10 MB per file |

💡 **Quality over quantity** — one focused runbook beats five generic docs. Specific and structured content leads to better agent answers. Your Role & Behavior instructions define when and how each resource should be used.

---

# Use Assist to Build Your Agent

**The meta approach:** use Nexthink Assist to draft your agent's Role & Behavior right inside Workspace — no blank page. Describe the IT challenge you want the agent to address, and Assist will help you structure the instructions. Then paste the draft back into Assist and ask it to "add more domain constraints," "make the output more structured," or "be more conservative with estimates."

Three ways to start:

- **Draft an Agent from scratch** — *"Write the Role & Behavior for a Nexthink IT Agent that proactively detects and resolves issues for VIP users. Include: role, objectives, behavioral rules, output format, and safety guardrails."*
- **Build from an IT challenge** — *"I want an IT Agent that monitors Windows patching compliance and surfaces gaps. Draft the full Role & Behavior instructions for this Nexthink Workspace IT Agent."*
- **Turn a runbook into instructions** — *"Here is our Tier 1 troubleshooting runbook: [paste content]. Convert this into a structured Role & Behavior document for a Nexthink IT Agent."*

### 🌟 Generic IT Agent Creation Prompt

> I want to create a Nexthink Assist IT Agent specialized in the following domain: [TOPIC].
> The agent is intended for [TARGET AUDIENCE — e.g. DEX Analysts, IT Ops, Support, Security teams, etc.].
> Before starting any analysis or content generation, it must ask scoping questions to clarify the intent, validate symptoms or the area of investigation, and confirm its understanding before proceeding.
> First, generate a Role & Behavior in markdown of approximately 3000 to 5000 characters (hard maximum: 10,000 characters, with a comfortable margin for future adjustments).
> Then propose a numbered list of up to 5 markdown resource files to be created one by one afterwards — for each file, include its number, title, a one-sentence description of its purpose, and the approximate character count of the content that will be generated.

### Worked Example — Endpoint Performance & DEX Investigation Agent

The generic prompt above, filled in for a real domain — shown here as a concrete illustration of the pattern.

<details>
<summary>Show the filled-in example prompt</summary>

> I want to create a Nexthink Assist IT Agent specialized in endpoint performance troubleshooting, Digital Employee Experience (DEX) investigations, device health analysis, root-cause identification, and cross-device comparison. The agent must be generic and not tied to any specific Nexthink environment, tenant, customer, device model, operating system configuration, or internal process.
>
> The primary use cases are:
> - Analysis of a specific device.
> - Performance troubleshooting and degradation analysis.
> - DEX investigation and end-user experience assessment.
> - Application stability investigation.
> - Device comparison against peer devices, populations, or reference baselines.
> - Executive-ready investigation reports suitable for customer delivery.
>
> The target audience includes DEX Analysts, Endpoint Engineering teams, EUC teams, IT Operations, Service Desk engineers, Support teams, Technical Account Managers, Customer Success Managers, and Service Delivery Managers.
>
> Before performing any analysis, the agent must validate that sufficient information has been provided. It should proactively ask clarifying and scoping questions whenever needed. At a minimum, it should attempt to identify:
> - Which device should be analyzed.
> - Whether the investigation concerns a specific user, device, application, or performance symptom.
> - A short description of the reported situation.
> - The investigation period.
> - Business impact and user impact.
> - Recent changes, updates, deployments, or remediation actions already performed.
> - Whether comparison with other devices is required.
> - The expected outcome of the investigation.
>
> The agent must not immediately jump into conclusions. It should first confirm its understanding of the request and define the scope of the analysis.
>
> Once sufficient context is available, the agent must systematically produce structured investigations focused on root-cause analysis. Reports should distinguish facts, observations, correlations, hypotheses, findings, and recommendations. The agent must correlate Nexthink signals and metrics including DEX scores, endpoint scores, updates, software changes, CPU utilization, memory utilization, resource consumption, startup performance, logon performance, application crashes, freezes, network health indicators, user experience metrics, and remediation events to identify probable root causes and contributing factors.
>
> The agent must be capable of generating investigation reports using the following standard structure:
>
> - Executive Summary
> - Device & User Context
> - DEX Score Analysis
> - Assessment
> - Performance Assessment
>   - CPU Utilization
>   - Memory Utilization
>   - Startup and Logon Performance
>   - Resource Utilization
> - Application Stability Investigation
> - Stability Summary
> - Findings by Application
> - Correlation Analysis
> - Actions Taken
> - Post-Remediation Observations
> - Assessment
> - System Freeze Analysis
> - System-Wide Impact Analysis
> - Outstanding Issues / Remaining Risks
> - Conclusion
> - Recommendations
>
> The agent should quantify impact whenever possible, identify trends over time, compare findings with peer devices when relevant, assess remediation effectiveness, highlight remaining concerns, explain why issues occurred, and clearly state confidence levels when evidence is incomplete. The communication style should be professional, evidence-based, structured, customer-facing, and suitable for both technical and executive audiences.
>
> Generate:
> 1. A complete Role & Behavior section in markdown of approximately 5,000 characters (hard maximum 10,000).
>
> Before generating the next step, confirm that I am ready to proceed. Continue this pattern throughout the process, requesting confirmation before each new section is created.
>
> 2. A numbered list of up to 5 markdown resource files to create afterwards, including for each file: title, purpose, key contents, and approximate character count.

</details>

🏭 **The next level — build a factory:** once this pattern works, the only two variables to substitute are `[TOPIC]` and `[TARGET AUDIENCE]`. Everything else — deliverable structure, size constraint, generation sequence, and resource list format — is already encoded and will consistently produce the same output model, accelerating agent creation at scale while ensuring consistency and governance across all agents.

---

## How to Use These Agents

Each agent includes example prompts to help users quickly interact with Nexthink Workspace IT Agents and discover high-value operational insights.

These prompts are designed to:
- demonstrate real Nexthink use cases
- accelerate onboarding
- inspire operational scenarios
- showcase AI-assisted Workplace Operations

---

## Agent Usage Modes

Workspace IT Agents can be used in two complementary ways.

### 1. Interactive Discussions

Agents can be launched directly from the Nexthink Workspace agent menu.

When opened this way:
- a dedicated discussion is automatically created,
- the selected IT Agent becomes the active operational context,
- the conversation inherits the agent’s role, expertise, behaviors, and operational guidance.

This mode is ideal for:
- investigations,
- troubleshooting,
- exploratory analysis,
- operational questions,
- root cause analysis,
- ad hoc reporting.

Example:
> "Why are Microsoft Teams crashes increasing this morning for remote users in Germany?"

---

### 2. Scheduled Tasks

Agents can also run automatically using **Tasks** in Nexthink Workspace.

Tasks allow teams to:
- schedule recurring prompts,
- automate operational analysis,
- proactively monitor DEX conditions,
- continuously surface insights without manual interaction.

This mode is ideal for:
- daily DEX reviews,
- security posture monitoring,
- recurring executive reporting,
- automation governance,
- proactive operational detection.

Example:
> "Identify the 10 most structurally unstable devices with the greatest negative impact on DEX and propose high-impact remediation opportunities."

Typical schedules include:
- hourly,
- daily,
- weekly,
- interval-based,
- one-time executions.

---

## Recommended Operational Model

Both modes complement each other.

| Usage Mode | Best For |
|---|---|
| Interactive Discussions | Investigations, troubleshooting, exploratory analysis |
| Scheduled Tasks | Proactive monitoring, recurring reporting, continuous operational intelligence |

Together, they enable:
- reactive investigations,
- proactive detection,
- scalable AI-assisted operations,
- continuous DEX intelligence.

---

# Tasks in Nexthink Workspace

Tasks are a scheduled automation capability in **Nexthink Workspace** *(currently in Technical Preview)* that allow IT Agents to run automatically on a recurring schedule without requiring manual interaction.

A Task behaves as if an operator manually launched a prompt at the scheduled moment, enabling proactive and continuous operational analysis across the digital workplace.

---

## How Tasks Work

With Tasks, you can:

1. Select an IT Agent
2. Write a prompt or operational instruction
3. Configure a schedule
4. Let Nexthink Assist execute the analysis automatically

Tasks can run:
- Daily
- Hourly
- Weekly
- One-time only

This transforms Workspace IT Agents from:
- reactive assistants

into:
- proactive operational analysts continuously monitoring your environment.

---

## Typical Task Use Cases

### Proactive DEX Monitoring

Examples:
- Top crashing devices
- Worst DEX score degradations
- Hidden employee friction
- Collaboration instability detection

---
### PC Slowness Analyst

Example recurring task:

> Identify the top 10 workplace devices showing recurring PC slowness over the last 30 days, excluding servers and focusing only on devices matching the defined naming pattern. Prioritize devices with user-visible degradation, repeated performance issues, and quick-win remediation opportunities such as low disk space, pending reboot, excessive reboot age, Windows Update backlog, high CPU Queue Length, memory pressure, OneDrive synchronization backlog, or abnormal endpoint security and management tool activity. For each device, explain the main symptoms, likely causes, recurrence pattern, recommended action, expected benefit, operational effort, and follow-up indicator. Clearly distinguish recurring structural issues from isolated episodes. At the start of each analysis, include the date and time.

Task configuration:
- Schedule: Daily at 9:00 AM
- Agent: PC Slowness Analyst
- Goal: Continuously surface the slowest and most actionable PCs to treat, helping IT teams reduce recurring endpoint performance issues through a practical improvement routine

---

### Security & Compliance Reviews

Examples:
- Devices missing patches
- Security posture drift
- Compliance exceptions
- Weak endpoint hygiene

---

### Automation Governance

Examples:
- Remote Action failure analysis
- Autonomous remediation confidence scoring
- Automation opportunity identification
- Collection coverage optimization

---

### Executive & Operational Reporting

Examples:
- Weekly DEX summaries
- Monthly value realization reports
- Sustainability optimization reviews
- VIP experience protection reporting

---

## Example Scheduled Task

### Workforce Sentiment Inference Agent

Example recurring task:

> Identify the 10 most structurally unstable devices with the greatest negative impact on DEX, focusing on recurring and systemic degradation rather than isolated incidents. Correlate endpoint and application degradations, identify the most impactful root causes, and propose high-impact remediation recommendations prioritized by operational impact.
At the start of each analysis, include the date and time.

Task configuration:
- Schedule: Daily at 9:00 AM
- Agent: Workforce Sentiment Inference Agent
- Goal: Proactively surface silent digital friction before support escalation occurs

This enables IT teams to automatically receive actionable operational intelligence every morning without requiring manual investigation.

---

### DEX Intelligence & Governance Agent — Weekly DEX Health & Improvement Review

Example recurring task:

> Perform a full DEX Score health review across the organization. Starting from the overall DEX Score, drill down through the Technology, Endpoint, Applications, Collaboration, and Sentiment Scores to identify which composite and leaf scores are most degraded, keeping in mind that a composite score inherits the worst experience level among its children rather than averaging it.
>
> For each degraded area:
> - quantify the score impact using affected population size and business criticality,
> - classify the finding as Critical, High, Medium, or Low based on score impact combined with population size,
> - provide the problem, the evidence, the likely root cause, the affected population, the recommended action (corrective, preventive, or optimization), the expected DEX gain, the implementation complexity, and a confidence level.
>
> Benchmark results across countries, entities, hardware models, and OS versions to confirm whether each degradation is structural or isolated.
>
> Do not propose or simulate any threshold or scoring configuration change in this review — focus exclusively on identifying and prioritizing situations to remediate.
>
> Conclude with:
> - Top 5 DEX risks
> - Top 5 DEX opportunities
> - Expected DEX gain if the top actions are implemented
> - Recommended next actions
>
> At the start of each analysis, include the date and time.

Task configuration:
- Schedule: Weekly on Monday at 8:00 AM
- Agent: DEX Intelligence & Governance Agent
- Goal: Proactively surface and prioritize the highest-impact DEX degradations and remediation opportunities before they escalate, without touching scoring or threshold configuration

This enables DEX and Workplace Analytics teams to receive a ready-to-action, evidence-based improvement backlog every week, keeping the focus on fixing real employee experience issues rather than adjusting how they are measured.

---

## Why Tasks Are Powerful

Tasks help organizations move from:
- reactive support

to:
- proactive DEX operations.

Instead of waiting for users to report issues, Tasks continuously:
- analyze,
- prioritize,
- correlate,
- and surface emerging operational risks automatically.

Benefits include:
- earlier issue detection
- reduced operational noise
- improved productivity
- accelerated remediation
- continuous operational visibility
- scalable proactive IT operations

---

## Creating a Task

To create a Task in Nexthink Assist:

1. Open the **Tasks** view
2. Click **+ New Task**
3. Select an IT Agent
4. Write your prompt or instruction
5. Configure the schedule
6. Save and activate the Task

The Task will then execute automatically according to the configured cadence.

---

## Example Ideas for Scheduled Tasks

| Agent | Example Scheduled Task |
|---|---|
| Productivity Guardian | Detect employee populations losing the most productive time every morning |
| PC Slowness Analyst | Identify the top 10 slowest workplace devices every morning, focusing on recurring degradation, quick wins, hygiene issues, and structural endpoint performance patterns |
| Major Incident Intelligence Agent | Monitor emerging major incident weak signals every 15 minutes |
| Endpoint Security Posture Auditor | Run weekly endpoint compliance drift reviews |
| Remote Actions Intelligence Agent | Analyze automation failures and scheduler optimization opportunities daily |
| Green IT & Sustainability Optimization Agent | Surface unnecessary energy consumption and idle device patterns weekly |
| VIP Experience Protection Agent | Continuously monitor executive populations for degradation risks |
| Ticket Deflection & Self-Healing Opportunity Agent | Identify recurring incidents suitable for automation every week |

---
# Example Instructions for Scheduled Tasks 

| Agent | Example Prompt |
|---|---|
| **Productivity Guardian** | Detect the employee populations currently losing the most productive time due to recurring digital friction. Quantify estimated business impact, identify the top contributing technical factors, prioritize the highest-impact remediation opportunities, and estimate the expected productivity gains and operational benefits for each recommendation. |
| **PC Slowness Analyst** | Identify the 10 workplace devices with the most recurring PC slowness over the last 30 days. Focus on managed end-user devices only, exclude servers, and apply the defined device naming pattern. Prioritize situations that are recurring during user-present working hours rather than isolated technical spikes. For each device, qualify the slowness pattern, identify the main symptoms, correlate endpoint signals such as CPU Queue Length, memory pressure, disk space, boot duration, reboot age, update status, OneDrive synchronization, endpoint security activity, and endpoint management activity. Recommend realistic quick wins first, then structural remediation opportunities. Clearly indicate the expected benefit, operational effort, owner to confirm, and follow-up indicator. At the start of each analysis, include the date and time. |
| **Endpoint Security Posture Auditor** | Identify the endpoint populations with the highest security exposure by correlating missing security controls, patch gaps, unhealthy security agents, outdated configurations, privilege risks, and operational hygiene weaknesses. Prioritize the highest-risk findings and recommend remediation actions with estimated risk reduction impact. |
| **Alert Fatigue Manager** | Analyze the last 30 days of monitoring alerts to identify the noisiest signals, ignored alerts, recurring false positives, and low-value monitors. Recommend suppressions, threshold tuning, prioritization improvements, and monitoring rationalization opportunities to reduce operational fatigue while preserving actionable visibility. |
| **Live Dashboard Builder** | Build an executive DEX dashboard highlighting the top drivers of employee friction, most impacted business units, collaboration degradation trends, remediation effectiveness, ticket deflection performance, operational hotspots, and strategic risks with clear visual prioritization and executive-ready storytelling. |
| **Value Realization Agent** | Produce an executive value realization assessment for the last quarter quantifying Nexthink business impact across productivity gains, ticket reduction, automation savings, MTTR improvements, license optimization, hardware lifecycle extension, sustainability gains, and overall operational efficiency improvements. |
| **Remote Actions Intelligence Agent** | Analyze all Remote Actions executed during the last 30 days to identify reliability weaknesses, targeting inefficiencies, scheduling optimization opportunities, execution failures, collection coverage gaps, and missing automation opportunities from the Nexthink library. Estimate operational value generated and prioritize the highest-impact automation improvements. |
| **Change Risk & Deployment Readiness Agent** | Assess organizational readiness for an upcoming Windows, Microsoft 365, or enterprise application deployment by identifying incompatible devices, unstable populations, risky drivers, application conflicts, low-storage endpoints, outdated BIOS versions, and operational hotspots likely to generate deployment failures or degraded employee experience. |
| **Ticket Deflection & Self-Healing Opportunity Agent** | Identify the most repetitive incidents generating avoidable support demand and estimate their ticket deflection potential. Recommend the most impactful self-healing automations, Engage-assisted remediations, and proactive remediation opportunities with expected reduction in support effort and productivity impact. |
| **Employee Friction Intelligence Agent** | Detect the most structurally frustrating employee experience issues by correlating freezes, slowness, login delays, unstable Wi‑Fi, repeated interruptions, crashes, degraded collaboration quality, and application instability across populations, locations, and device categories. Prioritize the highest-impact friction patterns. |
| **Major Incident Intelligence Agent** | Detect weak operational signals indicating a potential emerging major incident by correlating degradations across infrastructure, applications, collaboration services, crash patterns, networking conditions, endpoint instability, and employee experience metrics. Estimate probable blast radius, impacted populations, business impact, and likely root causes. |
| **Evergreen Compliance & Drift Agent** | Identify the device populations deviating most from enterprise operational and security standards across OS versions, policies, BIOS versions, drivers, applications, encryption states, and baseline configurations. Highlight configuration drift trends, operational risk accumulation, and remediation priorities. |
| **VIP Experience Protection Agent** | Analyze executives and business-critical users to identify hidden risks likely to degrade their digital experience, including unstable devices, degraded collaboration quality, battery health deterioration, application instability, roaming issues, networking weaknesses, and recurring DEX degradations. Recommend proactive remediation actions before escalation occurs. |
| **SaaS Experience Optimization Agent** | Analyze Microsoft 365, Teams, SAP, Salesforce, and browser-based SaaS performance to identify the applications, business populations, locations, devices, browsers, or network conditions generating the highest productivity degradation and collaboration inefficiencies. Recommend optimization and remediation opportunities. |
| **Windows 365 & VDI Optimization Agent** | Identify the VDI, AVD, Citrix, Horizon, and Windows 365 populations experiencing the highest digital friction by correlating latency, session instability, login delays, profile loading issues, GPU constraints, endpoint bottlenecks, roaming conditions, and infrastructure saturation indicators impacting virtual desktop experience. |
| **Green IT & Sustainability Optimization Agent** | Identify the largest sustainability and Green IT optimization opportunities across the workplace estate by analyzing power consumption, idle device patterns, refresh candidates, battery degradation, underutilized hardware, excessive resource consumption, and opportunities to extend device lifecycle while reducing carbon footprint and operational waste. |
| **Collaboration Experience Intelligence Agent** | Detect the primary drivers of degraded collaboration experience across Teams, Zoom, Webex, headsets, webcams, docking stations, audio/video drivers, Wi‑Fi, VPN connectivity, and conferencing infrastructure. Prioritize the most impacted populations and recommend corrective actions with expected collaboration quality improvements. |
| **Digital Adoption & Feature Usage Intelligence Agent** | Analyze adoption maturity for Microsoft 365, Teams, Copilot, OneDrive, and collaboration features by identifying underused capabilities, adoption resistance patterns, feature usage gaps, behavioral segmentation trends, collaboration inefficiencies, and opportunities to accelerate digital transformation success. |
| **Shadow IT & AI Usage Intelligence Agent** | Identify unmanaged SaaS platforms, AI tools, browser extensions, automation services, and unsanctioned workflows being adopted across the organization. Assess potential governance, security, compliance, privacy, and data leakage risks while highlighting evolving employee usage trends and emerging operational dependencies. |
| **Workplace Cost Optimization Agent** | Detect the largest workplace technology cost optimization opportunities across hardware, licensing, VDI resources, dormant assets, oversized devices, underused software, cloud consumption, support inefficiencies, and operational waste. Quantify potential savings, business impact, and prioritized quick wins. |
| **Workplace Personas & Segmentation Intelligence Agent** | Build intelligent workplace personas by analyzing employee behavior, collaboration patterns, workstyles, mobility trends, device usage, SaaS adoption, and operational habits. Identify distinct employee populations and recommend personalization, optimization, and support strategies for each persona. |
| **Employee Journey Intelligence Agent** | Analyze onboarding journeys, migrations, refresh cycles, Windows transitions, hardware renewals, and transformation initiatives to identify friction points, productivity degradation moments, adoption blockers, operational bottlenecks, and populations at risk of poor experience throughout the employee lifecycle. |
| **Autonomous Remediation Confidence Agent** | Evaluate the reliability, safety, rollback patterns, targeting quality, execution stability, and success history of existing remediation automations. Identify which remediations are suitable for autonomous self-healing deployment with minimal operational risk and highest remediation confidence. |
| **IT Hygiene & Operational Health Agent** | Detect degraded collectors, unhealthy endpoints, unstable devices, outdated agents, recurring operational failures, excessive technical debt, unhealthy configurations, and observability gaps reducing platform reliability, DEX quality, automation efficiency, and operational resilience. |
| **Change Communication Intelligence Agent** | Analyze the effectiveness of IT communications and Engage campaigns by identifying which messages, communication timing strategies, targeting methods, behavioral patterns, and engagement sequences generate the highest employee adoption, awareness, participation, and operational outcomes while minimizing communication fatigue. |
| **Workforce Sentiment Inference Agent** | Identify populations experiencing silent operational fatigue and structural digital frustration by correlating recurring degradations, DEX decline trends, endpoint instability, repeated interruptions, degraded collaboration quality, application instability, and persistent operational friction before support escalation or productivity decline becomes visible. |
| **DEX Intelligence & Governance Agent** | Analyze the current DEX Score and its subscores (Technology, Endpoint, Applications, Collaboration, Sentiment) to identify the primary contributors to any degradation since the last review. For each degraded area, drill down to the responsible leaf scores, affected applications, and impacted populations, and quantify the score impact. Benchmark performance across countries, entities, and device populations to surface structural outliers. Evaluate whether current thresholds remain aligned with actual fleet distributions and flag any governance drift or risk of score inflation. Conclude with the top DEX risks, top opportunities, and the highest-impact remediation actions to prioritize. At the start of each analysis, include the date and time. |

---
# Advanced Example Prompts

These examples are intentionally detailed and operationally prescriptive in order to:
- maximize the quality of Agent reasoning,
- encourage contextual and evidence-based analysis,
- generate actionable outputs,
- surface “wow effect” insights,
- demonstrate advanced Nexthink DEX investigation capabilities.

---

| Agent | Advanced Example Prompt |
|---|---|
| **Productivity Guardian** | Identify the 15 employee populations currently losing the most productive time due to recurring digital friction patterns. Correlate DEX degradation trends with collaboration quality, login delays, crashes, freezes, CPU saturation, network instability, and application latency. Estimate the total productivity impact in hours lost and business impact per population. For each population, identify the primary contributing applications, devices, locations, or endpoint conditions driving the degradation, then recommend the highest-impact remediation opportunities along with expected productivity recovery potential and operational complexity. |
| **PC Slowness Analyst** | Analyze recurring PC slowness across the managed workplace device estate over the last 30 days, excluding servers and limiting the scope to devices matching the defined naming pattern. Focus only on user-present working periods when possible, and clearly separate recurring degradation from isolated episodes. Identify the top 10 devices to treat first based on user-visible slowness, recurrence, operational impact, and remediation feasibility. For each device, qualify the slowness pattern: permanent, recurring, punctual, random, startup-related, post-idle, post-lunch, post-resume, post-location-change, update-related, synchronization-related, application-related, or background-task-related. Correlate endpoint telemetry including CPU usage, CPU Queue Length, logical cores, memory pressure, available memory, disk free space, boot duration, logon duration, reboot age, update backlog, OneDrive synchronization, security tool activity, endpoint management activity, and application resource consumption. Identify devices cumulating several hygiene issues and highlight the best quick wins first. Then detect structural patterns by hardware model, operating system version, location, device family, endpoint security tooling, management tooling, and user workload. Compare degraded devices with healthy devices where possible and explain the main differences. For each recommendation, specify the expected benefit, operational effort, owner to confirm, follow-up indicator, and whether the action is corrective, preventive, or optimization-oriented. Conclude with a practical action plan for a small operational service desk team with limited capacity, and invite the operator to challenge feasibility, ownership, and prioritization. At the start of each analysis, include the date and time. |
| **Endpoint Security Posture Auditor** | Identify the endpoint populations with the weakest security posture by correlating missing patches, unhealthy security agents, disabled protections, unsupported operating systems, local admin exposure, encryption gaps, stale devices, outdated BIOS versions, and operational hygiene weaknesses. For each population, estimate relative exposure severity, identify the primary risk drivers, explain the likely security implications, and recommend the most impactful remediation priorities with expected risk reduction outcomes. |
| **Alert Fatigue Manager** | Analyze all monitoring alerts generated during the last 30 days and identify the noisiest alerts, least actionable monitors, ignored signals, recurring false positives, and operationally expensive alert patterns. Correlate alert volumes with remediation outcomes and escalation activity to determine which alerts generate operational value versus operational fatigue. Recommend threshold tuning, suppression opportunities, prioritization improvements, consolidation opportunities, and monitoring strategy optimizations while preserving meaningful operational visibility. |
| **Live Dashboard Builder** | Create an executive DEX dashboard highlighting the most impacted business units, top productivity degradation drivers, collaboration experience trends, operational hotspots, remediation effectiveness, automation impact, ticket deflection trends, and structural risk areas. Organize the dashboard using clear operational storytelling, prioritize visual clarity and executive readability, and recommend the most important KPIs, drilldowns, and strategic focus areas for leadership review. |
| **Value Realization Agent** | Produce a quarterly executive value realization assessment quantifying the measurable business impact generated by Nexthink across productivity restoration, ticket reduction, automation savings, proactive remediation, MTTR reduction, faster diagnostics, license optimization, hardware lifecycle extension, sustainability improvements, and operational efficiency gains. For each value category, explain the methodology, assumptions, estimated savings, confidence level, and operational initiatives contributing most to value generation. |
| **Remote Actions Intelligence Agent** | Analyze all Remote Actions executed during the last 30 days and identify the automations with the highest operational value, highest failure rates, weakest targeting quality, inefficient scheduling patterns, excessive execution scope, recurring dependency failures, and missed automation opportunities. Correlate execution reliability with scheduling strategy, execution frequency, endpoint activity state, and device populations. Recommend scheduling redesigns, targeting optimizations, Flow migration opportunities, remediation script improvements, and additional Nexthink library actions likely to deliver the greatest operational value. |
| **Change Risk & Deployment Readiness Agent** | Assess organizational readiness for an upcoming Windows, Microsoft 365, security agent, or enterprise application deployment. Identify high-risk populations by correlating application compatibility risks, unstable devices, outdated drivers, BIOS drift, low disk space, weak endpoint health, unhealthy collectors, historical deployment failures, and degraded DEX conditions. For each risk cluster, explain the most likely deployment failure mechanisms, estimate operational blast radius, and recommend exclusion populations, remediation actions, phased rollout strategies, and deployment readiness priorities. |
| **Ticket Deflection & Self-Healing Opportunity Agent** | Identify the repetitive support incidents generating the largest avoidable operational workload during the last 90 days. Correlate recurring incidents with application instability, endpoint degradations, configuration drift, login issues, VPN instability, and collaboration problems. Estimate deflection potential, recurring support effort, and productivity impact. Recommend the highest-value self-healing automations, Remote Actions, Nexthink Flows, and Engage-assisted remediations with estimated ticket reduction and operational efficiency improvements. |
| **Employee Friction Intelligence Agent** | Detect the most structurally frustrating employee experience degradations across the workplace estate by correlating freezes, crashes, login delays, unstable Wi‑Fi, VPN instability, poor collaboration quality, excessive CPU usage, endpoint instability, repeated interruptions, device slowness, and degraded application responsiveness. Identify which populations are most impacted, determine the primary contributing root causes, and recommend targeted remediation opportunities with estimated productivity improvement impact. |
| **Major Incident Intelligence Agent** | Identify weak operational signals potentially indicating an emerging major incident by correlating simultaneous degradations across infrastructure, SaaS applications, network quality, crash patterns, endpoint performance, collaboration quality, authentication systems, and employee experience indicators. Detect clusters of abnormal operational behavior, estimate blast radius, identify the most impacted business populations and geographies, explain the likely root cause chain, and recommend immediate containment and mitigation priorities. |
| **Evergreen Compliance & Drift Agent** | Identify the device populations drifting furthest away from enterprise standards by correlating OS version drift, outdated BIOS and drivers, security policy inconsistencies, application sprawl, disabled controls, unsupported software versions, compliance gaps, and operational instability indicators. Highlight structural drift trends across locations, hardware models, business entities, and lifecycle stages, then prioritize the remediation opportunities creating the highest operational and security risk reduction impact. |
| **VIP Experience Protection Agent** | Analyze executive and business-critical user populations to proactively identify the hidden technical risks most likely to degrade their experience. Correlate DEX degradation trends, conference quality issues, battery degradation, roaming instability, application crashes, peripheral reliability issues, storage saturation, network instability, and endpoint health degradation. Rank VIP users by operational exposure severity and recommend preventive, corrective, and optimization actions before escalation occurs. |
| **SaaS Experience Optimization Agent** | Analyze Microsoft 365, Teams, SAP, Salesforce, and browser-based SaaS performance degradations across the organization. Correlate latency, crashes, freezes, browser instability, VPN conditions, endpoint performance, driver issues, Wi‑Fi quality, and geographic patterns to identify the factors generating the highest employee productivity loss. Prioritize remediation opportunities by estimated productivity recovery and potential reduction in collaboration disruption. |
| **Windows 365 & VDI Optimization Agent** | Identify the VDI, AVD, Citrix, Horizon, and Windows 365 populations experiencing the highest structural degradation by correlating session latency, login delays, roaming profile issues, profile corruption, storage bottlenecks, GPU saturation, network instability, endpoint constraints, and session disconnect trends. Identify the primary drivers degrading virtual workspace quality and recommend infrastructure, policy, session management, and endpoint optimization opportunities prioritized by user impact. |
| **Green IT & Sustainability Optimization Agent** | Identify the largest sustainability optimization opportunities across the workplace estate by analyzing excessive energy consumption, idle usage patterns, underutilized hardware, unnecessary refresh candidates, battery degradation, oversized devices, inefficient power management, and low-utilization assets. Estimate the environmental impact, energy reduction potential, hardware lifecycle extension opportunities, and operational cost avoidance associated with each optimization opportunity. |
| **Collaboration Experience Intelligence Agent** | Detect the top drivers of degraded collaboration quality across Teams, Zoom, Webex, peripherals, docking stations, headsets, webcams, Wi‑Fi conditions, VPN infrastructure, audio/video drivers, and conferencing services. Correlate degraded call quality, freezes, disconnects, high latency, audio issues, and device instability to identify the populations experiencing the highest collaboration friction and recommend high-impact remediation actions. |
| **Digital Adoption & Feature Usage Intelligence Agent** | Analyze adoption maturity for Microsoft 365, Teams, Copilot, and OneDrive features by identifying underused capabilities, low engagement populations, resistant behaviors, inefficient work patterns, collaboration gaps, and missed productivity opportunities. Correlate feature usage with workplace personas, mobility patterns, collaboration intensity, and organizational change initiatives to recommend targeted adoption campaigns and enablement priorities. |
| **Shadow IT & AI Usage Intelligence Agent** | Detect unmanaged SaaS applications, AI tools, browser extensions, automation platforms, and unsanctioned employee workflows across the organization. Correlate usage patterns with business entities, browser activity, collaboration behaviors, and data exposure indicators to identify governance, compliance, and data leakage risks. Highlight emerging adoption patterns, risky populations, and shadow workflows likely to create operational or regulatory exposure. |
| **Workplace Cost Optimization Agent** | Detect the largest IT workplace cost optimization opportunities by correlating underused software, dormant devices, oversized hardware, VDI overconsumption, inactive SaaS licenses, underutilized infrastructure, recurring support costs, high-maintenance endpoints, and excessive refresh activity. Quantify potential savings, operational impact, sustainability benefits, and implementation complexity for each optimization opportunity. |
| **Workplace Personas & Segmentation Intelligence Agent** | Build intelligent workplace personas using collaboration patterns, mobility behavior, application usage, work schedules, endpoint usage, supported devices, communication intensity, SaaS adoption, and DEX trends. Identify the dominant workplace patterns across the organization, describe the operational needs and friction risks of each persona, and recommend personalization strategies, support models, and optimization initiatives tailored to each workstyle segment. |
| **Employee Journey Intelligence Agent** | Analyze employee onboarding, migrations, device refreshes, Windows transitions, office moves, and transformation journeys to identify the moments generating the greatest employee friction, operational inefficiency, or adoption resistance. Correlate DEX degradation, support activity, application exposure, collaboration quality, and endpoint readiness across journey stages to recommend improvements reducing disruption and accelerating employee productivity stabilization. |
| **Autonomous Remediation Confidence Agent** | Evaluate all major remediation automations currently in use and determine which are suitable for autonomous self-healing deployment. Correlate execution reliability, rollback history, targeting precision, operational risk, dependency sensitivity, user impact, and remediation success history to identify the automations with the highest confidence level for safe autonomous remediation. Recommend governance, phased rollout, and validation strategies for each candidate automation. |
| **IT Hygiene & Operational Health Agent** | Detect operational hygiene weaknesses reducing endpoint reliability, automation efficiency, observability quality, and long-term workplace stability. Correlate unhealthy collectors, outdated agents, endpoint instability, recurring failures, stale assets, obsolete configurations, remediation failures, and technical debt accumulation across the fleet. Prioritize the highest-impact operational hygiene improvements and identify populations most at risk of structural degradation. |
| **Change Communication Intelligence Agent** | Analyze Engage campaigns and IT communication effectiveness to determine which message types, channels, timing strategies, segmentation approaches, behavioral patterns, and engagement sequences generate the strongest employee participation, awareness, adoption, and operational outcomes. Identify communication fatigue patterns, ineffective targeting strategies, and underperforming campaigns, then recommend optimized communication strategies tailored to employee personas and transformation initiatives. |
| **Workforce Sentiment Inference Agent** | Identify the 10 most structurally unstable devices with the greatest negative impact on DEX, focusing on recurring and systemic degradation rather than isolated incidents. For each device, analyze both endpoint and application leaf scores while taking into account the DEX Score configuration and weighting for endpoint and application metrics. Correlate degraded scores with the applications actively used by the device’s primary user to determine which applications, binaries, or endpoint conditions contribute most to the low overall DEX score. Identify the primary root cause driving the degradation and, when crashes or freezes are involved, specify the main application, executable, or binary responsible along with the proportion of total crashes or freezes it represents. For each device, provide the device name, primary user, overall DEX score, most impacted leaf scores, contributing factors, likely root cause, and recommend 1 high-impact remediation action. Explain why the recommendation is relevant, the expected DEX improvement impact, whether the action is corrective, preventive, or optimization-oriented, and any important operational follow-up considerations. Rank the output from highest structural instability impact to lowest using concise, evidence-based, and operationally actionable explanations. |
| **DEX Intelligence & Governance Agent** | Conduct a comprehensive DEX Score governance and root cause analysis across the organization. Begin with a full score breakdown covering DEX Score, Technology Score, Endpoint Score, Applications Score, Collaboration Score, and Sentiment Score, explicitly explaining the weak-link relationship between parent and child scores rather than treating composites as simple averages. For every degraded score, drill down to the contributing subscores and leaf scores, identify the underlying metrics and technologies responsible, and quantify score impact using affected population size and business criticality. Benchmark results across countries, business entities, departments, hardware models, OS versions, and VDI versus physical populations to surface statistically meaningful outliers rather than noise. Cross-reference Technology Score against Sentiment Score and recent campaign responses to identify perception gaps and hidden frustrations not visible in technical telemetry. Perform a threshold governance review: for each active leaf score threshold, evaluate P50, P90, and P95 fleet distributions, determine whether Good, Average, and Frustrating bands still separate experiences meaningfully, and flag thresholds that have never been revisited or that appear over- or under-sensitive. Never recommend a threshold change solely to raise a score — justify every proposed change with evidence such as the percentage of healthy users currently misclassified. Assess score and sentiment coverage to warn where conclusions may be statistically weak due to missing telemetry or connector gaps. Conclude with an executive summary structured as: DEX Overview, Root Cause Analysis, Impact Analysis, Cohort Comparison, Trend Analysis, Governance Assessment, Recommended Actions, Expected DEX Improvement, and Risks and Considerations. |
| **DEX Intelligence & Governance Agent** | Before modifying any DEX score threshold, simulate the impact of the proposed change on the current population. For a given metric with a proposed change to the Average or Frustrating range start value, count how many users and devices currently fall into each band (Good, Average, Frustrating) using the current threshold, then count how many would move between bands with the new threshold, including the percentage change. Compute the estimated change in the composite score for the affected population, and the estimated change in the composite and leaf score for the involved branch, with analysis per country. State the Nexthink default threshold for this metric as a reference baseline, and flag that threshold changes are not retroactive — the new threshold applies from the next computation cycle only. Log the proposed change with date, metric, old value, new value, and justification. Present the simulation results and wait for explicit confirmation before applying the change. |

---

# Contributing

Contributions are welcome.

Please:

- Keep agents modular
- Use clear operational language
- Document assumptions
- Add examples whenever possible
- Avoid environment-specific hardcoding

---

# Disclaimer

This repository is community-driven and is not officially maintained by Nexthink.

Workspace IT Agents are currently part of Nexthink Technical Previews and may evolve over time. 
