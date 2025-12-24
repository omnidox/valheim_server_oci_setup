# ⚔️ Valheim Server - Live Configuration

## 🌐 Connection Information

**Server Address:** `150.136.183.220:2456`  
**World Name:** `upsidedown`  
**Status:** ✅ **ONLINE AND OPERATIONAL**  
**Setup Date:** December 24, 2024

---

## 🔐 SSH Connection

### Via PuTTY (Windows):
1. Open PuTTY
2. Load session: "Valheim Server"
3. Click Open
4. Login as: `ubuntu`

### Via Command Prompt/PowerShell (Windows):
```cmd
ssh -i C:\path\to\valheim_private.ppk ubuntu@150.136.183.220
```

**Note:** For CMD/PowerShell, you need to convert your `.ppk` key to OpenSSH format first, OR you can use PuTTY's `plink` command:
```cmd
plink -i C:\path\to\valheim_private.ppk ubuntu@150.136.183.220
```

### Via Terminal (Mac/Linux):
```bash
ssh ubuntu@150.136.183.220
```

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
```

---

## 📁 Important File Locations

### Server Files:
- **Installation:** `/home/ubuntu/valheim_server/`
- **World Saves:** `/home/ubuntu/valheim_data/worlds_local/`
- **Credentials:** `/home/ubuntu/server_credentials`
- **Custom Startup:** `/home/ubuntu/valheim_server/start_server.custom.sh`

### Backups:
To backup your world, download these files:
- `upsidedown.db`
- `upsidedown.fwl`

Location: `/home/ubuntu/valheim_data/worlds_local/`

---

## 🔧 Configuration Files

### Server Credentials
Edit with: `nano ~/server_credentials`

```bash
SERVER_NAME="My server"
WORLD_NAME="upsidedown"
PASSWORD="your_password_here"
PUBLIC=0
PORT=2456
```

### Custom Server Flags
Edit with: `nano ~/valheim_server/start_server.custom.sh`

Add custom flags or environment variables here.

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

### Manual Backup via SFTP:
1. Use FileZilla or WinSCP
2. Connect to: `150.136.183.220` (port 22)
3. Use your private key for authentication
4. Navigate to: `/home/ubuntu/valheim_data/worlds_local/`
5. Download: `upsidedown.db` and `upsidedown.fwl`

### Automated Backup Script:
Create a backup script in the server:

```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/home/ubuntu/backups"
mkdir -p $BACKUP_DIR
cp /home/ubuntu/valheim_data/worlds_local/upsidedown.* $BACKUP_DIR/backup_$DATE/
```

---

## 🚨 Troubleshooting

### Server Won't Start:
```bash
# Check service status
systemctl --user status valheim_server

# View full logs
valheim_server logs | tail -50
```

### Can't Connect from Game:
1. Verify server is running: `valheim_server logs-live`
2. Check firewall rules in Oracle Cloud Console
3. Confirm IP is correct: `curl -s ifconfig.me`
4. Ensure ports 2456-2459 UDP are open

### Oracle Idle Instance Warning:
If you receive an email about idle instance reclamation:
1. Log into Oracle Cloud Console
2. Go to Compute → Instances
3. Click "Restart" on your instance
4. This resets the idle timer

---

## 📊 Server Monitoring

### Check Server Status:
```bash
# Is the server running?
systemctl --user is-active valheim_server

# Current connections
valheim_server logs | grep "Connections"

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

---

## 🔗 Useful Links

- **Oracle Cloud Console:** https://cloud.oracle.com/
- **Original Repository:** https://github.com/husjon/valheim_server_oci_setup
- **Discord Support:** https://discord.gg/ExnzM4E7pE
- **Valheim Wiki:** https://valheim.fandom.com/wiki/Valheim_Wiki

---

## 📝 Notes

- Server auto-starts on VM reboot (systemd enabled)
- Oracle may reclaim instance if idle for 14 days (just restart it)
- Keep your SSH private key safe - it's your access to the server
- Consider sharing connection info with friends: `150.136.183.220:2456`
- Password is stored in: `~/server_credentials`

---

## ✅ Setup Completed Successfully!

Your Valheim dedicated server is fully operational and free to run 24/7 on Oracle's Always Free tier!

**Happy Viking-ing!** ⚔️🛡️🏰
