# Windows Print Spooler — Troubleshooting Guide

## What is the Print Spooler?
The Print Spooler is a Windows service that manages print jobs sent to local and
network printers. If it crashes or hangs, all printing stops on the device.
The service short name is Spooler and the process it runs is called spoolsv.exe.

## Common symptoms
- Print jobs stuck in queue and cannot be deleted
- "Printer not available" or "Error" status on all printers
- Spooler service shows as Stopped in the Services console
- Blue screen error referencing a print driver file

## Automated remediation (preferred)
Use the Reset Print Spooler remote action in Nexthink Infinity.
This action automatically performs the following steps on the targeted device:
- Stops the Spooler service and any dependent services
- Clears all files in the Windows print spool folder
- Clears all pending print job objects
- Restarts the Spooler service
- Returns a report of cleared jobs, cleared files, and final service status

When to use: any time the print queue is stuck or the Spooler service is stopped.
Safe to run without user impact beyond a brief print interruption.

## Manual steps (if the remote action is unavailable)
Perform the following steps in order on the affected device:

1. Open the Services console by typing services.msc in the Windows Run dialog.
   Locate the Print Spooler service and stop it.
   If the service does not respond, use Task Manager to end the spoolsv.exe process.

2. Open File Explorer and navigate to the Windows print spool folder.
   It is located inside the Windows system folder, inside System32,
   inside a folder called spool, inside a folder called PRINTERS.
   Delete all files inside this folder. Do not delete the folder itself.

3. Return to the Services console and start the Print Spooler service.

4. Ask the user to retry printing and confirm the issue is resolved.

## Recurring Spooler crashes — investigation path
If the Spooler restarts successfully but crashes again within minutes, follow these steps:

1. Open Windows Event Viewer and filter the System log for Event ID 7031 or 7034.
   These events indicate the Spooler service terminated unexpectedly.

2. Open the Application log and look for a faulting application entry referencing spoolsv.exe.
   Note the faulting module name — this is typically a third-party printer driver file.

3. Open Print Management by typing printmanagement.msc in the Windows Run dialog.
   Review the installed drivers list and identify the driver matching the faulting module.

4. Remove the suspect driver from Print Management by right-clicking and selecting Delete.

5. Reinstall the driver from the vendor's official source or from the print server.

6. Restart the Spooler service and confirm stability.

## Key locations for L2 reference
- The active spool folder is inside the Windows system folder,
  inside System32, inside spool, inside PRINTERS.
- The 64-bit driver store is inside the Windows system folder,
  inside System32, inside spool, inside drivers, inside x64, inside folder 3.
- Printer configuration is stored in the Windows registry under
  CurrentControlSet, then Control, then Print, then Printers.

## Escalation to L2
When escalating, provide the following:
- Device name and Windows OS build number
- Name and version of the suspect printer driver
- Event IDs collected from Event Viewer (7031, 7034, application fault entries)
- Timestamp of the crash
- Output report from the Reset Print Spooler remote action if it was run