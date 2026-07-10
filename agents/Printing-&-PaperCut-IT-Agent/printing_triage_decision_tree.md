# Printing Issue Triage — Decision Tree for L1/L2

## Step 1 — Scope the problem
- Is the issue affecting **one user**, **one device**, or **multiple devices/sites**?
  - One user → go to Step 2
  - One device → go to Step 3
  - Multiple devices → go to Step 5 (server-side check)

## Step 2 — User-level checks
- Can the user see the printer in their printer list?
  - No → check if printer is deployed via GPO or PaperCut; verify user's OU membership
  - Yes → is the print job stuck in the queue?
    - Yes → go to Step 3 (Spooler reset)
    - No → check PaperCut client popup (Step 4)

## Step 3 — Print Spooler diagnosis
- Check Spooler service status: `services.msc` → Print Spooler → Status
- If **Stopped** or **Not Responding**:
  1. Run the **Reset Print Spooler** remote action via Nexthink (clears queue + restarts service)
  2. Verify service restarts successfully
  3. Ask user to retry printing
- If Spooler restarts but issue recurs within minutes:
  - Check for corrupted driver: `printmanagement.msc` → Drivers
  - Remove and reinstall the affected printer driver
  - If BSOD or system crash accompanies Spooler crash → escalate to L2 with crash dump reference

## Step 4 — PaperCut client checks
- Is the PaperCut client process running? (`PCClient.exe` in Task Manager)
  - No → restart client: `C:\Program Files\PaperCut MF Client\pc-client.exe`
  - Yes → is the popup appearing at print time?
    - No → check PaperCut server connectivity (Step 5)
    - Yes but authentication fails → check user account in PaperCut admin console

## Step 5 — Server-side / network checks
- Ping the PaperCut server from the affected device
- Check PaperCut Application Server service on the server
- Verify TCP port 9191 (PaperCut default) is reachable
- Check if multiple devices are affected simultaneously → likely server or network issue → escalate to L2/L3

## Escalation criteria to L2
- Spooler crash recurs after reset
- PaperCut server unreachable from multiple devices
- Driver corruption confirmed
- BSOD linked to printing
- Issue persists after all L1 steps