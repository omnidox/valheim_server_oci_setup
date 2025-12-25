# Valheim Server Implementation Checklist

**Created**: December 24, 2025  
**Server Status**: ✅ LIVE AND OPERATIONAL  
**Server IP**: 150.136.183.220  
**World Name**: upsidedown

---

## 🎯 IMPLEMENTATION PLAN - Step-by-Step

### Session Goals
Complete all 6 priority items to have a fully managed, automated Valheim server with Discord integration.

---

## ✅ Step 1: Set Up Automated Backups

**Priority**: 🔴 CRITICAL  
**Time Estimate**: 10 minutes  
**Dependencies**: SSH access (already configured)

### Tasks
- [ ] Create backup directory structure
- [ ] Write backup script (`~/backup_world.sh`)
- [ ] Make script executable
- [ ] Test manual backup
- [ ] Configure cron job for daily backups (3:00 AM)
- [ ] Verify cron job is active
- [ ] Document backup locations

### Commands to Run
```bash
# 1. Create backup directory
mkdir -p ~/valheim_backups

# 2. Create backup script
nano ~/backup_world.sh
# (paste script content)

# 3. Make executable
chmod +x ~/backup_world.sh

# 4. Test backup
~/backup_world.sh

# 5. Set up cron job
crontab -e
# Add: 0 3 * * * /home/ubuntu/backup_world.sh

# 6. Verify cron
crontab -l
```

### Backup Script Content
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/home/ubuntu/valheim_backups"
WORLD_DIR="/home/ubuntu/valheim_data/worlds_local"
WORLD_NAME="upsidedown"

# Create timestamped backup directory
mkdir -p "$BACKUP_DIR/backup_$DATE"

# Copy world files
cp "$WORLD_DIR/$WORLD_NAME.db" "$BACKUP_DIR/backup_$DATE/"
cp "$WORLD_DIR/$WORLD_NAME.fwl" "$BACKUP_DIR/backup_$DATE/"

# Log result
echo "$(date): Backup created at $BACKUP_DIR/backup_$DATE/" >> ~/backup.log

# Keep only last 7 backups (optional cleanup)
cd "$BACKUP_DIR"
ls -t | tail -n +8 | xargs -r rm -rf
```

### Success Criteria
- ✅ Manual backup test succeeds
- ✅ Backup files exist in ~/valheim_backups/
- ✅ Cron job shows in `crontab -l`
- ✅ backup.log file created

---

## ✅ Step 2: Configure Admin Access

**Priority**: 🔴 CRITICAL  
**Time Estimate**: 5 minutes  
**Dependencies**: Need your Steam ID (or Xbox ID if Xbox player)

### Tasks
- [ ] Find your Steam ID or Xbox ID
- [ ] Create/edit adminlist.txt file
- [ ] Add your ID to the list
- [ ] Restart server to load admin list
- [ ] Verify admin list loaded in logs

### Commands to Run
```bash
# 1. Create/edit admin list
nano ~/valheim_data/adminlist.txt

# 2. Add your Steam ID (one per line)
# Example:
# 76561198012345678

# 3. Restart server
valheim_server restart

# 4. Check logs for admin confirmation
valheim_server logs | grep -i admin
```

### How to Find Your Steam ID

**Method 1: Steam Profile URL**
1. Go to your Steam profile
2. Right-click anywhere → "Copy Page URL"
3. Go to https://steamid.io/
4. Paste your profile URL
5. Copy the "steamID64" number

**Method 2: Steam Console**
1. In Steam, press `Win+R`
2. Type: `steam://nav/console`
3. In console: `status`
4. Look for your Steam ID

### How to Find Xbox ID (If Xbox Player)
1. Join the server first
2. SSH into server
3. Run: `valheim_server logs | grep "Xbox_"`
4. Copy the `Xbox_XXXXXXXXXXXXXXXX` ID

### Success Criteria
- ✅ adminlist.txt file exists
- ✅ Your ID is in the file
- ✅ Server logs show "Loaded admin list with X entries"
- ✅ Ready to test admin commands in-game

---

## ✅ Step 3: Test Admin Commands In-Game

**Priority**: 🟡 HIGH  
**Time Estimate**: 5 minutes  
**Dependencies**: Step 2 complete, Valheim game launched

### Tasks
- [ ] Launch Valheim
- [ ] Connect to server (150.136.183.220:2456)
- [ ] Open console (F5)
- [ ] Enable dev commands
- [ ] Test basic admin commands
- [ ] Verify commands work
- [ ] Document which commands are available

### In-Game Testing Steps

**1. Enable Console (Steam)**
- Right-click Valheim in Steam Library
- Properties → Launch Options
- Add: `-console`
- Launch game

**2. Enable Console (Xbox/Game Pass on PC)**
- Navigate to `C:\XboxGames\Valheim\Content\`
- Right-click `valheim.exe` → Create shortcut
- Right-click shortcut → Properties
- In Target, add ` -console` at the end
- Launch from shortcut

**3. Open Console In-Game**
- Press **F5**

**4. Test Commands**
```
devcommands         # Enable admin/dev commands
save                # Force save (should work)
stopevent           # Stop any active raid (should work)
```

### Commands to Test

**✅ THESE WORK ON DEDICATED SERVERS:**
```
save              - Force world save
stopevent         - Stop current event/raid
kick PlayerName   - Kick a player
ban PlayerName    - Ban a player
banned            - List banned players
```

**❌ THESE DON'T WORK ON DEDICATED SERVERS:**
```
god               - God mode (single-player only)
ghost             - Invisibility (single-player only)
spawn ItemName    - Spawn items (single-player only)
debugmode         - Debug mode (single-player only)
```

### Success Criteria
- ✅ Console opens with F5
- ✅ `devcommands` command shows "Dev commands: True"
- ✅ `save` command shows "Saving..." message
- ✅ No "not an admin" errors

---

## ✅ Step 4: Create Discord Server (or Designate Existing)

**Priority**: 🟡 HIGH  
**Time Estimate**: 5 minutes  
**Dependencies**: Discord account

### Option A: Create New Discord Server (RECOMMENDED)

**Tasks**
- [ ] Open Discord
- [ ] Click "+" button to add server
- [ ] Choose "Create My Own"
- [ ] Select "For me and my friends"
- [ ] Name it (e.g., "Valheim - upsidedown")
- [ ] Create server

**Channel Setup**
- [ ] Create #server-info channel (pinned server IP, password)
- [ ] Create #server-status channel (for bot notifications)
- [ ] Create #general channel (player chat)
- [ ] Create #admin channel (admin-only)

### Option B: Use Existing Discord Server

**Tasks**
- [ ] Choose which server to use
- [ ] Create #valheim channel
- [ ] Create #valheim-status channel (for bot)

### Success Criteria
- ✅ Discord server exists
- ✅ Channels created
- ✅ Bot notification channel designated
- ✅ Ready for webhook creation

---

## ✅ Step 5: Install Discord Bot (Python Log Parser)

**Priority**: 🟠 MEDIUM  
**Time Estimate**: 15-20 minutes  
**Dependencies**: Step 4 complete, SSH access

### Tasks
- [ ] Create Discord webhook
- [ ] Install Python dependencies on server
- [ ] Clone bot repository
- [ ] Configure bot settings
- [ ] Create systemd service
- [ ] Start bot service
- [ ] Test notifications

### Step-by-Step Commands

**1. Create Discord Webhook**
- Discord → Server Settings → Integrations → Webhooks
- Click "New Webhook"
- Name: "Valheim Bot"
- Select channel: #server-status
- Copy webhook URL (you'll need this)

**2. SSH into Server and Install Dependencies**
```bash
# Update system
sudo apt update

# Install Python3 and pip if not already installed
sudo apt install -y python3 python3-pip git

# Verify installation
python3 --version
pip3 --version
```

**3. Clone Bot Repository**
```bash
cd ~
git clone https://github.com/jaumebecks/valheim-server-notifier.git
cd valheim-server-notifier
```

**4. Install Python Dependencies**
```bash
pip3 install -r requirements.txt
```

**5. Configure Bot**
```bash
# Copy example config
cp config.example.json config.json

# Edit config
nano config.json
```

**config.json content:**
```json
{
  "webhook_url": "YOUR_DISCORD_WEBHOOK_URL_HERE",
  "log_file": "/home/ubuntu/.config/unity3d/IronGate/Valheim/Player.log",
  "check_interval": 5,
  "notifications": {
    "server_start": true,
    "server_stop": true,
    "player_join": true,
    "player_leave": true,
    "player_death": true
  }
}
```

**6. Create Systemd Service**
```bash
sudo nano /etc/systemd/system/valheim-discord-bot.service
```

**Service file content:**
```ini
[Unit]
Description=Valheim Discord Notifier Bot
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/valheim-server-notifier
ExecStart=/usr/bin/python3 /home/ubuntu/valheim-server-notifier/notifier.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

**7. Enable and Start Bot**
```bash
sudo systemctl daemon-reload
sudo systemctl enable valheim-discord-bot
sudo systemctl start valheim-discord-bot

# Check status
sudo systemctl status valheim-discord-bot
```

### Testing Bot
```bash
# Restart Valheim server to trigger notification
valheim_server restart

# Check Discord for "Server starting..." message
# Join server to trigger "Player joined" message
```

### Troubleshooting
```bash
# View bot logs
sudo journalctl -u valheim-discord-bot -f

# Restart bot
sudo systemctl restart valheim-discord-bot

# Stop bot
sudo systemctl stop valheim-discord-bot
```

### Success Criteria
- ✅ Bot service running (`systemctl status` shows active)
- ✅ Discord receives test notification
- ✅ Player join/leave events appear in Discord
- ✅ Bot auto-starts on server reboot

---

## ✅ Step 6: Configure Raid Settings - ✅ COMPLETE!

**Priority**: 🟢 LOW  
**Time Estimate**: 5 minutes  
**Dependencies**: None (server already running)
**Completed**: December 25, 2025

### ✅ Decision: Raids Disabled

**What We Did:**
Edited the startup script to disable raids completely so players can learn the game in peace.

**Commands Used:**
```bash
# 1. Edited startup script
nano ~/valheim_server/start_server.custom.sh

# 2. Added to the valheim_server.x86_64 command:
    -modifier    raids 0

# 3. Restarted server
valheim_server restart

# 4. Verified it's running
ps aux | grep "modifier raids"
```

**Verification Output:**
```
✅ Server running with: -modifier raids 0
✅ No errors in logs
✅ Server active and accepting connections
```

**Status:** Raids are completely disabled. Players can build and progress without base attacks. Will re-enable when the group votes and is ready.

**To Re-Enable Later:**
```bash
nano ~/valheim_server/start_server.custom.sh
# Change: -modifier raids 0
# To:     -modifier raids 1 (100% normal)
#    Or:  -modifier raids 0.5 (50% reduced)
valheim_server restart
```

### Success Criteria
- ✅ Raid preference decided
- ✅ Configuration applied (if not using default)
- ✅ Server restarted
- ✅ Admin can use `stopevent` command

---

## 📊 PROGRESS TRACKER

### Overall Completion: 0/6 Steps Complete

- [ ] **Step 1**: Automated Backups (CRITICAL)
- [ ] **Step 2**: Admin Access Setup (CRITICAL)
- [ ] **Step 3**: Test Admin Commands (HIGH)
- [ ] **Step 4**: Discord Server Setup (HIGH)
- [ ] **Step 5**: Discord Bot Installation (MEDIUM)
- [ ] **Step 6**: Raid Configuration (LOW)

---

## 🎯 NEXT SESSION CHECKLIST

**Before Starting:**
1. Read this file completely
2. Confirm server is still running: `ssh ubuntu@150.136.183.220`
3. Have Discord open and ready
4. Have Steam ID ready (if needed)

**During Session:**
1. Complete steps in order (1 → 6)
2. Mark checkboxes as you complete tasks
3. Update progress tracker
4. Document any issues or changes

**After Completion:**
1. Update CLAUDE.md with completion status
2. Update SERVER_INFO.md with new info (webhook URL, backup schedule)
3. Test all features end-to-end
4. Share Discord server invite with players

---

## 📝 NOTES SECTION

### Issues Encountered
(Add any problems you encounter here with solutions)

### Custom Changes
(Document any deviations from the plan)

### Additional Features Added
(Note any extra features or improvements)

---

**Last Updated**: December 24, 2025  
**Ready to Begin**: YES ✅  
**Estimated Total Time**: ~45-60 minutes for all steps
