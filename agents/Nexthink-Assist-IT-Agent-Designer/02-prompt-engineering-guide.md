# 02 — Prompt Engineering Guide

Reference guide for writing effective prompts in Nexthink Assist Workspace:
anatomy, patterns, reformulation, templates vs. live prompts, and annotated
examples.

---

## 1. What makes a prompt effective?

A prompt in Nexthink Assist Workspace is a natural-language instruction that
drives a specific, repeatable output. The quality of the response is almost
entirely determined by the quality of the prompt. Nexthink Assist is not a
search engine — it reasons, retrieves data, and synthesizes. A vague prompt
produces a vague synthesis. A precise prompt produces a precise, actionable
result on the first try.

The four properties of an effective prompt:

1. **Specificity** — the intent is unambiguous. There is exactly one reasonable
   interpretation.
2. **Completeness** — all the information Assist needs to answer is present in
   the prompt (entity names, time scope, output format, thresholds).
3. **Single intent** — the prompt asks for one thing. Compound prompts produce
   compound, harder-to-act-on responses.
4. **Actionability** — the expected output is something the user can act on
   directly (a list, a decision, a draft, a recommendation with evidence).

---

## 2. Anatomy of a prompt

Every effective prompt contains four components. Not all four need to be
explicit sentences — some can be embedded in a single well-formed question —
but all four concerns must be addressed.

### 2.1 Context
Who is asking, from what perspective, and why. This is especially important
when the prompt will be reused by different users or run as a scheduled task.

> "As a DEX Analyst preparing a weekly executive summary…"
> "In the context of a post-incident review for a Teams outage on 2026-07-01…"

For one-off interactive prompts, context can be implicit if the conversation
already established it. For scheduled tasks and reusable templates, context
must always be explicit.

### 2.2 Task
The specific action or output requested. Use a verb that describes the output
type: *list*, *summarize*, *compare*, *identify*, *draft*, *explain*, *count*.

Avoid: *tell me about*, *look into*, *check*, *help me with*. These are
conversation starters, not task definitions.

| Weak verb       | Strong replacement                          |
|-----------------|---------------------------------------------|
| Tell me about   | Summarize / Explain / Describe              |
| Look into       | Identify / Analyze / Investigate            |
| Check           | Verify / List / Count / Flag                |
| Help me with    | Draft / Generate / Recommend                |

### 2.3 Constraints
Scope limits that prevent the response from being too broad. The three most
important constraint types:

- **Entity scope** — which devices, users, applications, locations, or
  departments. Use exact names or explicit filters (e.g., "devices in the
  Finance department", "the application Microsoft Teams").
- **Time scope** — an explicit window. Never leave time scope implicit.
  Relative expressions are acceptable ("last 7 days", "past 24 hours") but
  must be unambiguous. Fixed dates are preferable for non-recurring prompts.
- **Threshold** — a numeric criterion that defines what counts as notable
  (e.g., "crash rate > 5 per day", "free disk space < 10 GB", "DEX score
  below 40").

### 2.4 Output format
The shape of the expected response. Specify this explicitly when the default
narrative summary is not what you need.

Common output formats in Nexthink Assist:
- **Table** — for comparisons across multiple entities or metrics.
- **Bullet list** — for enumerations without ranking.
- **Ranked list** — for top-N results (e.g., "top 10 devices by crash count").
- **Markdown artifact** — for drafts (Role & Behavior, campaign text, task
  instruction).
- **Chart** — for trend visualization (specify the chart type if relevant:
  line, bar, pie).
- **Narrative summary** — for executive-level synthesis with key findings
  called out.

---

## 3. The five failure patterns

### Failure 1 — Vague intent
The prompt does not specify what output is expected. Assist produces a generic
overview instead of a targeted answer.

❌ `Tell me about DEX scores.`

✅ `Summarize the organization-wide DEX score for the last 30 days, broken
down by subscore (Endpoint, Applications, Collaboration, Sentiment). Identify
the subscore with the largest week-over-week decline and list the top 3
contributing factors.`

---

### Failure 2 — Missing time scope
No time window is specified. Assist defaults to a window that may not match
the user's intent, and the result is not reproducible.

❌ `List devices with high CPU usage.`

✅ `List devices where CPU usage exceeded 90% for more than 30 minutes during
the last 7 days. Show device name, department, and the peak CPU usage value.`

---

### Failure 3 — Pronoun and reference ambiguity
The prompt uses pronouns ("my", "this", "the user") or partial names that
Assist cannot resolve without additional context.

❌ `What happened on my device yesterday?`

✅ `List all notable events (crashes, high CPU, connectivity failures) recorded
on the device LAPTOP-FR-04821 on 2026-07-06.`

❌ `Show me Teams issues for the Paris office.`

✅ `Identify devices in the Paris location where Microsoft Teams reported
audio or video quality issues in the last 14 days. Group results by issue
type and show the number of affected users per type.`

---

### Failure 4 — Multi-intent overload
The prompt combines two or more distinct questions. Assist attempts to answer
all of them, producing a long response where each part is shallower than it
would be if asked separately.

❌ `Give me the DEX score trend, list all active alerts, show me which
applications are crashing the most, and tell me what remote actions I should
run.`

✅ Split into four separate prompts, run sequentially or in parallel:
1. `Summarize the DEX score trend for the last 30 days by subscore.`
2. `List all active alerts triggered in the last 24 hours, grouped by severity.`
3. `Rank the top 5 applications by crash count over the last 7 days.`
4. `Recommend remote actions to address the top application crash issue
   identified above.`

**Rule:** One prompt, one intent. If you need the output of one prompt to
inform the next, run them sequentially. If they are independent, they can run
in parallel.

---

### Failure 5 — Hand-written NQL in the prompt
The user includes NQL syntax directly in the prompt, either to guide Assist or
to ask it to "run this query." This is unreliable: hand-written NQL may
contain syntax errors, reference non-existent fields, or use incorrect enum
values.

❌ `Run this NQL: devices | where os.type == "windows" | list name, os.version`

✅ `List all Windows devices with their OS version. Show device name and OS
version, sorted by OS version ascending.`

Nexthink Assist generates and validates NQL internally from natural-language
descriptions. The user never needs to write NQL in a prompt.

---

## 4. Templates vs. live prompts

### Live prompt
A fully specified, immediately executable instruction. Every entity, time
scope, threshold, and output format is concrete. Can be run as-is without
modification.

Example:
> `List the top 10 devices by number of application crashes in the last 7 days.
> Show device name, primary user, department, and total crash count. Sort by
> crash count descending.`

### Template
A reusable pattern with `<placeholders>` the user fills in before running.
Templates are not executable as-is. Always label them clearly as templates.

Example:
> `List the top <N> devices by number of <metric> in the last <time_window>.
> Show device name, primary user, department, and <metric> value. Sort by
> <metric> descending.`

**When to use each:**
- Use a **live prompt** for a specific, one-time analysis or for a scheduled
  task (which must be fully self-contained).
- Use a **template** in documentation, knowledge source files, or onboarding
  guides where the user will adapt the prompt to their context.

**Never present a template as a live prompt.** A template with unfilled
placeholders will produce an error or a nonsensical result if run directly.

---

## 5. Prompt length and density

Effective prompts are typically **50–200 words**. This range covers the four
components (context, task, constraints, output format) without padding.

| Length          | Typical use case                                      |
|-----------------|-------------------------------------------------------|
| < 30 words      | Simple lookups ("List all devices with < 10 GB free disk space in the last 24h") |
| 50–150 words    | Standard analytical prompts with full constraints     |
| 150–300 words   | Complex multi-step analyses or artifact generation    |
| > 300 words     | Rarely justified — consider moving context to a knowledge source file |

When a prompt exceeds 300 words, ask: is the extra content context that belongs
in a knowledge source file, or is it genuinely part of the instruction? If it
is context, move it to a file. If it is instruction, the prompt may be trying
to do too much — split it.

---

## 6. Prompts for artifact generation

When the expected output is a Nexthink artifact (Role & Behavior draft, campaign
text, task instruction, NQL explanation), the prompt structure shifts slightly:

- **Context** becomes more important: specify the target audience, the
  deployment environment (generic vs. instance-specific), and any constraints
  the artifact must respect.
- **Task** should name the artifact type explicitly: "Draft a Role & Behavior
  for…", "Write a campaign notification for…", "Generate a scheduled task
  instruction for…"
- **Constraints** should include format requirements: character limits, section
  structure, tone.
- **Output format** should specify whether you want the artifact alone, the
  artifact plus a rationale, or the artifact plus a character count.

Example — Role & Behavior generation prompt:
> `Draft a Role & Behavior for a Nexthink Assist IT agent specialized in
> onboarding new DEX Analysts. The agent should guide users through their first
> 30 days with Nexthink Infinity: understanding the data model, running their
> first investigations, and interpreting DEX scores. The agent is generic
> (cross-instance). Target audience: technically fluent but new to Nexthink.
> Follow the six-section structure (identity, audience, capabilities,
> out-of-scope, reasoning standards, platform constraints). Target
> approximately 8,500 characters. Report the character count at the end.`

---

## 7. Prompts for data analysis — quick reference

The following patterns cover the most common Nexthink Assist data analysis
use cases. Each is a live prompt template — fill in the `<placeholders>`
before use.

### Device health
> `List devices where <metric> exceeded <threshold> for more than <duration>
> in the last <time_window>. Show device name, department, location, and
> <metric> value. Sort by <metric> descending. Flag any device that also
> appears in active alerts.`

### Application performance
> `Identify the top <N> applications by <metric> (crash count / freeze count /
> high CPU usage) over the last <time_window>. For each application, show the
> number of affected devices, the number of affected users, and the trend
> compared to the previous equivalent period.`

### DEX score analysis
> `Summarize the DEX score for <scope: organization / department / location>
> over the last <time_window>. Break down by subscore (Endpoint, Applications,
> Collaboration, Sentiment). Identify the subscore with the largest decline
> and list the top contributing events.`

### Connectivity issues
> `List devices that experienced <connectivity issue type: Wi-Fi drops /
> VPN failures / TCP failures> more than <N> times in the last <time_window>.
> Show device name, location, user, and event count. Recommend the most
> relevant remote action to investigate further.`

### Software inventory
> `List all devices where <application or package name> is installed. Show
> device name, installed version, and last seen date. Highlight devices running
> a version older than <version_threshold>.`

---

## 8. Prompts for scheduled tasks — specific rules

Prompts used in scheduled tasks have additional constraints because they run
without human context. See `03-scheduled-tasks-reference.md` for the full
guide. Key rules that apply at the prompt level:

- **No pronouns, no implicit context.** Every entity must be named or scoped
  explicitly.
- **Relative time expressions only.** Use "last 24 hours", "past 7 days" —
  never fixed dates, which become stale.
- **Include an action trigger.** End the prompt with a clear signal: "Flag any
  result that exceeds the threshold" or "If no devices are found, state that
  explicitly."
- **Specify the output format.** A scheduled task that produces an unstructured
  wall of text is not actionable. Request a table, a ranked list, or a
  structured summary.

---

## 9. Prompt review checklist

Before finalizing any prompt, verify:

- [ ] The intent is unambiguous — there is exactly one reasonable
      interpretation.
- [ ] An explicit time scope is present.
- [ ] All entity references are exact names or explicit filters (no pronouns,
      no partial names).
- [ ] The prompt contains exactly one intent.
- [ ] No hand-written NQL appears in the prompt.
- [ ] The output format is specified (or the default narrative is acceptable).
- [ ] If the prompt is a template, it is labeled as such and no placeholder
      is left unfilled before running.
- [ ] If the prompt will be used in a scheduled task, it passes the four
      scheduled-task rules above.

---

*This file is a knowledge source for the Nexthink Assist Agent Designer agent.
It does not contain live data and does not reference any specific customer
environment.*
```

---
