# ⚔️ Valheim Server - Live Configuration

## 🌐 Connection Information

**Server Address:** `150.136.183.220:2456`  
**World Name:** `upsidedown`  
**Password:** `hellfire666club`  
**Status:** ✅ **ONLINE AND OPERATIONAL**  
**Setup Date:** December 24, 2025

### 🎮 How to Connect

**Steam Players:**
1. Open Valheim
2. Select "Join Game"
3. Click "Add server" 
4. Enter IP: `150.136.183.220:2456`
5. Enter password: `hellfire666club`

**Quick Connect Link (Steam):**
```
steam://rungameid/892970//+connect%20150.136.183.220:2456/
```
Click this link to launch Valheim and connect automatically (password still required)

**Xbox/Game Pass Players:**
1. Open Valheim
2. Select "Join Game"
3. Click "Join IP" 
4. Enter: `150.136.183.220:2456`
5. Enter password: `hellfire666club`

*Note: Join code changes on every server restart. Check logs with `valheim_server logs | grep "join code"` for current code.*

---

## 🔐 SSH Connection

### Windows - Command Prompt/PowerShell (Recommended):
```cmd
ssh -i C:\Users\omnid\.ssh\valheim_key ubuntu@150.136.183.220
```

### Windows - PuTTY:
1. Open PuTTY
2. Host Name: `150.136.183.220`
3. Port: `22`
4. Connection → SSH → Auth → Private key file: `C:\Users\omnid\Desktop\valheim_private.ppk`
5. Click "Open"
6. Login as: `ubuntu`

### Mac/Linux:
```bash
ssh -i ~/.ssh/valheim_key ubuntu@150.136.183.220
```

**SSH Keys Location:**
- OpenSSH format: `C:\Users\omnid\.ssh\valheim_key`
- PuTTY format: `C:\Users\omnid\Desktop\valheim_private.ppk`

---

## 🎮 Server Management Commands

Once connected via SSH, use these commands:

```bash
valheim_server start      # Start the server
valheim_server stop       # Stop the server  
valheim_server restart    # Restart the server
valheim_server update     # Update server when Valheim updates
valheim_server logs       # View recent logs
valheim_server logs-live  # Watch logs in real-time (Ctrl+C to exit)
valheim_server help       # Show all available commands
```

---

## 👑 Admin Access

### Becoming an Admin

**1. Find Your Player ID:**

**Steam Players:**
- Visit https://steamid.io/
- Enter your Steam profile URL
- Copy your "steamID64" number (e.g., `76561198012345678`)

**Xbox/Game Pass Players:**
1. Join the server first
2. SSH into server: `valheim_server logs | grep "Xbox_"`
3. Copy your Xbox ID (format: `Xbox_XXXXXXXXXXXXXXXX`)

**2. Add Yourself as Admin:**
```bash
# SSH into server
ssh -i C:\Users\omnid\.ssh\valheim_key ubuntu@150.136.183.220

# Edit admin list
nano ~/valheim_data/adminlist.txt

# Add your ID (one per line):
# 76561198012345678      (Steam example)
# Xbox_2535416401464117  (Xbox example)

# Save: Ctrl+O, Enter, Ctrl+X

# Restart server to apply
valheim_server restart
```

### Using Admin Commands In-Game

**Enable Console (Steam):**
1. Right-click Valheim in Steam Library
2. Properties → Launch Options
3. Add: `-console`
4. Launch game, press **F5** in-game

**Enable Console (Xbox/Game Pass on PC):**
1. Navigate to: `C:\XboxGames\Valheim\Content\`
2. Right-click `valheim.exe` → Create shortcut
3. Right-click shortcut → Properties → Shortcut tab
4. In Target field, add ` -console` at the end
5. Launch from shortcut, press **F5** in-game

**Enable Console (Xbox Console):**
- Hold: **LB + RB + LT + RT**
- Press: **Menu button**
- Press: **A** to open keyboard

**Admin Commands That Work on Dedicated Servers:**
```
devcommands         # Enable admin commands first
save                # Force save world
stopevent           # Stop current raid/event
kick PlayerName     # Kick a player
ban PlayerName      # Ban a player
unban PlayerName    # Unban a player
banned              # List banned players
```

**Commands That DON'T Work on Dedicated Servers:**
```
god                 # God mode (single-player only)
ghost               # Invisibility (single-player only)
spawn ItemName      # Spawn items (single-player only)
debugmode           # Debug mode (single-player only)
```

---

## 📁 Important File Locations

### Server Files:
- **Installation:** `/home/ubuntu/valheim_server/`
- **World Saves:** `/home/ubuntu/valheim_data/worlds_local/`
- **Admin List:** `/home/ubuntu/valheim_data/adminlist.txt`
- **Credentials:** `/home/ubuntu/server_credentials`
- **Custom Startup:** `/home/ubuntu/valheim_server/start_server.custom.sh`

### World Backup Files:
Location: `/home/ubuntu/valheim_data/worlds_local/`
- `upsidedown.db` - World data
- `upsidedown.fwl` - World metadata

### Automated Backups (Once Configured):
Location: `/home/ubuntu/valheim_backups/`
- Daily backups at 3:00 AM (when configured)
- Format: `backup_YYYYMMDD_HHMMSS/`
- Keeps last 7 backups automatically

---

## 🔧 Configuration Files

### Server Credentials
Edit with: `nano ~/server_credentials`

```bash
SERVER_NAME="My server"
WORLD_NAME="upsidedown"
PASSWORD="hellfire666club"
PUBLIC=0
PORT=2456
```

### Custom Server Flags
Edit with: `nano ~/valheim_server/start_server.custom.sh`

**Example - Disable Raids:**
```bash
VALHEIM_EXTRA_ARGS="${VALHEIM_EXTRA_ARGS} -modifier raids 0"
```

**Example - Reduce Raid Frequency (50%):**
```bash
VALHEIM_EXTRA_ARGS="${VALHEIM_EXTRA_ARGS} -modifier raids 0.5"
```

**Example - Increase Resources (5x):**
```bash
VALHEIM_EXTRA_ARGS="${VALHEIM_EXTRA_ARGS} -modifier resources 5"
```

---

## 🔄 Updating the Server

When Valheim releases an update:

1. **SSH into server**
2. **Stop the server:**
   ```bash
   valheim_server stop
   ```
3. **Update:**
   ```bash
   valheim_server update
   ```
4. **Start the server:**
   ```bash
   valheim_server start
   ```

---

## 💾 Backing Up Your World

### Manual Backup (Quick):
```bash
# SSH into server
cd /home/ubuntu/valheim_data/worlds_local/

# Create timestamped backup
cp upsidedown.db upsidedown.db.backup_$(date +%Y%m%d_%H%M%S)
cp upsidedown.fwl upsidedown.fwl.backup_$(date +%Y%m%d_%H%M%S)
```

### Download Backup via SFTP:
1. Use FileZilla, WinSCP, or any SFTP client
2. Host: `150.136.183.220` | Port: `22`
3. Use your SSH private key for authentication
4. Navigate to: `/home/ubuntu/valheim_data/worlds_local/`
5. Download: `upsidedown.db` and `upsidedown.fwl`

### Restore from Backup:
```bash
# SSH into server
valheim_server stop

# Restore files (replace TIMESTAMP with actual backup timestamp)
cd /home/ubuntu/valheim_data/worlds_local/
cp upsidedown.db.backup_YYYYMMDD_HHMMSS upsidedown.db
cp upsidedown.fwl.backup_YYYYMMDD_HHMMSS upsidedown.fwl

# Start server
valheim_server start
```

### Automated Backups (To Be Configured):
Daily automated backups will be set up at `/home/ubuntu/valheim_backups/`
- Schedule: 3:00 AM daily
- Retention: Last 7 backups kept
- See `IMPLEMENTATION_CHECKLIST.md` for setup instructions

---

## 🤖 Discord Integration (Planned)

### Discord Bot Features (To Be Implemented):
- ✅ Server start/stop notifications
- ✅ Player join/leave alerts
- ✅ Death notifications
- ✅ Automated backup confirmations
- ✅ Server status monitoring

### Bot Commands (Once Configured):
```
!status     # Check server status
!players    # See who's online
!backup     # Create manual backup (admin only)
!restart    # Restart server (admin only)
!join       # Get connection info and join code
```

*Note: Discord bot installation pending. See IMPLEMENTATION_CHECKLIST.md for setup.*

---

## 🛡️ Raid Configuration

### Current Status:
**Raids are ENABLED** (default Valheim behavior)

### To Disable Raids Completely:
```bash
# Edit startup script
nano ~/valheim_server/start_server.custom.sh

# Add this line:
VALHEIM_EXTRA_ARGS="${VALHEIM_EXTRA_ARGS} -modifier raids 0"

# Save and restart
valheim_server restart
```

### Admin Raid Control:
Any admin can stop active raids:
1. Press **F5** (open console)
2. Type: `stopevent`
3. Raid ends immediately

---

## 🚨 Troubleshooting

### Server Won't Start:
```bash
# Check service status
systemctl --user status valheim_server

# View full logs
valheim_server logs | tail -50

# Check for errors
valheim_server logs | grep -i error
```

### Can't Connect from Game:
1. **Verify server is running:**
   ```bash
   valheim_server logs-live
   ```
   Look for "Game server connected"

2. **Check firewall in Oracle Cloud Console:**
   - Ports 2456-2459 UDP should be open
   - Security List should allow ingress traffic

3. **Verify correct IP:**
   ```bash
   curl -s ifconfig.me
   ```
   Should show: `150.136.183.220`

4. **Check password:** `hellfire666club`

### Wrong Password Error:
- Password is case-sensitive: `hellfire666club`
- Check server credentials: `cat ~/server_credentials`

### Join Code for Xbox Players:
```bash
# Get current join code
valheim_server logs | grep "join code"

# Example output:
# Session 'upsidedown' with join code 295265 and IP 150.136.183.220:2456
```
Note: Join code changes every server restart!

### Oracle Idle Instance Warning:
If you receive an email about idle instance reclamation:
1. Log into Oracle Cloud Console
2. Go to Compute → Instances
3. Click "Reboot" or "Start" on your instance
4. This resets the 7-day idle timer

### Server Logs Won't Exit (Ctrl+C not working):
If `valheim_server logs-live` won't exit:
- Close the entire SSH session
- Reconnect via SSH
- Server continues running (systemd keeps it alive)

---

## 📊 Server Monitoring

### Check Server Status:
```bash
# Is the server running?
systemctl --user is-active valheim_server

# View recent activity
valheim_server logs | tail -20

# Get current join code
valheim_server logs | grep "join code"

# Server uptime
uptime
```

### Resource Usage:
```bash
# Memory usage
free -h

# Disk usage
df -h

# CPU usage
top
```

### Performance Stats:
- **Max Players:** 10 (official limit)
- **Server RAM:** 24GB available
- **Server CPU:** 4 OCPUs (ARM64)
- **Expected Performance:** Excellent for 10 players

---

## 🔗 Useful Links

- **Oracle Cloud Console:** https://cloud.oracle.com/
- **Original Setup Repository:** https://github.com/husjon/valheim_server_oci_setup
- **Discord Support:** https://discord.gg/ExnzM4E7pE
- **Valheim Wiki:** https://valheim.fandom.com/wiki/Valheim_Wiki
- **Steam ID Finder:** https://steamid.io/
- **Implementation Guide:** See `IMPLEMENTATION_CHECKLIST.md` in this folder

---

## 📋 Server Specifications

- **Platform:** Oracle Cloud Infrastructure (OCI)
- **Instance Type:** VM.Standard.A1.Flex (Always Free Tier)
- **CPU:** 4 OCPUs (ARM64)
- **RAM:** 24GB
- **OS:** Ubuntu 22.04 LTS (aarch64)
- **Emulation:** FEX (x86_64 emulation for Valheim)
- **Network:** 2456-2459 UDP
- **Crossplay:** ✅ Enabled (experimental)

---

## 📝 Important Notes

- ✅ Server auto-starts on VM reboot (systemd enabled)
- ⚠️ Oracle may reclaim instance if idle for 7+ days (just restart it)
- 🔐 Keep your SSH private key safe - it's your only access method
- 🎮 Crossplay is experimental - may experience occasional crashes
- 💾 Regular backups recommended (automated backups pending setup)
- 🔄 Join code changes every server restart (Xbox/Game Pass players)
- 👥 Max 10 players officially (potentially more with mods)

---

## 🎯 Quick Reference

**Connect to Server:** `150.136.183.220:2456` (Password: `hellfire666club`)  
**SSH Command:** `ssh -i C:\Users\omnid\.ssh\valheim_key ubuntu@150.136.183.220`  
**Restart Server:** `valheim_server restart`  
**View Logs:** `valheim_server logs`  
**Stop Raid:** Press F5, type `stopevent`  
**Force Save:** Press F5, type `save`

---

## ✅ Next Steps (Pending)

See `IMPLEMENTATION_CHECKLIST.md` for detailed setup instructions:

- [ ] Configure automated daily backups
- [ ] Set up admin access for all admins
- [ ] Install Discord bot for notifications
- [ ] Configure raid settings (if desired)
- [ ] Test Xbox player admin access
- [ ] Set up Discord server roles and permissions

---

## ✨ Server is Live and Ready!

Your Valheim dedicated server is fully operational and free to run 24/7 on Oracle's Always Free tier!

**Share this info with your players:**
- **Server:** `150.136.183.220:2456`
- **Password:** `hellfire666club`
- **World:** `upsidedown`

**Happy Viking-ing!** ⚔️🛡️🏰

---

**Last Updated:** December 24, 2025  
**Status:** Operational | **Uptime:** Since December 24, 2025
