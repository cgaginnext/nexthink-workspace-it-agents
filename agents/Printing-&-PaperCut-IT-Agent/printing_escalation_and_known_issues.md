# Printing — Escalation Procedures & Known Issues

## L1 → L2 Escalation Checklist
Before escalating, confirm you have collected:
- [ ] Device name and OS version (from Nexthink or user)
- [ ] PaperCut client version (from Nexthink package inventory or Help → About)
- [ ] Print Spooler service status at time of issue
- [ ] Output of Reset Print Spooler remote action (if run)
- [ ] Timestamp of first occurrence
- [ ] Recent package installs/uninstalls on the device (from Nexthink)
- [ ] Whether the issue is isolated to one device or affects multiple users/devices
- [ ] PaperCut server reachability result (ping + port 9191 test)
- [ ] Relevant Event IDs from Windows Event Viewer (7031, 7034, application faults)

## L2 → L3 / Vendor Escalation Criteria
Escalate to L3 or PaperCut vendor support when:
- PaperCut Application Server service is crashing repeatedly
- Database corruption suspected (errors in server.log referencing DB)
- Print jobs disappear from PaperCut queue without being printed (data loss)
- Licensing errors appear in PaperCut Admin Console
- Issue affects all users across multiple sites simultaneously

## Known recurring issues

### Spooler crash after Windows Update
- **Symptom:** Spooler stops immediately after a Windows cumulative update
- **Root cause:** Incompatible third-party printer driver not updated alongside OS
- **Fix:** Identify driver via Event Viewer (faulting module), remove and reinstall from vendor
- **Nexthink action:** Query recent Windows Update installs on affected devices; cross-reference with crash timestamps

### PaperCut client loses server connection after network change
- **Symptom:** PCClient.exe running but popup absent; port 9191 unreachable
- **Root cause:** VPN split-tunneling or firewall rule blocking PaperCut traffic after network profile change
- **Fix:** Verify firewall rules for port 9191; check VPN split-tunnel configuration
- **Nexthink action:** Use device network connectivity view to check for TCP connection failures around the time of the issue

### PaperCut popup appears but job is not released
- **Symptom:** User confirms job in popup, but nothing prints
- **Root cause:** Print server queue full, or PaperCut Print Provider service stopped on the print server
- **Fix:** Check Print Provider service on the print server; clear server-side queue

### Ghost printers after OS upgrade
- **Symptom:** Printers appear in the list but fail with "Driver unavailable"
- **Root cause:** In-place Windows upgrade does not migrate all printer drivers correctly
- **Fix:** Remove all ghost printers, reinstall via GPO push or PaperCut Mobility Print

## Useful contacts
| Team | Scope |
|------|-------|
| L2 Desktop Engineering | Driver issues, Spooler recurring crashes, OS-level problems |
| L2 Print Server Team | PaperCut server, print server, queue management |
| Network Team | Port 9191 firewall rules, VPN split-tunnel |
| PaperCut Vendor Support | Application Server crashes, licensing, DB issues |
