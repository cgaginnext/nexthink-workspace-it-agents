# 01 — Agent Design Patterns

Reference guide for designing Nexthink Assist IT agents: structure, patterns,
anti-patterns, and validation checklist.

---

## 1. What is a Nexthink Assist IT agent?

A Nexthink Assist IT agent is a specialized configuration of the Nexthink Assist
AI, deployed within Workspace. It is defined by:

- A **Role & Behavior** — a text block (up to 10,000 characters) that instructs
  the agent on its identity, scope, tone, capabilities, and limits.
- One or more **knowledge source files** — markdown documents attached to the
  agent that provide domain-specific reference material the agent can retrieve
  during a conversation.

An IT agent is not a general-purpose assistant. Its value comes from its
constraints: a well-scoped agent produces more precise, more consistent, and
more trustworthy outputs than a broad one.

---

## 2. Role & Behavior — structure

Every Role & Behavior should follow this six-section structure. Sections can be
combined or split as needed, but all six concerns must be addressed.

### 2.1 Identity and purpose
One concise paragraph. Answer three questions:
- What is this agent?
- Who does it serve?
- What is the one thing it does better than standard Workspace?

Keep it under 150 words. This section sets the agent's "north star" — every
other section should be consistent with it.

### 2.2 Audience and tone
Describe the target user's technical level and expectations. Specify:
- How technical the language should be (e.g., "users are fluent in NQL and DEX
  concepts" vs. "users are non-technical employees").
- The appropriate register (professional, concise, instructional, empathetic).
- Whether the agent should explain its reasoning or just deliver results.

### 2.3 Core capabilities
List 2–4 things the agent does well. For each capability:
- Name it clearly.
- Describe the behavior in enough detail that the agent knows *how* to execute
  it, not just *what* it is.
- Include decision rules where relevant (e.g., "ask one clarifying question
  before producing a full artifact if the scope is ambiguous").

Avoid listing more than 4 capabilities. An agent with 8 capabilities is an
agent with no focus.

### 2.4 Out-of-scope behavior
Explicitly list what the agent declines. For each out-of-scope item:
- State what it is.
- Provide a one-sentence redirect to the right tool or surface.

This section is as important as the capabilities section. An agent that tries
to answer everything answers nothing well.

### 2.5 Reasoning and output standards
Define how the agent structures its responses:
- Does it ask clarifying questions before producing output? Under what
  conditions?
- What output formats does it use (markdown, bullet lists, tables, code blocks)?
- How does it handle uncertainty? (e.g., "state the assumption explicitly rather
  than guessing")
- Does it report metadata about its output? (e.g., character counts for Role &
  Behavior drafts, confidence levels for recommendations)

### 2.6 Platform constraints
List the Nexthink-specific technical limits the agent must respect and enforce.
Examples:
- Role & Behavior character limit: 10,000 characters.
- NQL: never hand-write NQL; always describe intent in natural language.
- Unsupported fields: custom fields, custom trends, specific remote action
  outputs, specific campaign responses.
- Data retention: events 30 days, trends 90 days–13 months.
- Scheduled task minimum interval: 1 hour.

---

## 3. Scope design — the most important decision

The scope of an agent determines everything else. A poorly scoped agent is the
single most common failure mode in agent design.

### 3.1 The scope matrix

Use this matrix to position an agent before writing a single line of Role &
Behavior:

| Dimension        | Narrow ←————————————→ Broad         |
|------------------|--------------------------------------|
| Domain           | One topic (e.g., NQL authoring)      | Multiple topics (e.g., full DEX) |
| Audience         | One role (e.g., DEX Analyst)         | All IT users |
| Instance scope   | Generic (cross-customer)             | Customer-specific |
| Action scope     | Read-only / advisory                 | Includes execution triggers |
| Knowledge depth  | Deep on one subject                  | Shallow on many |

**Rule:** An agent should be narrow on at least three of these five dimensions.
If it is broad on four or five, split it into two agents.

### 3.2 Generic vs. instance-specific agents

**Generic agents** (cross-instance):
- Do not reference specific customer data, device names, or environment
  configurations.
- Rely on knowledge source files for domain content rather than hardcoded
  examples.
- Are portable: the same agent can be deployed across multiple Nexthink
  instances without modification.
- Require the Role & Behavior to explicitly state: "This agent does not query
  live data. It provides guidance, drafts, and frameworks."

**Instance-specific agents**:
- May reference specific NQL patterns, department names, or environment
  conventions relevant to one customer.
- Require maintenance when the customer's environment changes.
- Should include a "last reviewed" date in the Role & Behavior or a knowledge
  source file to signal staleness.

### 3.3 Defining the out-of-scope boundary

For every capability you include, ask: "What is the adjacent thing this agent
should *not* do?" Document that boundary explicitly.

Examples of well-drawn boundaries:
- "This agent designs campaigns but does not trigger or send them."
- "This agent explains NQL patterns but does not execute queries against live
  data."
- "This agent recommends remote actions but does not execute them."

---

## 4. Common design patterns

### Pattern A — The Specialist Advisor
**Use when:** The domain is deep and technical; the user needs expert guidance,
not data retrieval.
**Structure:** Heavy on reasoning standards and output formats; light on
platform constraints. Knowledge sources contain reference material (guides,
patterns, examples).
**Example agents:** NQL authoring assistant, campaign design advisor, DEX score
interpretation guide.

### Pattern B — The Workflow Companion
**Use when:** The agent supports a repeatable process (e.g., onboarding a new
Nexthink instance, running a quarterly DEX review).
**Structure:** Core capabilities map to process steps. Each step has a defined
input, a defined output, and a clear handoff to the next step or to a human.
**Example agents:** Nexthink instance setup guide, DEX quarterly review
assistant.

### Pattern C — The Meta-Agent (this agent's pattern)
**Use when:** The agent's subject matter *is* agent design, prompt engineering,
or platform configuration — not IT operations.
**Structure:** Capabilities focus on producing artifacts (Role & Behavior drafts,
prompts, task instructions). Out-of-scope section is critical: the agent must
firmly redirect operational requests to standard Workspace.
**Key risk:** Scope creep. Users will try to use a meta-agent for operational
queries. The out-of-scope section must be explicit and the redirect must be
immediate.

### Pattern D — The Scoped Data Analyst
**Use when:** The agent is allowed to query live data but only within a defined
domain (e.g., "only security-related metrics", "only collaboration tool
performance").
**Structure:** Capabilities include data retrieval with explicit scope filters.
Platform constraints section must list which tables and metrics are in scope.
**Key risk:** Query sprawl. Without explicit scope limits, the agent will answer
any data question. Use the out-of-scope section to name the tables and metrics
that are off-limits.

---

## 5. Anti-patterns

### Anti-pattern 1 — The Everything Agent
**Symptom:** The Role & Behavior lists 8+ capabilities covering unrelated
domains (DEX analysis, campaign creation, NQL authoring, onboarding, security,
etc.).
**Problem:** The agent has no coherent identity. It produces mediocre answers
across all domains instead of excellent answers in one.
**Fix:** Split into 2–3 focused agents. Use a "router" prompt in standard
Workspace to direct users to the right agent.

### Anti-pattern 2 — The Vague Identity
**Symptom:** The identity section says something like "This agent helps IT teams
with Nexthink."
**Problem:** The agent cannot prioritize between competing requests. It defaults
to generic answers.
**Fix:** Rewrite the identity section to answer: what specifically, for whom,
and better than what alternative.

### Anti-pattern 3 — The Missing Boundary
**Symptom:** The out-of-scope section is absent or contains only one line
("This agent does not answer non-Nexthink questions").
**Problem:** The agent attempts to answer everything, producing low-quality
responses outside its domain and eroding user trust.
**Fix:** For every capability, write one corresponding out-of-scope item. The
out-of-scope section should be roughly as long as the capabilities section.

### Anti-pattern 4 — The Hardcoded Instance
**Symptom:** The Role & Behavior contains specific device names, department
names, NQL field values, or customer-specific thresholds.
**Problem:** The agent breaks or produces misleading answers when deployed in a
different environment.
**Fix:** Move all instance-specific content to a knowledge source file labeled
clearly as environment-specific. Keep the Role & Behavior generic.

### Anti-pattern 5 — The Overlong Role & Behavior
**Symptom:** The Role & Behavior is at or near the 10,000-character limit, with
long paragraphs repeating the same instructions in different words.
**Problem:** Redundancy dilutes signal. The agent struggles to prioritize
instructions when they are repeated with slight variations.
**Fix:** Edit ruthlessly. Each sentence should add a constraint or a behavior
not already stated. Target 8,500–9,000 characters for a production agent,
leaving margin for future edits.

### Anti-pattern 6 — The Implicit Constraint
**Symptom:** The agent is expected to avoid certain behaviors (e.g., never
hand-write NQL, never execute remote actions) but this is not stated in the
Role & Behavior.
**Problem:** The agent will occasionally violate the implicit constraint,
especially under user pressure.
**Fix:** State every constraint explicitly in the Platform Constraints section.
If it matters, write it down.

---

## 6. Validation checklist

Run this checklist before finalizing any Role & Behavior.

### Scope
- [ ] The identity section answers: what, for whom, and better than what.
- [ ] The agent is narrow on at least 3 of the 5 scope matrix dimensions.
- [ ] Every capability has a corresponding out-of-scope boundary.
- [ ] The agent is either explicitly generic or explicitly instance-specific
      (not ambiguous).

### Structure
- [ ] All six sections are present (identity, audience, capabilities,
      out-of-scope, reasoning standards, platform constraints).
- [ ] No capability is listed without behavioral guidance (not just a name).
- [ ] The out-of-scope section includes a redirect for each declined request.

### Technical
- [ ] Character count is between 7,000 and 9,500 (production range).
- [ ] No hand-written NQL appears anywhere in the Role & Behavior.
- [ ] All Nexthink platform constraints are listed explicitly.
- [ ] No instance-specific content is hardcoded (device names, department names,
      customer thresholds).

### Knowledge sources
- [ ] Each attached knowledge source file covers exactly one topic.
- [ ] The Role & Behavior does not duplicate content already in a knowledge
      source file.
- [ ] Knowledge source files are in markdown format.
- [ ] The number of attached files is between 3 and 5.

---

## 7. Quick reference — Role & Behavior template

The following is a structural template. Replace every `<placeholder>` with
real content before use. Do not use this template as-is.

```
# Role & Behavior — <Agent Name>

## Identity and purpose
<One paragraph: what the agent is, who it serves, what it does better than
standard Workspace.>

## Audience and tone
<Target user profile, technical level, register, reasoning visibility.>

## Core capabilities

### <Capability 1 name>
<Behavioral description, decision rules, output format.>

### <Capability 2 name>
<Behavioral description, decision rules, output format.>

### <Capability 3 name> (optional)
<Behavioral description, decision rules, output format.>

## Out-of-scope behavior
- **<Declined request type>:** <One-sentence redirect.>
- **<Declined request type>:** <One-sentence redirect.>
- **<Declined request type>:** <One-sentence redirect.>

## Reasoning and output standards
<Clarification rules, output formats, uncertainty handling, metadata reporting.>

## Platform constraints
<Explicit list of Nexthink technical limits the agent enforces.>
```

---

*This file is a knowledge source for the Nexthink Assist Agent Designer agent.
It does not contain live data and does not reference any specific customer
environment.*
```

---