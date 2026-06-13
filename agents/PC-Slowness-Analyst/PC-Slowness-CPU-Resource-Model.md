# PC Slowness CPU Resource Model

## Purpose

This file defines how the PC Slowness Analyst should analyze CPU and memory resource consumption for degraded devices.

The goal is to understand how much resource is consumed by IT, system, security, management, and user activity, and to estimate the remaining usable capacity for the employee.

---

## Analysis scope

Use this model when the user asks for:

- CPU Queue Length analysis
- CPU consumption distribution
- Memory consumption distribution
- Comparison between degraded and healthy devices
- Resource usage by device model
- Remaining resources available for the user
- Working-hours-only analysis
- User-present-period analysis

---

## Device filtering

Default filters:

- Include only devices matching the agreed device name pattern
- Exclude servers
- Include only active Windows workstations
- Focus on user-present working periods when requested
- Prioritize devices with user-visible slowness or degraded performance signals

---

## Degraded device criteria

A device may be classified as degraded when one or more of the following are true:

- CPU Queue Length is above the recommended threshold
- CPU usage is repeatedly high during working hours
- Memory usage is repeatedly high during working hours
- Available memory is low
- Device performance score is poor
- Boot or logon duration is repeatedly high
- User complaints or poor sentiment exist
- Device cumulates several hygiene issues

For CPU Queue Length, the user-defined threshold can be:

- Greater than 2 times the number of logical cores

Example:

- 4 logical cores: degraded if CPU Queue Length > 8
- 8 logical cores: degraded if CPU Queue Length > 16

---

## Resource categories

### 1. Security & Network

Include:

- Microsoft Defender
- EDR tools
- Antivirus tools
- Zscaler
- Nessus
- TeamViewer if used as remote support or endpoint access tool

Processes:

- `msmpeng.exe`
- `mssense.exe`
- `nissrv.exe`
- `sensendr.exe`
- `zsatraymanager.exe`
- `zsatunnel.exe`
- `zsatray.exe`
- `zsaupm.exe`
- `zdpservice.exe`
- `zepservice.exe`
- `zdpapp.exe`
- `nessus-service.exe`
- `teamviewer_service.exe`
- `teamviewer_desktop.exe`
- `teamviewer.exe`

---

### 2. Windows System

Include native Windows system activity.

Processes:

- `svchost.exe`
- `system`
- `services.exe`
- `lsass.exe`
- `dwm.exe`
- `explorer.exe`
- `searchindexer.exe`
- `wmiapsrv.exe`
- `audiodg.exe`
- `dptf_helper.exe`
- `ipf_helper.exe`

Also include other native Windows processes when clearly identifiable.

---

### 2.1. System Management & Deployment

Include endpoint management, deployment, update, inventory, and IT tooling.

Processes:

- `wmiprvse.exe`
- `ccmexec.exe`
- `ccmsetup.exe`
- `microsoft.management.services.intunewindowsagent.exe`
- `agentexecutor.exe`
- `clienthealtheval.exe`
- `clientcertcheck.exe`
- `sensorlogontask.exe`
- `omadmclient.exe`
- `deviceenroller.exe`
- `mdmappinstaller.exe`
- `usoclient.exe`
- `wuaucltcore.exe`
- `adaptivaclientservice.exe`
- `msiexec.exe`
- `lrs.personalprint.service.exe`
- `pdqinventory-scanner-1.exe`
- `pdqinventoryscanner.exe`
- `remotedesktopmanager.exe`

---

### 3. User Workspace

This category represents the resource left for actual user activity and business work.

Split it into subcategories.

---

#### 3.1. Productivity & Collaboration

Processes:

- `ms-teams.exe`
- `outlook.exe`
- `olk.exe`
- `olkbg.exe`
- `excel.exe`
- `winword.exe`
- `powerpnt.exe`
- `officeclicktorun.exe`
- `zoom.exe`
- `slack.exe`
- `shift.exe`

---

#### 3.2. Storage and synchronization

Processes:

- `onedrive.exe`
- `onedrive.sync.service.exe`

---

#### 3.3. Browsers

Processes:

- `msedge.exe`
- `msedgewebview2.exe`
- `chrome.exe`
- `brave.exe`
- `chromium.exe`
- `firefox.exe`


---

#### 3.4. Engineering, CAD, BIM

Processes:

- `acad.exe`
- `acadlt.exe`
- `inventor.exe`
- `revit.exe`
- `robot.exe`
- `sciaengineer.exe`
- `connectivity.vaultpro.exe`
- `adskidentitymanager.exe`

---

#### 3.5. Simulation and energy tools

Processes:

- `pleiadescalc.exe`
- `pleiadesmod.exe`
- `pleiadesres.exe`
- `caneco5.exe`

---

#### 3.6. Business applications

Processes:

- `saplogon.exe`
- `pbidesktop.exe`
- `seirich-entreprise.exe`
- `xlpro4.exe`
- `comet.exe`

---

#### 3.7. Development and IT tools

Processes:

- `code.exe`
- `devenv.exe`
- `dbeaver.exe`
- `pythonw.exe`
- `javaw.exe`
- `jp2launcher.exe`
- `sqlservr.exe`
- `treesizefree.exe`

---

### 4. Non-business or out-of-scope usage

Isolate when present.

Processes:

- `geforcenow.exe`
- `spotify.exe`
- `anki.exe`
- `perplexity.exe`
- `z-suite.exe`

Do not overstate conclusions. Mention these only when the resource consumption is significant and occurs during working hours.

---

## Expected visual outputs

When data is available, produce:

### 1. Stacked bar chart by model

Show CPU distribution by model:

- Security & Network
- Windows System
- System Management & Deployment
- User Workspace
- Non-business or out-of-scope usage

### 2. Global pie chart

Show global CPU distribution across all degraded devices.

### 3. Summary table

Include:

- Model
- Number of impacted devices
- Average CPU Queue Length
- Average CPU usage
- Average memory usage
- Percentage CPU by category
- Remaining CPU for user
- Remaining memory for user

### 4. Degraded vs healthy comparison

Compare:

- Number of degraded devices
- Number of healthy devices
- CPU usage by category
- Memory usage by category
- CPU Queue Length difference
- Remaining CPU difference
- Remaining memory difference

---

## Remaining user capacity

The agent should estimate the remaining resource available for user work.

### CPU remaining capacity

Calculate or estimate:

- Total CPU capacity
- CPU used by Security & Network
- CPU used by Windows System
- CPU used by Management & Deployment
- CPU used by user applications
- CPU used by non-business applications
- Remaining CPU headroom

### Memory remaining capacity

Calculate or estimate:

- Total memory
- Memory used by system and IT tools
- Memory used by user applications
- Available memory
- Memory pressure
- Remaining memory headroom

---

## Recommended interpretation

### If Security & Network is high

Possible causes:

- Antivirus scan during working hours
- EDR activity
- Zscaler tunnel or service issue
- Nessus scan
- Remote support tool activity

Recommended actions:

- Validate scan schedule
- Check policy timing
- Confirm whether activity happens during user-present periods
- Compare degraded and healthy devices
- Escalate to security owner if systemic

---

### If Windows System is high

Possible causes:

- Windows services activity
- Search indexing
- Driver issue
- Desktop Window Manager activity
- Audio, graphics, or hardware service issue
- Windows maintenance

Recommended actions:

- Check Windows health
- Check drivers and firmware
- Review indexing behavior
- Check reboot age
- Validate update status

---

### If Management & Deployment is high

Possible causes:

- MECM client activity
- Intune agent activity
- Software deployment
- Update installation
- Inventory scan
- Client repair loop
- Repeated installer activity

Recommended actions:

- Check deployment schedule
- Identify repeated installations
- Validate client health
- Move heavy tasks outside working hours where possible
- Compare affected and healthy devices

---

### If User Workspace is high

Possible causes:

- Teams or browser resource pressure
- Outlook synchronization
- OneDrive sync backlog
- Heavy Excel or business workload
- CAD, BIM, simulation, or development workloads
- Insufficient hardware for the user profile

Recommended actions:

- Identify top consuming user applications
- Separate normal business workload from abnormal behavior
- Check whether device model is adapted to workload
- Consider memory upgrade or replacement for heavy users
- Review OneDrive sync health

---

### If Non-business usage is high

Possible causes:

- Personal or non-business applications consuming resources during working hours

Recommended actions:

- Mention objectively
- Quantify the impact
- Avoid judgmental wording
- Ask whether these processes are allowed or expected
- Treat only if consumption is significant

---

## Output format

## CPU and memory resource analysis

### Scope

- Device pattern:
- Period:
- Working hours only:
- User-present periods:
- Degraded device criteria:
- Number of degraded devices:
- Number of healthy comparison devices:

### Executive summary

Short summary of the main resource pressure and whether it seems structural.

### CPU distribution by model

Describe the stacked bar chart.

### Global CPU distribution

Describe the global pie chart.

### Summary table

Include model-level results.

### Degraded vs healthy comparison

Explain the main differences.

### Remaining user capacity

- Remaining CPU:
- Remaining memory:
- Most constrained models:
- Most constrained user populations:

### Recommendations by category

#### Security & Network

Actions.

#### Windows System

Actions.

#### Management & Deployment

Actions.

#### User Workspace

Actions.

#### Non-business or out-of-scope usage

Actions if relevant.

### Questions for confirmation

Ask the user to confirm:

- Are these process categories correct for your environment?
- Are some tools missing?
- Are some tools owned by another team?
- Should some processes be reclassified?
- Are the proposed actions feasible?