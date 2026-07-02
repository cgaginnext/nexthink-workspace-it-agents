# DEX Threshold Governance

## Purpose

Prevent score inflation and maintain score credibility.

---

## Evaluation Criteria

Review:

- distribution
- percentiles
- cohort behavior
- population relevance
- benchmark alignment

---

## Before Recommending Changes

Always evaluate:

- P50
- P90
- P95

Determine whether thresholds still separate:

- Good
- Average
- Frustrating

experiences meaningfully.

---

## Simulation

Whenever possible estimate:

If the threshold changes:

- how many devices and users change bucket
- impact on leaf score
- impact on parent score
- impact on DEX

### Example prompts for simulation:

#### Single leaf score threshold simulation:
Before modifying any DEX score threshold, simulate the impact of the 
proposed change on the current population.

Ask for the metric [METRIC NAME] with proposed threshold change 
from [CURRENT VALUE] to [NEW VALUE] for Average and Frustrating range start:

1. Count how many users/devices currently fall in each band 
   (Good / Average / Frustrating) using the current threshold.
2. Count how many would move between bands with the new threshold, include also percent change.
3. Compute the estimated change in the composite score for the affected population.
4. Compute the estimated change in the composite and leaf score for the involved branch and provide analysis per country.
5. State the Nexthink default threshold for this metric as a reference baseline.
6. Flag that threshold changes are not retroactive
   the new threshold applies from the next computation cycle only.
7. Log the proposed change with: date, metric, old value, new value, 
   justification provided by the user.

Present the simulation results and wait for explicit user confirmation.

#### Full DEX leaf analysis and suggestions
Give me a full audit of all active DEX score, sub-score and leaf score thresholds for Average and Frustrating. Propose updated thresholds for every leaf score based on a relevant distribution of good, average and frustrating helping to raise the bar constructively without showing too much unuseful frustration. 
1. Count how many users/devices currently fall in each band 
   (Good / Average / Frustrating) using the current threshold.
2. Count how many would move between bands with the new threshold, include also percent change.
3. Compute the estimated change in the composite score for the affected population.
4. Compute the estimated change in the composite and leaf score for the involved branch and provide analysis per country.

---

## Governance Principles

Bad reason:

"Increase threshold because score is low."

Good reason:

"Current threshold classifies 45% of healthy users as frustrating."

---

## Red Flags

Never recommend changes solely because:

- leadership wants a higher score
- another country has a higher score
- a KPI target is missed

Experience quality must remain the objective.