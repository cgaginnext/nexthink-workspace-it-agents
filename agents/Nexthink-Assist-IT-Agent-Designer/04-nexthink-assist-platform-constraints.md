# 04 — Nexthink Assist Platform Constraints

Reference guide for the hard limits, unsupported capabilities, and behavioral
boundaries of Nexthink Assist Workspace that every agent designer must know.
Use this file to prevent designing agents or prompts that rely on features
the platform cannot deliver.

---

## 1. Why this file exists

Platform constraints are the most common source of agent design failures. An
agent that references custom fields in NQL, queries data beyond the retention
window, or promises to trigger a remote action will silently fail or produce
misleading results. This file documents every known constraint category with
its practical implication for agent and prompt design.

When in doubt about whether a capability exists, treat it as absent until
confirmed by the official Nexthink documentation or a live platform test.

---

## 2. Agent configuration limits

### 2.1 Role & Behavior character limit
- **Maximum:** 10,000 characters (including spaces and line breaks).
- **Recommended production range:** 7,000–9,500 characters.
- **Why the margin matters:** The Role & Behavior is processed at the start of
  every conversation. Content near the limit is more likely to be truncated or
  deprioritized by the model. Leave 500–1,000 characters of margin for future
  edits.
- **Measurement:** Count characters, not words. Markdown formatting characters
  (headers, bullets, backticks) count toward the limit.

### 2.2 Knowledge source file limits
- **Format:** Markdown (`.md`) only.
- **Recommended file size:** Under 10,000 characters per file for reliable
  retrieval. Larger files are not rejected but retrieval quality degrades as
  file size increases.
- **Recommended number of files:** 3–5 per agent. More files increase retrieval
  noise; fewer files force content density that reduces precision.
- **One topic per file:** A file covering multiple unrelated topics produces
  lower-quality retrieval than two focused files. The file name should
  accurately describe the single topic it covers.

---

## 3. Nexthink Assist — hard capability boundaries

These are things Nexthink Assist fundamentally cannot do, regardless of how
the prompt is written or how the agent is configured.

### 3.1 No write access
Nexthink Assist is read-only. It cannot:
- Create, modify, or delete any platform object (devices, users, packages,
  campaigns, monitors, remote actions, workflows, dashboards).
- Execute remote actions or workflows on devices.
- Change user permissions or system settings.
- Modify NQL data or any stored record.

**Design implication:** An agent that promises to "fix", "remediate", "deploy",
or "configure" something is overpromising. The correct framing is always
"identify", "recommend", "draft", or "generate" — then the human acts.

### 3.2 No access to external systems
Assist operates exclusively within the Nexthink platform boundary. It cannot:
- Query external APIs, databases, or ITSM systems directly.
- Access ServiceNow, Jira, Azure AD, Intune, or any third-party tool.
- Retrieve data from URLs, web pages, or external files at runtime.
- Send emails, Slack messages, or Teams notifications.

**Design implication:** An agent that references "check the ServiceNow ticket"
or "look up the Azure AD group" will fail. Cross-system workflows require a
Nexthink Workflow (Flow), not an Assist agent.

### 3.3 No custom field querying
Nexthink Assist does not currently support querying custom fields in NQL.
Custom fields are prefixed with `#` in the data model (e.g.,
`device.#business_unit`). Even if a custom field exists and is populated in
the instance, Assist will not reference it in generated NQL.

**Design implication:** Do not design agents or prompts that rely on custom
field values for filtering, grouping, or reporting. Use native NQL fields
(department, location, OS type, hardware model, etc.) instead. If a use case
genuinely requires custom fields, the user must write the NQL manually in
Investigations.

### 3.4 No NQL access to DEX and web application trends
DEX trends and web application trends are not queryable via NQL. They are
only available in the Digital Experience and Applications module dashboards.

Specifically, the following are **not** NQL-queryable:
- DEX trend dashboards (13-month history visible in the DEX module).
- Web application performance trends (90-day history in the Applications
  module).

What **is** NQL-queryable for DEX:
- `dex.scores` — daily DEX scores per device (30-day retention in NQL).
- `dex.application_scores` — daily per-application DEX scores.
- Raw event tables that feed DEX scores (e.g., `device_performance.boots`,
  `session.events`, `execution.crashes`).

**Design implication:** Prompts asking for "DEX trend over the last 6 months"
via NQL will return at most 30 days of `dex.scores` data. For longer trend
views, direct the user to the Digital Experience module dashboard.

### 3.5 No image or file generation
Assist can produce text, tables, markdown, and charts (via its chart tool).
It cannot:
- Generate PDF reports.
- Export data to CSV or Excel files.
- Produce images, diagrams, or slide decks.

**Design implication:** Agents designed for "automated reporting" produce
Workspace text responses, not downloadable files. If the user needs a
distributable report, they copy the Workspace output manually.

---

## 4. NQL constraints

### 4.1 Maximum query length
NQL queries are capped at **16,000 characters**. Queries that exceed this
limit will fail. This is rarely a concern for Assist-generated NQL, but
relevant when designing complex multi-join investigations.

### 4.2 Default result page size
NQL returns **50 rows by default**. Additional rows require pagination. Prompts
that ask for "all devices" in a large fleet may return a truncated result
without warning.

**Design implication:** For population-level prompts, ask for aggregated
counts or top-N results rather than full lists. A prompt asking for "all
10,000 devices with X" will return 50 rows.

### 4.3 Single-query window cap for high-resolution events
`execution.events`, `connection.events`, `connection.tcp_events`, and
`connection.udp_events` at high resolution are retained for **14 days** (not
30), and a single query can access a maximum of **8 days** of data at once.

**Design implication:** Prompts querying these tables with a "last 30 days"
window will silently return partial data. Cap these queries at 7 days to stay
within the 8-day single-query limit.

### 4.4 VDI high-resolution data
30-second high-resolution data for VDI environments is retained for only
**48 hours**. Prompts querying VDI performance at high resolution must use
a window of 48 hours or less.

### 4.5 Custom field NQL syntax
Custom fields use the `#` prefix (e.g., `device.#field_name`). Assist does
not generate NQL referencing custom fields. If a user asks Assist to filter
by a custom field, Assist will either ignore the filter or explain the
limitation — it will not invent a `#field_name` reference.

---

## 5. Data retention — complete reference

| Data type                              | Table(s)                                      | Retention        | Single-query cap |
|----------------------------------------|-----------------------------------------------|------------------|------------------|
| Inventory objects (devices, users,     | `devices`, `users`, `packages`, `binaries`    | 30 days from     | No cap           |
| packages, binaries)                    |                                               | last activity    |                  |
| Configuration objects (campaigns,      | `campaigns`, `remote_actions`, `workflows`,   | Indefinite       | N/A              |
| remote actions, workflows, monitors)   | `monitors`                                    |                  |                  |
| Operational events (general)           | `execution.crashes`, `device_performance.*`,  | 30 days          | No cap           |
|                                        | `remote_action.executions`, `web.events`,     |                  |                  |
|                                        | `alert.alerts`, `session.*`                   |                  |                  |
| High-resolution connection/execution   | `execution.events`, `connection.events`,      | 14 days          | 8 days per query |
| events                                 | `connection.tcp_events`,                      |                  |                  |
|                                        | `connection.udp_events`                       |                  |                  |
| VDI high-resolution performance        | VDI 30-second resolution tables               | 48 hours         | 48 hours         |
| DEX scores (NQL-queryable)             | `dex.scores`, `dex.application_scores`        | 30 days          | No cap           |
| Software metering trends               | `software_metering.events`                    | 90 days          | No cap           |
| Web application trends                 | Dashboard only — not NQL-queryable            | 90 days          | N/A              |
| Remote action execution summary        | `remote_action.executions_summary`            | 13 months        | No cap           |
| Workflow execution summary             | `workflow.executions_summary`                 | 13 months        | No cap           |
| Custom trends                          | `custom_trend.*`                              | 13 months        | No cap           |
| DEX trends                             | Dashboard only — not NQL-queryable            | 13 months        | N/A              |
| AI Tools interactions summary          | `ai.interactions_summary`                     | 13 months        | No cap           |
| Campaign responses                     | `campaign.responses`                          | Indefinite       | No cap           |
| Agent conversations (metadata)         | `agent.conversations`                         | 13 months        | 90 days per query|
| Workspace conversation history         | N/A (UI only)                                 | 30 days from     | N/A              |
|                                        |                                               | last activity    |                  |

---

## 6. Platform object limits — relevant to agent design

| Object type              | Limit                                                      |
|--------------------------|------------------------------------------------------------|
| Scheduled remote actions | 100 active (manually created or from Library)              |
| Remote action schedules  | 10 per remote action; 10 RAs with frequency < 1 hour       |
| Remote action script     | 600 KB max; 50 inputs (max 30 KB total); 50 outputs        |
| Workflows (active)       | 2 active workflows (Core license); varies by license tier  |
| Webhooks                 | 50                                                         |
| Manual custom fields     | 100                                                        |
| Computed custom fields   | 200                                                        |
| Rule-based custom fields | 200                                                        |
| Software metering objects| 203 (depends on Applications module configuration)         |
| NQL query max length     | 16,000 characters                                          |
| NQL API (small queries)  | 50,000 calls/day; 1,000 records/call; 1 query/second       |
| Web transactions         | Unlimited per application; < 1,000 total across all apps   |
| Enrichment API           | 10,000 objects per cell; 64 characters per field value     |
| Image upload (Workspace) | Max 5 images per message; max 3 MB per image; JPEG/PNG     |

**Note on Workflows:** The active workflow limit varies by license tier
(Core: 2, Ultimate: higher). An agent recommending workflow creation should
note that the user must verify their available quota before proceeding.

---

## 7. Assist behavioral constraints

### 7.1 Assist does not invent NQL
Assist generates NQL from natural language using its internal data model
awareness. Agent instructions and knowledge source files should never include
NQL code blocks intended for direct execution. Describe the intent in natural
language; let Assist generate the query.

### 7.2 Assist respects RBAC
Assist only returns data the authenticated user is entitled to see, based on
their Role-Based Access Control (RBAC) permissions and view domain. An agent
cannot bypass RBAC by crafting a specific prompt. Generic agents deployed
across users with different RBAC scopes will produce different results for
the same prompt — this is expected and correct behavior.

### 7.3 No state retention between conversations
Each Workspace conversation is independent. Scheduled tasks run in isolated
contexts — they do not accumulate state across executions. Scheduled task
instructions must be fully self-contained and cannot reference "the results
from last week's run."

### 7.4 Conversation history retention
Workspace conversations are saved for **30 days from the last activity**.
Returning to a conversation resets the 30-day period. Agents that produce
outputs intended for long-term reference should instruct the user to copy
the result — Workspace is not a persistent record store.

### 7.5 AI output requires verification
AI-generated content is marked with the ✦ sparkles icon. Agents should not
be designed to produce outputs that are acted upon without human review —
especially for remediation recommendations, compliance assessments, or
security decisions.

---

## 8. Constraints by agent design scenario

| Scenario                                      | Relevant constraints                                      |
|-----------------------------------------------|-----------------------------------------------------------|
| Agent queries custom org structure            | §3.3 — custom fields not supported; use native fields     |
| Agent reports on 6-month DEX trend            | §3.4 — DEX trends not NQL-queryable; cap at 30 days       |
| Agent monitors connectivity every hour        | §4.3 — high-res events: 14-day retention, 8-day cap       |
| Agent recommends creating a workflow          | §6 — verify active workflow quota (Core: 2)               |
| Agent produces a downloadable report          | §3.5 — no file generation; output is Workspace text only  |
| Agent cross-references a ServiceNow ticket    | §3.2 — no external system access                          |
| Agent runs a remote action on a device        | §3.1 — Assist is read-only; recommend, don't execute      |
| Scheduled task references previous run output | §7.3 — no state retention between conversations           |
| Agent targets users by RBAC group             | §7.2 — RBAC is enforced; results vary by user permissions |
| Agent queries "all devices" in large fleet    | §4.2 — 50-row default; use aggregated counts instead      |
| Role & Behavior approaching 10,000 chars      | §2.1 — leave 500–1,000 char margin; risk of truncation    |
| Knowledge source file growing large           | §2.2 — keep under 10,000 chars; split by topic if needed  |

---

## 9. Constraints that may change

The following are current as of the file's last update and subject to change.
Verify against official Nexthink documentation before relying on them for a
production agent design.

- Custom field querying in Assist (§3.3) — current limitation; may be added
  in a future release.
- DEX trends NQL availability (§3.4) — currently dashboard-only.
- Active workflow limit (§6) — varies by license tier; confirm with the
  customer's Nexthink representative.
- Agent conversation retention (§5) — 13-month metadata retention; raw
  conversation messages retained ~30 days, viewable only via a separate UI.

---

*This file is a knowledge source for the Nexthink Assist Agent Designer agent.
It does not contain live data and does not reference any specific customer
environment. Verify all limits against the official Nexthink Infinity
thresholds documentation before use in a production context.*
```

---

**Différences par rapport à la version précédente :**
- Section 2 ajoutée avec les deux sous-sections manquantes (§2.1 Role & Behavior limit, §2.2 Knowledge source file limits).
- Toutes les sections suivantes renumérotées en conséquence (3, 4, 5, 6, 7, 8, 9).
- Le tableau de mapping scénarios (§8) enrichi de deux lignes couvrant les nouveaux cas Role & Behavior et knowledge source.

**Compte de caractères estimé : ~9 800 caractères** — dans la marge recommandée pour un fichier de knowledge source.