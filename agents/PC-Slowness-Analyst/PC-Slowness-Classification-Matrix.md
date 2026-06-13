# PC Slowness Classification Matrix

## Purpose

This file defines how to classify PC slowness issues by recurrence, user impact, scope, and remediation priority.

It is inspired by a productivity impact model where prioritization is based on impact, scope, recurrence, and correlation between signals. The reference model classifies productivity issues by lost time, affected population, and escalation rules when several signals converge.

---

## Priority levels

### P1 — Critical structural degradation

Use P1 when:

- A large population is affected
- The issue impacts business-critical users or locations
- The degradation is recurring or persistent
- Multiple technical signals converge
- There is clear user productivity loss
- The same root cause appears across several models, locations, or populations

Examples:

- Many devices show CPU saturation during working hours because of the same security tool behavior
- A specific device model has systemic memory pressure
- Startup time is severely degraded across a large device family
- Endpoint management activity repeatedly runs during business hours and impacts many users

Recommended response:

- Immediate prioritization
- Cross-team coordination
- Structural remediation plan
- Follow-up measurement

---

### P2 — High recurring degradation

Use P2 when:

- A significant group is affected
- The issue is recurring
- User experience is clearly degraded
- The root cause appears treatable
- Several devices cumulate hygiene problems

Examples:

- Top 10 devices repeatedly show high CPU Queue Length
- Several devices have low disk space, pending reboot, and update backlog
- OneDrive synchronization repeatedly consumes resources on the same user group
- A security scan causes recurring slowness on older models

Recommended response:

- Treat as a priority operational improvement
- Start with quick wins
- Track before/after evolution
- Decide whether the pattern is structural

---

### P3 — Medium localized degradation

Use P3 when:

- The issue affects a limited population
- There is measurable degradation
- The issue is recurring but not widespread
- The operational impact is moderate

Examples:

- A few devices have recurring slow startup
- One model shows moderate CPU pressure
- A specific location has repeated slowness after network reconnection
- A small group has recurring memory pressure during Teams usage

Recommended response:

- Include in weekly treatment list
- Investigate root cause
- Apply standard remediation
- Monitor recurrence

---

### P4 — Low or isolated degradation

Use P4 when:

- The issue is isolated
- The issue happened once
- The impact is low
- The issue auto-resolved quickly
- There is no measurable productivity degradation

Examples:

- A one-time CPU spike
- One isolated slow startup
- A short-lived update episode
- A single application spike without recurrence

Recommended response:

- Do not over-prioritize
- Mention as isolated
- Monitor only if it reappears
- Avoid unnecessary remediation

---

## Recurrence classification

### Permanent

The device is degraded most of the time during user-present periods.

Typical signals:

- Constant high CPU usage
- Constant memory pressure
- Very low available disk space
- Long-term poor performance score
- Repeated poor user experience

---

### Recurring

The device is degraded repeatedly in similar conditions.

Typical patterns:

- Every morning at startup
- After lunch or idle period
- After network change
- During update cycles
- During OneDrive synchronization
- During scheduled scans
- During software deployment
- During Teams or browser-heavy usage

---

### Random

The degradation appears irregular and does not show an obvious schedule.

Typical approach:

- Look for correlation with background tasks
- Check device thermal or hardware issues
- Check network changes
- Check application crashes
- Check security or management tool activity

---

### Isolated

The degradation happened once or very rarely.

Typical approach:

- Mark as isolated
- Do not include in top priority unless the impact was severe
- Monitor for recurrence

---

## Escalation rules

Escalate the priority when:

- The same issue affects several devices
- The same issue affects several locations
- The same issue persists for more than 3 days
- The same model is repeatedly impacted
- Several signals converge, for example CPU saturation, memory pressure, disk shortage, long boot time, and user complaints
- A device cumulates several basic hygiene problems
- The issue occurs during active working hours

The reference productivity model also recommends escalation when the same issue affects multiple locations, persists for more than 3 days, or when several signals converge.

---

## Downgrade rules

Downgrade the priority when:

- The issue auto-resolves quickly
- The issue is isolated
- The issue happens outside working hours
- The affected device is inactive
- There is no measurable user impact
- The affected population is very small and there are no complaints
- The signal is technical only and does not degrade user experience

The reference productivity matrix also recommends downgrading noise signals when the issue auto-resolves quickly, repeatedly affects a small group with no complaints, or shows no measurable productivity degradation.