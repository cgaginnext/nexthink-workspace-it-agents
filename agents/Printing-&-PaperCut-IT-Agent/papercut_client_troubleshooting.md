# PaperCut Client — Troubleshooting Guide for L1/L2

## Architecture overview
- **PaperCut Application Server**: Central server managing print jobs, quotas, and user accounts
- **PaperCut Client (PCClient.exe)**: Runs on the user's device; shows popup for job release, quota info, and authentication
- **PaperCut Print Provider**: Intercepts print jobs on the print server and routes them through PaperCut

## Common issues and resolutions

### Issue: PaperCut popup does not appear at print time
**Cause:** PCClient.exe not running, or cannot reach the PaperCut server
1. Verify `PCClient.exe` is running in Task Manager
2. If not running, start it: `"C:\Program Files\PaperCut MF Client\pc-client.exe"`
3. Check connectivity to PaperCut server: `Test-NetConnection -ComputerName <PaperCutServer> -Port 9191`
4. If port unreachable → network/firewall issue → escalate to L2/network team
5. If port reachable but popup still absent → check PaperCut client config file:
   `C:\Program Files\PaperCut MF Client\client.conf`
   Verify `server-ip` or `server-name` is correct

### Issue: Authentication failure / "Invalid credentials"
1. Confirm the user's domain account is synced in PaperCut Admin Console → Users
2. Check if the user's account is disabled or has zero balance with restricted printing
3. Reset the user's PaperCut password if using local PaperCut authentication
4. If using SSO/AD sync, verify the sync job ran recently (Admin Console → Options → User/Group Sync)

### Issue: Print job sent but never appears in PaperCut queue
1. Verify the printer is managed by PaperCut (Admin Console → Printers)
2. Check the PaperCut Application Server service is running on the server
3. Review PaperCut server logs: `[PaperCut Install Dir]\server\logs\server.log`
4. Check for "hold/release" queue configuration — job may be held pending release

### Issue: PaperCut client crashes or freezes
1. Check PaperCut client version: Help → About in the client, or via Nexthink package inventory
2. Compare against the latest supported version for your PaperCut server version
3. Review client logs: `%APPDATA%\PaperCut MF Client\logs\`
4. Reinstall the client if logs show repeated exceptions

### Issue: "Quota exceeded" error
1. Check user's balance in PaperCut Admin Console → Users → [username] → Account Balance
2. Top up balance if policy allows, or redirect user to request a quota increase
3. If balance is correct but error persists → check for caching issue; restart PCClient.exe

## Key ports
| Port | Purpose |
|------|---------|
| 9191 | PaperCut client ↔ server (HTTP) |
| 9192 | PaperCut client ↔ server (HTTPS) |
| 9193 | PaperCut Web Print |

## Log locations
| Component | Log path |
|-----------|---------|
| PaperCut Server | `[Install Dir]\server\logs\server.log` |
| PaperCut Client | `%APPDATA%\PaperCut MF Client\logs\` |
| Windows Event Log | Application + System logs, source: PaperCut |