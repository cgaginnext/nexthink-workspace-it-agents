# PC Slowness Action Follow-up Model

## Purpose

This file defines how the PC Slowness Analyst should recommend, track, and review remediation actions.

The goal is to move from one-time diagnosis to a continuous improvement routine.

The original Productivity Guardian model includes continuous optimization principles: tracking recurring issues, previously resolved problems, and ineffective remediations.

---

## Action categories

### 1. Immediate quick wins

Low effort actions with likely immediate impact.

Examples:

- Free disk space
- Restart the device
- Complete pending updates
- Resolve update loop
- Reduce startup applications
- Clear OneDrive sync backlog
- Check abnormal browser or Teams resource usage
- Validate security agent health
- Validate endpoint management client health

---

### 2. Standard remediation

Actions requiring more investigation but still manageable by the operational team.

Examples:

- Repair endpoint management client
- Repair OneDrive client
- Update drivers or firmware
- Review startup policies
- Reinstall or repair problematic agent
- Apply known vendor fix
- Check device thermal or hardware health
- Review power settings
- Review local profile size

---

### 3. Structural remediation

Actions that address a repeated pattern across the estate.

Examples:

- Reschedule scans outside user-present hours
- Adjust endpoint management deployment windows
- Review security tool exclusions or policies
- Review model lifecycle and hardware standards
- Increase memory on specific models
- Replace underpowered device families
- Standardize BIOS, driver, or firmware versions
- Define reboot hygiene process
- Define disk hygiene automation
- Create recurring top 10 device review

---

### 4. Escalation

Actions requiring another team.

Examples:

- Security tool policy review
- Network or proxy investigation
- Application performance investigation
- Infrastructure or identity investigation
- Procurement or hardware lifecycle decision

---

## Action recommendation format

Each recommendation should follow this format:

## Recommended action

- Device or population:
- Issue addressed:
- Evidence:
- Action:
- Owner:
- Effort:
- Risk:
- Expected impact:
- Feasibility to confirm:
- Follow-up indicator:
- Review date or next review cycle:

---

## Top 10 treatment list format

Use this format for operational review.

| Rank | Device | Model | Main symptom | Likely cause | Recurrence | Quick win | Recommended action | Feasibility to confirm | Follow-up indicator |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Device name | Model | Example: slow startup | Example: pending updates + low disk | Recurring | Yes | Free disk, reboot, complete updates | Confirm remote action allowed | Boot time, CPU Queue Length, user feedback |

---

## Feedback loop

After recommendations, always ask the user:

- Are these actions feasible for your team?
- Are some tools outside your ownership?
- Are there actions you want to exclude?
- Do you want to focus only on quick wins?
- Should the next analysis compare before and after remediation?
- Should we keep the same top 10 format for recurring follow-up?

---

## Follow-up analysis

For each action, compare before and after.

### Before indicators

- Performance score
- CPU usage
- CPU Queue Length
- Memory pressure
- Memory Swap Rate
- Available memory
- Disk free space
- Disk Queue Length
- Boot duration
- Logon duration
- Reboot age
- User sentiment or complaint count if available

### After indicators

- Same indicators after action
- Improvement percentage
- Issue recurrence
- Remaining bottleneck
- Whether action was effective

---

## Action status

Use these statuses:

- Proposed
- Confirmed
- Planned
- In progress
- Done
- Not feasible
- Rejected
- Needs another team
- Monitoring
- Reopened

---

## Continuous improvement routine

Recommended routine:

1. Run the analysis on a fixed period, usually 7 or 30 days.
2. Identify recurring issues first.
3. Produce the top 10 devices to treat.
4. Apply quick wins.
5. Track actions.
6. Recheck the same devices.
7. Compare degraded and healthy devices.
8. Identify structural causes.
9. Adjust policies, schedules, or standards.
10. Repeat regularly.

---

## Outcome classification

After follow-up, classify each action as:

### Effective

The device improved and the issue did not reappear.

### Partially effective

The device improved, but some symptoms remain.

### Ineffective

No measurable improvement.

### Not measurable

Action was done but evidence is insufficient.

### Recurrent

The issue improved temporarily but returned.

---

## Final follow-up output format

## Follow-up summary

- Number of actions proposed:
- Number of actions completed:
- Number of devices improved:
- Number of devices still degraded:
- Number of recurrent issues:
- Main lessons learned:

## What improved

List the strongest improvements.

## What did not improve

List unresolved or recurrent situations.

## Next recommendations

List what should be done next, focusing on realistic actions.