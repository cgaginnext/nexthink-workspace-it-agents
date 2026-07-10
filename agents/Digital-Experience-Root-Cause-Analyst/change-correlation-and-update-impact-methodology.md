# Change Correlation and Update Impact Methodology

## Purpose

This file defines the complete methodology for correlating software
changes (installations, uninstallations, updates) with DEX score
degradation. It covers the NQL data sources, the temporal correlation
windows, the causality classification framework, the update impact
classification rules, and the documented patterns of known
change-to-degradation mechanisms.

The agent MUST apply this methodology consistently in every RCA that
involves a DEX score change. Never assign causality without following
the classification framework in Section 4. Never present a change as
causal based on timing alone.

---

## 1. Data Sources for Change Detection

### 1.1 Primary tables

| Table | Namespace | Key fields | What it captures |
|---|---|---|---|
| `installations` | `package` | `installation.time`, `package.name`, `package.version`, `package.publisher`, `package.type`, `package.parent_name` | Every package install event on the device |
| `uninstallations` | `package` | `uninstallation.time`, `package.name`, `package.version`, `package.publisher`, `package.type` | Every package uninstall event on the device |
| `installed_packages` | `package` | `package.name`, `package.version`, `package.publisher`, `package.type`, `package.parent_name`, `package.first_seen` | Current installed state — snapshot of what is on the device now |

### 1.2 Package type enumeration

The `package.type` field accepts two values:

| Value | Meaning |
|---|---|
| `Program` | A new application or tool being installed |
| `Update` | An update to a previously installed package |

Use `package.parent_name` to identify which program an update belongs
to. For example, an update with `package.parent_name == "Microsoft Edge"`
is an Edge browser update.

### 1.3 Package platform enumeration

The `package.platform` field accepts: `Windows`, `macOS`, `unknown`.
Always filter by platform when the device OS is known to avoid
cross-platform noise in the results.

### 1.4 Change query window

The standard change detection window for an RCA is:
- **Start**: 72 hours before the identified DEX degradation onset date
- **End**: 24 hours after the degradation onset date

This window captures changes that could have caused the degradation
(pre-onset) and changes that occurred as a consequence or coincidence
immediately after (post-onset).

For remediation validation, extend the end of the window to 7 days
after the remediation action to capture the full DEX recovery window.

---

## 2. Identifying the Degradation Onset Date

Before correlating changes, the agent MUST identify the degradation
onset date precisely. This is the anchor for all temporal correlation.

### 2.1 Step-by-step onset detection

1. Query `dex.scores during past 30d` for the device, listing
   `score.value` and `score.technology.value` by date.
2. Scan consecutive daily pairs for a delta ≥ 10 points (significant
   drop threshold).
3. The date of the **higher** value in the pair is the last "good"
   day. The date of the **lower** value is the degradation onset date.
4. If multiple drops are present, identify each separately and treat
   them as distinct degradation events.
5. If the score has been consistently low for the entire 30-day
   window with no visible step-change, the degradation onset predates
   the available data. State this explicitly and note that the
   correlation window cannot be fully established.

### 2.2 Onset date precision note

The DEX score is computed once per day at 00:00 UTC using a rolling
7-day window. A score drop on date D means the degradation occurred
**sometime in the 7 days preceding D**, not necessarily on D itself.
The onset date is therefore an upper bound, not a precise timestamp.
Always state this when presenting the onset date to the user.

---

## 3. Change Timeline Construction

Once the onset date is established, construct a change timeline
covering the 72h-before to 24h-after window.

### 3.1 Timeline table format

Every change timeline MUST be presented in this format:

| Timestamp | Operation | Package name | Version | Publisher | Package type | Parent package | DEX delta | Preliminary classification |
|---|---|---|---|---|---|---|---|---|
| 2026-07-04 09:14 | Install | Microsoft Edge | 126.0.2592.68 | Microsoft | Update | Microsoft Edge | −18 pts (next day) | Candidate |
| 2026-07-04 11:02 | Install | Zscaler Client Connector | 4.2.1.180 | Zscaler | Program | — | −18 pts (next day) | Candidate |
| 2026-07-05 08:30 | Install | Windows Security Update KB5040442 | — | Microsoft | Update | Windows | — | Coincidental |

The DEX delta column shows the score change observed on the day
following the installation. If the installation occurred on the same
day as the onset date, the delta is the onset-day drop.

### 3.2 Prioritization rules for the timeline

Not all changes are equally worth investigating. Apply these
prioritization rules to focus the analysis:

**Investigate first (High priority)**:
- Security software (EDR, antivirus, DLP, VPN clients) — these
  agents run continuously and can cause CPU, memory, and network
  contention
- OS updates and cumulative patches — driver changes, kernel
  modifications, and compatibility breaks
- Browser updates — extension compatibility, rendering engine changes
- Collaboration tool updates (Teams, Zoom, Webex) — audio/video
  driver interactions, codec changes
- VPN and network proxy clients — connectivity and authentication
  chain changes

**Investigate second (Medium priority)**:
- Productivity suite updates (Microsoft 365, Google Workspace) —
  add-in compatibility, COM component changes
- Runtime environments (.NET, Visual C++ Redistributable, Java) —
  dependency changes affecting multiple applications
- Device management agents (Intune, SCCM, Jamf) — policy enforcement
  changes, background scanning

**Investigate last (Low priority)**:
- Unrelated line-of-business application updates with no system
  integration
- Font packages, language packs, minor utility updates
- Packages installed more than 72h before the onset date with no
  plausible delayed mechanism

---

## 4. Causality Classification Framework

Every change identified in the timeline MUST be assigned one of four
classifications. Never leave a change unclassified. Never assign
"Causal" based on timing alone — a plausible mechanism is required.

### 4.1 Classification definitions

| Classification | Definition | Required evidence |
|---|---|---|
| **Causal** | The change is the primary driver of the DEX degradation | Temporal alignment (< 24h) + plausible mechanism + corroboration from ≥ 2 raw data sections (e.g., CPU spike AND freeze cluster) |
| **Contributory** | The change worsened an existing condition but did not cause it alone | Temporal alignment (< 72h) + plausible mechanism + corroboration from 1 raw data section |
| **Coincidental** | The change occurred in the window but has no plausible mechanism linking it to the observed degradation | No corroboration from raw data sections; degradation explained by other factors |
| **Candidate** | Temporal alignment exists but evidence is insufficient to classify further | Requires additional data (remote action, user confirmation, or extended observation) |

### 4.2 Plausible mechanism library

Use this library to identify whether a mechanism exists between a
change type and an observed degradation pattern. A mechanism must be
stated explicitly in the analysis — never implied.

| Change type | Observed degradation | Plausible mechanism |
|---|---|---|
| EDR / antivirus update | CPU P95 spike + software performance drop | Real-time scanning engine update triggers full re-scan of binaries; elevated CPU contention during scan window |
| EDR / antivirus update | Application freeze cluster | Scanning engine intercepts binary execution; I/O wait causes freeze during file access |
| OS cumulative update | System crash cluster (new error code) | Driver compatibility break introduced by kernel patch; new bug-check code appears post-update |
| OS cumulative update | Boot duration increase | New startup services or GPO processing added by the update; logon script changes |
| Browser update | Web application performance drop | Rendering engine change breaks SPA soft navigation detection; extension incompatibility with new engine version |
| Browser update | Application freeze (browser process) | Memory regression in new browser version; heap allocation change |
| VPN client update | `no_service_connections` spike | Authentication chain change; new certificate validation step; proxy bypass rule change |
| VPN client update | Logon script duration increase | VPN initialization now required before logon script execution; sequential dependency introduced |
| Collaboration tool update | Collaboration score drop | Audio/video codec change; driver version incompatibility with new client; new background processing |
| Collaboration tool update | CPU spike during calls | New video processing pipeline; hardware acceleration disabled in new version |
| .NET / runtime update | Application crash cluster (new binary) | Dependency version mismatch; application compiled against older runtime version |
| Device management agent update | Background CPU contention | Policy re-evaluation cycle triggered post-update; inventory scan initiated |
| Package uninstallation | Application crash (crash_on_start) | Dependency removed; application attempts to load missing DLL or component |

### 4.3 Corroboration requirement

A "Causal" classification requires corroboration from at least two
independent raw data sections. The following pairs are considered
strong corroboration:

| Primary evidence | Corroborating evidence | Strength |
|---|---|---|
| CPU P95 spike in `device_performance.events` | Freeze cluster in `execution.events` | Strong |
| System crash cluster in `device_performance.system_crashes` | New error code appearing post-install | Strong |
| `no_service_connections` spike in `connection.events` | Logon script duration increase in `session.logins` | Strong |
| Application crash cluster in `execution.crashes` | `crash_on_start == true` for affected binary | Strong |
| Collaboration score drop in `dex.scores` | `session.call.quality == poor` in `collaboration.sessions` | Strong |
| CPU P95 spike | Memory swap rate increase | Moderate |
| Boot duration increase | Logon script duration increase | Moderate |

---

## 5. Update Impact Classification

After the causality classification, each change that is Causal or
Contributory must be assigned an update impact classification.
This classification describes the net effect of the change on the
user experience.

### 5.1 Impact classification definitions

| Classification | Criteria | DEX delta threshold |
|---|---|---|
| **Positive** | The change improved the user experience | DEX score increase ≥ +5 pts in the 7 days following installation, OR crash/freeze delta is negative (fewer events) |
| **Neutral** | The change had no measurable effect on the user experience | DEX delta between −4 and +4 pts; no significant change in raw metrics |
| **Potentially Impacting** | The change degraded the user experience | DEX score decrease ≥ −5 pts in the 7 days following installation, OR crash/freeze delta is positive (more events) |
| **Regression** | The change introduced a new failure mode not previously observed | New error code, new crashing binary, or new freeze pattern appearing exclusively post-installation |

### 5.2 Measuring the DEX delta for a specific change

To measure the DEX delta attributable to a specific installation:

1. Identify `installation.time` for the package.
2. Query `dex.scores` for the 7 days before `installation.time`
   (pre-window). Calculate `avg(score.technology.value)` for this
   window.
3. Query `dex.scores` for the 7 days after `installation.time`
   (post-window). Calculate `avg(score.technology.value)` for this
   window.
4. DEX delta = post-window avg − pre-window avg.
5. Apply the threshold from §5.1 to assign the classification.

**Important**: use `score.technology.value`, not `score.value`, for
this measurement. The Technology score reflects hard metrics directly
affected by software changes. The overall DEX score includes Sentiment,
which is not affected by software installations.

### 5.3 Distinguishing impact types

When a change is classified as Potentially Impacting or Regression,
further distinguish between:

| Sub-type | Definition | Indicator |
|---|---|---|
| **Exposed existing issue** | The change revealed a pre-existing weakness (e.g., low RAM that was borderline before the update) | Raw metrics were already near threshold before the installation; the change pushed them over |
| **Introduced new issue** | The change created a new failure mode that did not exist before | New error code, new crashing binary, or new freeze pattern with no prior occurrence in the 30-day window |
| **Improved experience** | The change resolved a pre-existing issue | Crash/freeze count drops to near-zero post-installation; DEX score recovers |

---

## 6. Documented Change-to-Degradation Patterns

These are the most frequently observed patterns in enterprise
environments. When a pattern matches, use it to accelerate the
causality classification — but always verify with raw data before
assigning "Causal."

### Pattern 1 — EDR update → CPU spike → Software performance drop

**Trigger**: Security agent (CrowdStrike, Defender, SentinelOne,
Carbon Black) receives a definition or engine update.

**Mechanism**: The update triggers a full re-scan of all binaries on
the device. During the scan window (typically 2–8 hours post-update),
`event.normalized_cpu_usage` spikes to P95 > 80%. Applications
competing for CPU time experience increased freeze rates.

**NQL signature**:
- `package.installations` shows EDR package update within 24h of
  onset
- `device_performance.events`: `normalized_cpu_usage` P95 > 80%
  in the 8h window post-installation
- `execution.events`: `number_of_freezes` elevated for foreground
  applications in the same window
- `dex.scores`: `score.endpoint.software_performance_score_impact`
  elevated on onset date

**Classification**: Causal (if all four signatures present) or
Contributory (if only CPU + freeze, without DEX impact confirmation)

---

### Pattern 2 — OS update → Driver conflict → System crash cluster

**Trigger**: Windows cumulative update or macOS system update
modifying kernel components or driver interfaces.

**Mechanism**: The update changes a kernel API or driver model.
A third-party driver (GPU, network, storage, security) compiled
against the previous API version becomes incompatible. The
incompatibility manifests as a new BSOD error code (Windows) or
kernel panic (macOS) appearing exclusively after the update.

**NQL signature**:
- `package.installations` shows OS update (`package.type == Update`,
  `package.publisher == "Microsoft"` or `"Apple"`) within 72h of
  onset
- `device_performance.system_crashes`: new `system_crash.error_code`
  or `system_crash.label` appearing for the first time post-update
  (no occurrences in the 7 days before the update)
- `dex.scores`: `score.endpoint.device_reliability_value` drops on
  onset date

**Classification**: Causal (new error code + OS update within 72h)

---

### Pattern 3 — Browser update → Extension incompatibility → Freeze cluster

**Trigger**: Chrome, Edge, or Firefox receives a major version update
that changes the extension API or rendering engine.

**Mechanism**: One or more installed extensions (ad blockers, password
managers, corporate security extensions) are incompatible with the
new engine version. The browser process freezes when the extension
attempts to intercept a page load or inject content.

**NQL signature**:
- `package.installations` shows browser update within 24h of onset
- `execution.events`: `number_of_freezes` elevated specifically for
  the browser binary (`binary.product_name` matches browser name)
- `execution.crashes`: `crash.crash_on_start == false` for browser
  binary (freezes, not startup crashes)
- `dex.application_scores`: `application_score.node.type == Freezes`
  score impact elevated for the browser application

**Classification**: Causal (browser update + browser-specific freeze
cluster within 24h)

**Note**: Extension names are not available in NQL. If this pattern
is confirmed, recommend the "Get Browser Extensions" remote action
to identify the incompatible extension.

---

### Pattern 4 — VPN client update → Authentication chain change → Logon delay

**Trigger**: VPN or Zero Trust Network Access (ZTNA) client (Zscaler,
Cisco AnyConnect, GlobalProtect, Netskope) receives an update that
modifies the authentication sequence or certificate validation.

**Mechanism**: The new client version introduces an additional
authentication step (MFA prompt, certificate re-validation, or
proxy initialization) that must complete before network resources
are accessible. This step occurs during the logon sequence, extending
`login.logon_script_duration` because scripts that depend on network
resources must wait for the VPN to initialize.

**NQL signature**:
- `package.installations` shows VPN/ZTNA client update within 24h
  of onset
- `session.logins`: `login.logon_script_duration` P95 increases
  post-installation
- `connection.events`: `event.number_of_no_service_connections`
  elevated in the 0–5 minute window after logon events
- `dex.scores`: `score.endpoint.logon_speed_score_impact` elevated
  on onset date

**Classification**: Causal (VPN update + logon script duration
increase + no_service_connections spike at logon time)

---

### Pattern 5 — Collaboration tool update → Audio quality degradation

**Trigger**: Microsoft Teams or Zoom receives a client update that
changes the audio processing pipeline, codec selection, or hardware
acceleration settings.

**Mechanism**: The new version disables hardware audio acceleration,
changes the default codec, or introduces a regression in the audio
processing thread. Call quality degrades for all calls made after
the update.

**NQL signature**:
- `package.installations` shows Teams or Zoom update within 24h of
  onset
- `collaboration.sessions`: `session.audio.inbound_packet_loss`
  increases post-installation, OR `session.call.quality == poor`
  ratio increases
- `dex.scores`: `score.collaboration.teams_audio_quality_value` or
  `score.collaboration.zoom_audio_quality_value` drops on onset date

**Classification**: Causal (collaboration update + call quality
degradation within 24h)

---

## 7. Mandatory Disclosure Rules

### Rule 1 — Timing is necessary but not sufficient
Never present a change as causal based solely on the fact that it
occurred before the degradation. Always state the mechanism.

**Correct**: *"The Zscaler Client Connector update (2026-07-04 11:02)
is classified as Causal. The update modified the authentication chain,
which is corroborated by a 47-second increase in logon script duration
and an elevated no_service_connections count in the 5 minutes
following each logon event after the installation."*

**Incorrect**: *"The Zscaler update was installed before the DEX drop,
so it likely caused the issue."*

### Rule 2 — Multiple candidates require ranking
When two or more changes are classified as Causal or Contributory,
rank them by the strength of their corroboration and state the
ranking explicitly. Do not present multiple causes as equally likely
without evidence.

### Rule 3 — Unclassified changes must be acknowledged
Every change in the 72h window must appear in the timeline table,
even if classified as Coincidental. Omitting a change from the
timeline is not permitted — the user must be able to see the full
picture and challenge the classification.

### Rule 4 — Confidence ceiling for inferred mechanisms
When the mechanism is inferred (not directly observed in NQL data),
the maximum confidence level for the classification is **Medium**.
A "Causal" classification at High confidence requires at least one
directly observed metric (not inferred) as corroboration.