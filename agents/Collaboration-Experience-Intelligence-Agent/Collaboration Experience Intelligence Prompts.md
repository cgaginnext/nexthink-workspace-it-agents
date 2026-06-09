# Collaboration Experience Intelligence Prompts

## Core Collaboration Quality Prompts

### Poor Audio Peripheral Analysis

List the microphones used during poor audio quality sessions in Teams/Zoom over the last 14 days, grouped by microphone name and count. Give it in proportion of poor sessions vs total sessions (poor rate).

---

### Concurrent Endpoint Issue Correlation

Open Device View for [device name] and check the Timeline during the poor call — were there any concurrent application crashes, connectivity drops, or high CPU events?

---

### Camera Driver Quality Analysis

List the camera driver versions detected during poor video quality Teams sessions on Windows, grouped by driver name/version and count. Give it in proportion of poor sessions vs total sessions (poor rate).

---

### Microphone Driver Quality Analysis

List the microphone driver versions detected during poor audio quality Teams sessions on Windows, grouped by driver name/version and count.

---

### Camera Quality Analysis

List the cameras used during poor video quality sessions, grouped by camera name and count.

---

### Speaker Quality Analysis

List the speakers used during poor audio quality sessions, grouped by speaker name and count of poor sessions.

---

### Global Collaboration Quality Impact

What is the total duration of poor-quality collaboration sessions in the last 30 days, aggregated across all users?

---

# Advanced Collaboration Intelligence Prompts

## 🏆 Peripheral Quality Leaderboard

Prompt:

"For each microphone/speaker/camera model, what is the ratio of poor vs. good sessions over the last 30 days — ranked from worst to best?"

Purpose:
- build a peripheral quality scorecard
- identify poor-performing hardware
- support procurement standardization decisions

---

## 🔄 Driver Update Impact Analysis

Prompt:

"For devices that received an audio driver update in the last 30 days, compare the number of poor audio sessions before and after the update date."

Purpose:
- validate remediation efficiency
- assess rollout quality impact
- identify regressive driver versions

---

## 📍 Peripheral Issues by Location

Prompt:

"Group poor audio/video sessions by location type (onsite vs. remote) and by microphone/camera model — which peripheral models perform worse in office vs. at home?"

Purpose:
- isolate environmental factors
- detect remote-work-specific degradations
- compare office acoustic/network conditions

---

## 🔁 Silent Sufferers Detection

Prompt:

"List users who had more than 5 poor audio sessions in the last 14 days but never submitted a support ticket — grouped by microphone model."

Purpose:
- identify hidden employee frustration
- detect underreported collaboration pain
- enable proactive support outreach

---

## 🧪 Peripheral A/B Comparison

Prompt:

"Compare poor session rates between users using [Headset Model A] vs. [Headset Model B] on the same OS and connection type."

Purpose:
- isolate peripheral impact
- eliminate environmental bias
- validate procurement standards

---

## 📦 Outdated Driver + Poor Quality Combo

Prompt:

"List devices that have both an outdated audio driver (older than [version X]) AND at least one poor audio session in the last 7 days."

Purpose:
- produce actionable remediation targets
- avoid broad unnecessary deployments
- optimize patch targeting

---

## 🎯 New Hire / New Device Peripheral Risk

Prompt:

"For devices first seen in the last 60 days, what is the distribution of microphone and camera models — and how many already had poor sessions?"

Purpose:
- detect onboarding quality issues
- identify bad hardware delivered recently
- improve employee first experience

---

## 📣 Targeted Nexthink Campaign Opportunity

Prompt:

"Create a campaign targeting users whose last poor session used [specific headset model], asking if they are experiencing audio issues and whether they'd like a replacement."

Purpose:
- proactive employee engagement
- contextualized support outreach
- user sentiment collection

---

## 🔗 Peripheral + Network Correlation

Prompt:

"For poor audio sessions, break down by microphone model AND connection type (WiFi vs. Ethernet) — does the peripheral issue persist regardless of network quality?"

Purpose:
- distinguish network vs hardware root causes
- isolate problematic peripherals
- improve remediation precision

---

## 📊 Fleet Standardization Opportunity

Prompt:

"How many distinct microphone, speaker, and camera models are in use across the fleet — what is the long tail of rarely used peripherals?"

Purpose:
- identify peripheral sprawl
- reduce support complexity
- optimize hardware governance