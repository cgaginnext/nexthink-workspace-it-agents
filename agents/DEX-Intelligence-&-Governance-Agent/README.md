# DEX Intelligence & Governance Agent

## Purpose

The DEX Intelligence & Governance Agent helps organizations understand, govern, optimize, and continuously improve their Nexthink Digital Employee Experience (DEX) Score.

Unlike traditional DEX dashboards, this agent does not simply report scores. It explains:

- Why scores are changing
- Which technical factors contribute most to degradation
- Which populations are impacted
- Which actions will generate the highest improvement
- Whether current thresholds remain relevant
- Whether the DEX configuration itself should evolve

The agent combines DEX analytics, score governance, threshold optimization, benchmarking, sentiment analysis, trend detection, and remediation prioritization into a single expert advisor.

---

## Why This Agent?

Many teams can see their DEX scores.

Few teams can answer:

- Why did the score change?
- Which score dimension contributes most to degradation?
- Is the score degradation structural or temporary?
- Which application is responsible?
- Are our thresholds still meaningful?
- Are we measuring experience properly?
- Is the DEX score aligned with employee perception?
- What should we fix first to maximize DEX improvement?

This agent is designed to answer those questions.

---

## Core Capabilities

### DEX Score Analysis

Analyze:

- DEX Score
- Technology Score
- Endpoint Score
- Applications Score
- Collaboration Score
- Sentiment Score

Explain:

- score hierarchy
- score composition
- weak-link effects
- bucket calculations
- frequency impact versus severity impact
- day-to-day score evolution

---

### Root Cause Analysis

Drill down from:

DEX Score

↓

Technology Score

↓

Subscores

↓

Leaf Scores

↓

Underlying Metrics

Identify:

- primary contributors
- affected populations
- impacted applications
- impacted devices
- likely root causes

---

### Score Impact Analysis

Leverage score impact data to identify:

- largest contributors to DEX degradation
- highest-value remediation opportunities
- expected score recovery potential

Prioritize actions according to:

- impact
- population size
- business relevance
- remediation effort

---

### Threshold Governance

Audit and optimize:

- Endpoint thresholds
- Application thresholds
- Collaboration thresholds

Detect:

- threshold inflation
- under-sensitive thresholds
- over-sensitive thresholds
- governance drift

Simulate:

- threshold changes
- population movement between bands
- expected score variation
- impact by country and entity

---

### Population Benchmarking

Compare:

- countries
- locations
- entities
- departments
- device models
- operating systems
- VDI versus physical environments

Identify:

- outliers
- best performers
- worst performers
- structural gaps

---

### Sentiment Intelligence

Correlate:

- Technology Score
- Sentiment Score
- Campaign responses

Identify:

- perception gaps
- hidden frustrations
- recurring complaint themes
- adoption risks

---

### Trend and Drift Analysis

Detect:

- gradual degradation
- sudden score drops
- post-deployment regressions
- post-remediation improvements

Compare:

- before versus after changes
- deployment periods
- organizational cohorts

---

### Coverage and Configuration Assessment

Evaluate:

- score coverage
- sentiment participation
- missing telemetry
- connector coverage
- scoring quality

Detect:

- missing DEX visibility
- score approximation situations
- incomplete scoring coverage

---

### DEX Governance and Strategy

Act as a DEX advisor.

Recommend:

- remediation priorities
- threshold reviews
- monitoring improvements
- governance initiatives
- long-term DEX strategy

---

## Typical Use Cases

### DEX Score Health Review

Understand:

- current DEX posture
- main degradation drivers
- impacted populations
- improvement opportunities

---

### Executive DEX Reporting

Generate:

- current DEX posture
- score evolution
- business impact
- remediation priorities
- executive recommendations

---

### Root Cause Investigation

Identify:

- why a score dropped
- which subscore is responsible
- which applications contribute most
- which populations are affected

---

### Threshold Optimization

Audit:

- threshold relevance
- governance quality
- alignment with reality

Recommend data-driven threshold improvements.

---

### Sentiment Analysis

Compare employee perception versus technical reality.

Identify hidden frustrations not visible in technical telemetry.

---

### Long-Term DEX Governance

Monitor:

- score evolution
- threshold quality
- adoption maturity
- experience improvement programs

---

# Example Prompts

## DEX Score Deep Dives

> Give me a full DEX score breakdown for the organization. Identify the weakest subscore, explain why it is degraded, quantify its impact on the overall DEX score, and tell me what would move the score most.

---

> Compare DEX scores across all countries and identify the top three best-performing and worst-performing populations. Explain the primary factors driving the differences.

---

> Show me the DEX score trend over the last 30 days. Identify whether the score is improving, deteriorating, or stable and explain the factors driving the trend.

---

## Root Cause Analysis

> My Endpoint score dropped this week. Perform a complete root cause analysis including crashes, freezes, logon times, boot duration, CPU pressure, memory pressure, storage performance, and network quality. Prioritize corrective actions.

---

> Which applications contribute most to the degradation of the Applications score? Rank them by score impact and explain the root causes for each application.

---

> The Collaboration score is below average. Determine whether the problem comes primarily from Teams, Zoom, endpoint performance, networking, or user environment conditions.

---

## DEX Governance & Threshold Reviews

### Comprehensive Threshold Audit

> Give me a full audit of all active DEX score, sub-score, and leaf score thresholds currently configured for Average and Frustrating classifications. Propose updated thresholds for every leaf score based on actual fleet distributions while maintaining meaningful differentiation between Good, Average, and Frustrating experiences. Avoid artificial score inflation and prioritize employee experience authenticity.

For each threshold:

- show current values,
- recommend proposed values,
- explain the rationale,
- compare against observed population distributions,
- show the percentage of users currently classified as Good, Average, and Frustrating,
- estimate the impact of the proposed changes.

Additionally:

1. Count how many users and devices currently fall into each band.
2. Count how many would move between bands using the proposed threshold.
3. Calculate percentage changes.
4. Estimate the resulting impact on leaf scores, parent scores, composite scores, and overall DEX.
5. Provide analysis by country, business unit, and device type.
6. Highlight governance risks and threshold drift concerns.

---

### Threshold Audit & Inventory

> Give me a full audit of all active DEX thresholds configured in our environment. Which thresholds have never been modified since their original deployment?

---

> Which thresholds appear overly permissive and no longer contribute meaningful sensitivity to DEX scoring?

---

> Which thresholds appear overly restrictive and may be creating unnecessary frustration classifications?

---

### Threshold vs Reality Analysis

> For each DEX metric, compare the current threshold configuration against actual fleet distributions. Identify thresholds that are no longer aligned with operational reality.

---

> Compare our current Logon Speed thresholds against actual fleet percentiles and recommend an improved configuration.

---

> Compare our CPU thresholds against real endpoint usage patterns. Are we identifying the right populations?

---

### Threshold Simulation

> Simulate the impact of changing the Logon Speed frustrating threshold from its current value to a more realistic value based on the P95 of observed behavior. Estimate user migration between score bands and DEX score impact.

---

> Simulate the impact of resetting all DEX thresholds back to Nexthink defaults.

---

> Which single threshold change would have the largest positive impact on DEX governance quality?

---

## Population Intelligence

> Compare DEX scores between VDI and physical devices. Identify structural differences and their root causes.

---

> Which department currently has the worst digital experience? Break down the score contribution by subscore and leaf score.

---

> Show me the lowest-DES-scoring devices in Paris and identify common characteristics such as model, OS version, age, and primary applications.

---

## Trend & Drift Analysis

> Identify any DEX score drift occurring during the last 90 days. Distinguish between gradual degradation and sudden regressions.

---

> Has a recent software deployment negatively impacted DEX? Compare the seven days before and after deployment and quantify the effect.

---

> Which populations have experienced continuous DEX decline over the past three months?

---

## Sentiment Intelligence

> Compare Technology Score with Sentiment Score. Identify perception gaps and explain what employees may be experiencing that technical metrics are not capturing.

---

> Analyze recent DEX campaign responses. Identify recurring frustrations and map them to the relevant score branches and leaf scores.

---

> Design a targeted DEX campaign for employees with a DEX score below 40 to identify their biggest digital experience challenges.

---

## Remediation Planning

> Give me a prioritized remediation plan to improve our overall DEX score by 10 points. Rank actions by ROI, expected score gain, remediation effort, and business impact.

---

> Which devices are most likely to continue degrading over the next 30 days? Recommend preventive actions.

---

> Estimate the DEX improvement achievable within the next 30 days if the top three score-impact contributors are remediated.

---

## Executive Reporting

> Prepare a board-ready executive summary of our DEX posture including current score, score trends, key issues, affected populations, business impact, remediation priorities, governance observations, and expected score improvement opportunities.

---

> Produce a quarterly DEX governance review including threshold quality, score evolution, sentiment trends, root causes, and strategic recommendations.

---

## Advanced Analysis

> Identify the 20 largest contributors to DEX degradation using score impact analysis. Estimate the DEX gain achievable if each contributor were fully remediated and rank them by cost-benefit ratio.

---

> Evaluate the overall quality of our DEX configuration, including thresholds, score coverage, sentiment coverage, application coverage, collaboration coverage, and long-term trend visibility. Provide a maturity assessment and improvement roadmap.

---

## Included Resource Files

This agent is designed to leverage the following supporting resources:

- `dex_analysis_framework.md`
- `dex_threshold_governance.md`
- `dex_score_impact_analysis.md`
- `dex_trend_and_cohort_analysis.md`
- `dex_detection_and_recommendations.md`

---

## Ideal Audience

This agent is designed for:

- Digital Workplace Teams
- DEX Teams
- End User Computing (EUC)
- Workplace Engineering
- Operational Excellence Teams
- IT Operations
- IT Leadership
- Customer Success Managers
- Workplace Analytics Teams

---

## Expected Outcomes

Organizations using this agent should be able to:

- Improve overall DEX scores
- Improve DEX governance maturity
- Detect degradation earlier
- Align technology and sentiment insights
- Improve remediation prioritization
- Reduce score inflation risk
- Optimize threshold quality
- Improve user experience visibility
- Enable evidence-based DEX decision making

---

## Disclaimer

This agent is intended to support DEX governance and decision making.

Recommendations should be reviewed in the context of organizational objectives, business priorities, operational constraints, and DEX governance policies.