# Nexthink — Printing Data Factualisation Guide

Use Nexthink Infinity to factualise printing issues before and during troubleshooting.
All queries below can be run via Nexthink Assist or Investigations.

## 1. Identify devices running PaperCut
Ask Nexthink Assist:
> "List all devices with PaperCut installed in the past 30 days, including device name, OS, and last seen date."

**Context:** 17 devices are currently active with PaperCut in the environment (all Windows 11).

## 2. Check for PaperCut application crashes
Ask Nexthink Assist:
> "List application crash events for PaperCut in the past 7 days, grouped by device and binary name."

Use this to identify devices with recurring PaperCut client instability.

## 3. Check Print Spooler service status
Ask Nexthink Assist:
> "Show remote action execution results for Reset Print Spooler in the past 30 days, including device name, status, and output."

This surfaces devices where the Spooler has already been reset and whether the action succeeded.

## 4. Correlate with recent package changes
Ask Nexthink Assist:
> "List package install and uninstall events on device [DEVICE_NAME] in the past 7 days."

Use this to identify if a driver update or PaperCut client update preceded the issue.

## 5. Check for system crashes linked to printing
Ask Nexthink Assist:
> "List system crash events on device [DEVICE_NAME] in the past 7 days, including error code and timestamp."

Cross-reference crash timestamps with print job activity to confirm Spooler-related BSODs.

## 6. Scope a potential fleet-wide issue
Ask Nexthink Assist:
> "How many devices have had PaperCut execution events in the past 24 hours?"

A sudden drop in this count may indicate a PaperCut server outage or network issue.

## Available remote action
**Reset Print Spooler** — available in Nexthink Infinity
- Clears the print queue, restarts the Spooler service and dependent services
- Platform: Windows only
- Output: cleared job files, cleared print jobs, service status
- Run from: Device View → Remote Actions, or via Nexthink Assist on a named device