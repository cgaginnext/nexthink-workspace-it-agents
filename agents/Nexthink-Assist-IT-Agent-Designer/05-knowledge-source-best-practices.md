# 05 — Knowledge Source Best Practices

Guide for writing, structuring, and maintaining knowledge source files
for Nexthink Assist agents. A knowledge source is only as useful as its
retrievability — content that exists but cannot be found at query time
is dead weight.

---

## 1. What a knowledge source is (and is not)

A knowledge source file is a Markdown document attached to a Nexthink
Assist agent. When a user sends a message, the agent retrieves the most
relevant chunks from its knowledge source files and uses them to ground
its response.

**A knowledge source is:**
- A reference document the agent consults at query time.
- A way to extend the agent's behavior beyond what the Role & Behavior
  can hold within its 10,000-character limit.
- A precision tool: the more focused the file, the more reliably it is
  retrieved for the right query.

**A knowledge source is not:**
- A prompt. Instructions belong in the Role & Behavior, not in knowledge
  source files. A file that says "always respond in French" will not
  reliably change agent behavior — it may or may not be retrieved.
- A database. Knowledge sources are static documents. They do not update
  automatically and do not contain live data.
- A substitute for the Role & Behavior. Core persona, tone, scope, and
  behavioral rules must live in the Role & Behavior. Knowledge sources
  supply reference content, not behavioral instructions.
- A catch-all. A single file covering ten unrelated topics will be
  retrieved inconsistently. Split by topic.

---

## 2. The retrieval model — how content is found

Understanding how retrieval works is the prerequisite for writing files
that actually get used.

### 2.1 Chunk-based retrieval
The agent does not read knowledge source files in full at query time. It
splits each file into chunks (typically a few hundred tokens each) and
retrieves the chunks most semantically similar to the user's query.

**Implication:** A file can be 8,000 characters long, but if the relevant
answer is buried in the middle of a dense paragraph, it may not be
retrieved. Structure content so that each chunk is self-contained and
answers a specific question.

### 2.2 Semantic similarity, not keyword matching
Retrieval is based on meaning, not exact words. A user asking "why is my
laptop slow" can retrieve a chunk about "device performance degradation
causes" even if the words "laptop" and "slow" do not appear in the chunk.

**Implication:** Write in plain, descriptive language. Avoid jargon-only
headers. A section titled "§3.4 — MTTR optimization vectors" is harder
to retrieve than "How to reduce mean time to resolution for device issues."

### 2.3 Chunk boundaries follow document structure
Chunks are typically split at section boundaries (headers, blank lines,
list breaks). A section that runs 2,000 characters without a break will
be split arbitrarily, potentially cutting a concept in half.

**Implication:** Keep sections short. Aim for 300–600 characters per
logical unit. Use headers to create natural chunk boundaries.

### 2.4 File name contributes to retrieval context
The file name is included in the retrieval context. A file named
`03-scheduled-tasks-reference.md` is more likely to be retrieved for
queries about scheduled tasks than a file named `misc-notes.md`.

**Implication:** Name files descriptively and specifically. The name
should match the dominant topic of the file, not the project it belongs
to.

---

## 3. File structure rules

### 3.1 One topic per file
Each file should cover exactly one topic. If a file covers both
"data retention limits" and "NQL query patterns," queries about
retention may retrieve NQL content and vice versa — reducing precision.

**Test:** Can you describe the file's topic in five words or fewer?
If not, split it.

### 3.2 Lead with the most retrievable content
Put the most query-relevant content at the top of each section. The
first 100 characters of a chunk carry more retrieval weight than the
last 100. A section that opens with context-setting prose before
reaching the actual answer will be retrieved less reliably than one
that leads with the answer.

**Pattern to follow:**
```
### [Descriptive header that matches likely query phrasing]
[Answer or key fact — one sentence]
[Supporting detail — 2–4 sentences or a short list]
```

**Pattern to avoid:**
```
### Background
In order to understand the following, it is important to first
consider the historical context of how Nexthink evolved...
[Answer buried 200 characters later]
```

### 3.3 Use headers as retrieval anchors
Every distinct concept should have its own header. Headers are the
primary chunk boundary signal. A file with three headers and 8,000
characters of prose will produce large, imprecise chunks. A file with
fifteen focused headers will produce small, precise chunks.

Recommended header depth: H2 for major sections, H3 for individual
concepts. Avoid H4 and deeper — they add visual noise without improving
retrieval.

### 3.4 Lists over paragraphs for enumerable facts
When content is a set of facts, limits, rules, or steps, use a bulleted
or numbered list rather than prose. Lists are chunked more cleanly and
scanned more reliably by the retrieval model.

**Prose (harder to retrieve precisely):**
The retention period for operational events is 30 days, while
high-resolution connection events are retained for only 14 days with
a single-query cap of 8 days, and VDI data is retained for 48 hours.

**List (easier to retrieve precisely):**
- Operational events: 30-day retention, no query cap.
- High-resolution connection events: 14-day retention, 8-day query cap.
- VDI high-resolution data: 48-hour retention.

### 3.5 Tables for structured reference data
Use Markdown tables for data that has two or more dimensions (e.g.,
object type + limit, scenario + constraint, metric + threshold). Tables
are retrieved as a unit and present well in Workspace responses.

Keep tables narrow — three to four columns maximum. Wide tables with
many columns are harder to parse and may be truncated in the response.

### 3.6 Avoid redundancy across files
If the same fact appears in three files, the agent may retrieve all
three chunks and present conflicting or repetitive information. Each
fact should live in exactly one file. Cross-reference by file name
when needed:

```
For retention limits, see 04-nexthink-assist-platform-constraints.md §5.
```

---

## 4. Content quality rules

### 4.1 Write for the query, not the author
The author knows the full context. The retrieval model does not. Write
each section as if the reader has no prior context — because the chunk
may be retrieved in isolation, without the surrounding sections.

**Bad (assumes context):**
```
As mentioned above, the limit is 10,000 characters.
```

**Good (self-contained):**
```
The Role & Behavior character limit is 10,000 characters (including
spaces and Markdown formatting).
```

### 4.2 Use the vocabulary the user will use
Retrieval is semantic, but vocabulary alignment still improves precision.
If users will ask about "slow devices," include the phrase "slow device"
or "device performance degradation" in the relevant section — not just
"endpoint resource contention."

Write a brief vocabulary note at the top of files covering technical
topics:

```
> **Also known as:** device slowness, endpoint lag, high CPU/RAM,
> performance issues.
```

### 4.3 Date-stamp volatile content
Any content that may change (platform limits, feature availability,
retention periods) should carry a last-verified date. This prevents
the agent from confidently citing outdated information.

```
> **Last verified:** 2026-07-07. Check official Nexthink documentation
> before relying on these values in a production context.
```

### 4.4 Distinguish facts from recommendations
Use clear markers to separate documented facts from design
recommendations. Mixing them produces responses where the agent
presents an opinion as a platform constraint.

- **Fact:** "NQL returns 50 rows by default."
- **Recommendation:** "For population-level queries, use aggregated
  counts rather than full lists."

Use bold labels (`**Fact:**`, `**Recommendation:**`, `**Design
implication:**`) to make the distinction explicit in the file.

### 4.5 No NQL code blocks in knowledge source files
Knowledge source files should never contain NQL code blocks intended
for direct execution. Assist generates NQL from natural language — it
does not execute NQL found in knowledge sources. Including NQL creates
confusion: the agent may quote the code block as if it were a valid
query without validating it.

If a query pattern needs to be documented, describe it in natural
language:

**Avoid:**
```nql
devices | where os.type == Windows | list name, os.version
```

**Use instead:**
```
To list Windows devices with their OS version, ask Assist to retrieve
all devices filtered by Windows operating system, showing device name
and OS version.
```

---

## 5. File sizing and splitting strategy

### 5.1 Target size
- **Optimal:** 3,000–7,000 characters per file.
- **Acceptable:** Up to 10,000 characters.
- **Avoid:** Over 10,000 characters. Retrieval quality degrades as
  file size increases because chunks become less precise relative to
  the total file volume.

### 5.2 When to split a file
Split a file when any of the following is true:
- The file exceeds 8,000 characters.
- The file covers two or more topics that a user would query
  independently.
- A section within the file is longer than 1,500 characters without
  a sub-header.
- Testing shows the agent retrieving the wrong section for a query
  that should hit a specific part of the file.

### 5.3 When NOT to split a file
Do not split a file just to stay under a character limit if the
resulting files would each be under 1,000 characters. Very short files
add retrieval overhead without adding precision. Merge thin files that
cover closely related sub-topics.

### 5.4 Recommended file set for a standard agent
A well-designed agent typically has 3–5 knowledge source files:

| File                              | Content                                      | Target size     |
|-----------------------------------|----------------------------------------------|-----------------|
| `01-agent-scope-and-personas.md`  | Who the agent serves, what it covers,        | 2,000–4,000 ch  |
|                                   | what it explicitly excludes                  |                 |
| `02-domain-reference.md`          | Core facts, thresholds, metrics for the      | 4,000–7,000 ch  |
|                                   | agent's domain                               |                 |
| `03-prompt-patterns.md`           | Ready-to-use prompt templates for the        | 3,000–5,000 ch  |
|                                   | agent's primary use cases                    |                 |
| `04-constraints-and-limits.md`    | Platform limits, unsupported capabilities,   | 3,000–6,000 ch  |
|                                   | data retention                               |                 |
| `05-escalation-and-handoff.md`    | When to escalate, who to contact, what       | 1,000–3,000 ch  |
| (optional)                        | the agent cannot resolve                     |                 |

---

## 6. Maintenance rules

### 6.1 Treat knowledge source files as living documents
A knowledge source file that is never updated becomes a liability. Set
a review cadence aligned with the platform release cycle (Nexthink
releases approximately every 4–6 weeks). At minimum, review files after
each major platform release.

### 6.2 Version control outside the platform
Nexthink Assist does not provide version history for knowledge source
files. Maintain the authoritative copy in a version-controlled
repository (Git, SharePoint with versioning, Confluence). The file
uploaded to the agent is a deployment artifact, not the source of truth.

### 6.3 Test after every update
After updating a knowledge source file, run the five most common
queries the agent is expected to handle and verify the response quality.
A change to one section can degrade retrieval for an unrelated section
if it shifts chunk boundaries.

**Minimum test set for any agent update:**
1. A query that should hit the updated section directly.
2. A query that should NOT hit the updated section (verify no
   regression).
3. A query that previously worked correctly (regression check).

### 6.4 Remove content that is no longer accurate
Outdated content is worse than missing content. An agent that
confidently cites a deprecated limit or a removed feature erodes user
trust faster than an agent that says "I don't have that information."
When a platform change invalidates a section, remove or update it
before the next deployment.

### 6.5 Document what was removed and why
When removing a section, add a one-line comment at the top of the file
noting the removal:

```
<!-- 2026-07-07: Removed §4.3 (VDI 48h retention) — superseded by
     platform update; new retention is 7 days as of release 2026.3 -->
```

This prevents the same content from being re-added by a future editor
who does not know it was intentionally removed.

---

## 7. Anti-patterns — what degrades retrieval quality

| Anti-pattern                          | Why it fails                                      | Fix                                      |
|---------------------------------------|---------------------------------------------------|------------------------------------------|
| One giant file for all topics         | Chunks are imprecise; wrong sections retrieved    | Split by topic; one file per domain      |
| Instructions in knowledge source      | Not reliably retrieved; may conflict with R&B     | Move instructions to Role & Behavior     |
| NQL code blocks                       | Agent quotes unvalidated code as executable       | Describe query intent in natural language|
| Jargon-only headers                   | Semantic mismatch with user query vocabulary      | Use plain-language headers               |
| Long prose paragraphs without breaks  | Arbitrary chunk splits cut concepts in half       | Use lists, tables, short sections        |
| Duplicate facts across files          | Agent retrieves conflicting versions              | Single source of truth per fact          |
| No date-stamp on volatile content     | Agent cites outdated limits confidently           | Add last-verified date to volatile facts |
| File name unrelated to content        | Retrieval context weakened                        | Name files to match their dominant topic |
| Sections over 1,500 chars without H3  | Retrieval precision degrades within the section   | Add sub-headers every 500–800 characters |
| Context-dependent sentences           | Chunk retrieved in isolation loses meaning        | Make every chunk self-contained          |

---

## 8. Quick-reference checklist — before uploading a file

- [ ] File covers exactly one topic.
- [ ] File name accurately describes the topic.
- [ ] File is under 10,000 characters (ideally under 7,000).
- [ ] Every section has a descriptive H2 or H3 header.
- [ ] No section exceeds 1,500 characters without a sub-header.
- [ ] No NQL code blocks present.
- [ ] No behavioral instructions (those belong in Role & Behavior).
- [ ] Volatile content (limits, retention periods) carries a
      last-verified date.
- [ ] Facts and recommendations are clearly distinguished.
- [ ] No content duplicated from another file in the same agent.
- [ ] File tested against at least three representative queries
      before deployment.

---

*This file is a knowledge source for the Nexthink Assist Agent Designer agent.
It does not contain live data and does not reference any specific customer
environment. Last verified: 2026-07-07.*
```

---

**Compte de caractères estimé : ~9 750 caractères** — au plafond recommandé, ce qui est cohérent pour un fichier de référence dense.

---

**Récapitulatif des cinq fichiers produits :**

| Fichier | Contenu principal | Taille estimée |
|---|---|---|
| `01-agent-design-patterns.md` | Patterns de conception, checklist de création | ~8 680 ch |
| `02-prompt-engineering-guide.md` | Anatomie du prompt, patterns d'échec, bibliothèque | ~8 820 ch |
| `03-scheduled-tasks-reference.md` | Tâches planifiées, types, règles de rédaction | ~8 400 ch |
| `04-nexthink-assist-platform-constraints.md` | Limites plateforme, rétention, objets, RBAC | ~9 800 ch |
| `05-knowledge-source-best-practices.md` | Retrieval, structure, maintenance, anti-patterns | ~9 750 ch |
