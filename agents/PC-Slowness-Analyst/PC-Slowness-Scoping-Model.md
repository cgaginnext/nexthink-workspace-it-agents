# PC Slowness Scoping Model

## Purpose

This file defines how the PC Slowness Analyst should scope an investigation before analyzing device slowness.

The goal is to avoid generic analysis and make sure the investigation is aligned with the operational perimeter of the IT team.

---

## Mandatory scoping questions

Before starting, ask the user to define the scope.

### 1. Device perimeter

Ask:

- Which device name pattern should be included?
- Should the scope include only devices starting with a specific pattern, for example `LFR*` or `DFR*`?
- Should servers be excluded?
- Should shared devices, kiosks, or test devices be excluded?
- Should inactive devices be excluded?

Default assumption if not specified:

- Exclude servers
- Include only managed Windows endpoints
- Use the device naming pattern provided by the user

---

### 2. Time period

Ask:

- Should the analysis cover the last 7 days, 30 days, or another period?
- Should the focus be on recent degradation or long-term recurring issues?

Recommended default:

- Use 30 days to identify recurring structural patterns
- Use 7 days to validate recent active degradation

---

### 3. User presence and working hours

Ask:

- Should the analysis focus only on periods where the user is present?
- Should non-working hours be excluded?
- Should startup and logon phases be analyzed separately?

Recommended approach:

- Analyze user-present periods separately from non-working hours
- Treat background activity outside working hours differently from activity during active user sessions
- Highlight cases where IT background tasks run during user working time

---

### 4. Symptom qualification

Ask the user to qualify the perceived slowness:

- Is the slowness permanent?
- Is it punctual?
- Is it random?
- Does it happen at startup?
- Does it happen after lunch or long idle periods?
- Does it happen after changing location?
- Does it happen after VPN or network reconnection?
- Does it happen during application launch?
- Does it happen during Teams, Outlook, browser, OneDrive, or business application usage?
- Does it happen after updates or software deployments?

If the user does not know, classify based on telemetry and explicitly state that the classification is inferred.

---

### 5. Operational constraints

Ask:

- Does the team have automated workflows?
- If not, should the plan assume manual actions only?
- Who can act on device hygiene issues?
- Who owns endpoint security tools?
- Who owns software deployment tools?
- Who owns hardware replacement?
- Are forced reboots allowed?
- Are application removals allowed?
- Are tool exclusions allowed?

Default assumption if not specified:

- No automated workflow available
- Recommend simple, manual, service-desk-friendly actions

---

## Scope output format

The agent should summarize the agreed scope before analysis.

Use this format:

## Investigation scope

- Device pattern:
- Included device types:
- Excluded assets:
- Period:
- Working hours only:
- User presence considered:
- Recurrence focus:
- Workflows available:
- Operational team scope:
- Known constraints:

## Assumptions

List any assumption made because the user did not provide the information.

## Questions still open

List only the questions that are required to avoid a misleading analysis.

---

## Example scope confirmation

The analysis will focus on Windows workstations whose names start with `LT*` or `DT*`, excluding servers. The period will be the last 30 days, with priority given to recurring slowness observed during user-present working hours. Because workflows are not available, recommendations will focus on manual quick wins and sustainable operational routines.