# Valheim Server on Oracle Cloud Infrastructure - Project Guide

## 🚀 CURRENT SERVER STATUS: **LIVE AND OPERATIONAL** ✅

**Server is currently running and accepting connections!**

### Live Server Details
- **Public IP**: 150.136.183.220
- **Connection String**: `150.136.183.220:2456`
- **World Name**: upsidedown
- **Server Name**: Set in ~/server_credentials
- **Status**: Active and tested - user successfully connected and played

### SSH Access Configured
- **Public Key**: C:\Users\omnid\Desktop\valheim_public.pub
- **Private Key (PuTTY)**: C:\Users\omnid\Desktop\valheim_private.ppk
- **Private Key (OpenSSH)**: C:\Users\omnid\.ssh\valheim_key
- **Connection Methods**: Both PuTTY GUI and Windows CMD working

---

## 📋 UPCOMING TASKS - TO BE COMPLETED

### Priority 1: Server Administration ⚡
- [ ] **Set up automated backups** (cron job for daily world saves)
- [ ] **Configure admin access** (edit ~/valheim_data/adminlist.txt)
  - [ ] Add your Steam ID
  - [ ] Add trusted Xbox/Game Pass player IDs (Xbox_XXXXXXXXXXXXXXXX format)
- [ ] **Test admin commands** in-game (F5 console)
  - [ ] Test `stopevent` (stop raids)
  - [ ] Test `save` (force save)
  - [ ] Test `kick` command

### Priority 2: Discord Integration 🤖
- [ ] **Create Discord server** for Valheim group (or designate existing server)
- [ ] **Install Discord bot** (Python log parser - Option 2)
  - Repository: https://github.com/jaumebecks/valheim-server-notifier
  - Features: server start/stop, player join/leave, deaths, custom events
  - ARM-compatible (no BepInEx required)
- [ ] **Configure Discord webhook**
- [ ] **Set up automated notifications**
  - Server status changes
  - Player join/leave events
  - Death notifications
  - Backup completion alerts

### Priority 3: Raid Configuration 🛡️
- [ ] **Decide on raid settings** (keep, disable, or modify frequency)
- [ ] **Configure raid modifiers** if needed
  - Edit ~/valheim_server/start_server.custom.sh
  - Add `-modifier raids 0` to disable completely
  - Or keep default for normal gameplay
- [ ] **Test raid commands** (`stopevent`, `event [name]`)

### Priority 4: Crossplay Testing 🎮
- [ ] **Test Xbox/Game Pass player connections**
- [ ] **Document join codes** (changes on every restart)
- [ ] **Verify crossplay stability** (marked experimental on ARM)

### Priority 5: Documentation Updates 📝
- [ ] **Update SERVER_INFO.md** with Discord bot info
- [ ] **Create Xbox player guide** for enabling console
- [ ] **Document backup procedures** and schedule
- [ ] **Create admin quick reference** for commands

---

## 🎮 XBOX/GAME PASS ADMIN SUPPORT

### Important: Xbox Players CAN Be Admins! ✅

Xbox and Game Pass players can have full admin access using their Xbox IDs.

### Xbox ID Format
```
Xbox_2535416401464117  # Example Xbox ID (NOT SteamID)
```

### Finding Xbox Player IDs
1. Have Xbox player join server
2. SSH into server: `valheim_server logs | grep "Xbox_"`
3. Look for: `PlayFab socket... received local Platform ID Xbox_XXXXXXXXXXXXXXXX`
4. Copy the `Xbox_XXXXXXXXXXXXXXXX` part

### Mixed Admin List Example
```bash
# File: ~/valheim_data/adminlist.txt
76561198012345678      # You (Steam)
76561198087654321      # Friend 1 (Steam)
Xbox_2535416401464117  # Friend 2 (Xbox/Game Pass)
Xbox_2433224801742044  # Friend 3 (Xbox/Game Pass)
```

### Xbox Console Access (Two Methods)

**Method 1: Controller (Xbox Console)**
1. Hold: LB + RB + LT + RT (all four triggers)
2. Press: Menu button (three lines)
3. Press: A to bring up keyboard
4. Type commands

**Method 2: PC Shortcut (Xbox/Game Pass on Windows)**
1. Navigate to: `C:\XboxGames\Valheim\Content\`
2. Right-click `valheim.exe` → Create shortcut
3. Right-click shortcut → Properties → Shortcut tab
4. In Target field, add ` -console` at the end
5. Launch game from shortcut
6. Press F5 in-game

### Admin Commands That Work on Dedicated Servers
```
✅ kick [player]    - Kick player
✅ ban [player]     - Ban player  
✅ unban [player]   - Unban player
✅ banned           - List banned
✅ stopevent        - Stop current event/raid
✅ save             - Force save
```

### Commands That DON'T Work on Dedicated Servers
```
❌ god              - Only works in single-player
❌ ghost            - Only works in single-player
❌ spawn [item]     - Only works in single-player
❌ debugmode        - Only works in single-player
```

---

## 🤖 DISCORD BOT INTEGRATION PLAN

### Chosen Solution: Python Log Parser Bot (RECOMMENDED)
**Repository**: https://github.com/jaumebecks/valheim-server-notifier

### Why This Option?
- ✅ ARM-compatible (no BepInEx required)
- ✅ Lightweight and reliable
- ✅ Reads server logs, posts to Discord webhook
- ✅ No game modifications needed
- ✅ Easy to set up and maintain

### Bot Features
- Server start/stop notifications
- Player join/leave events
- Death notifications
- Custom event matching
- Scheduled status updates

### Installation Steps (To Be Completed)
1. SSH into server
2. Install Python dependencies
3. Clone bot repository
4. Create Discord webhook
5. Configure bot settings
6. Set up systemd service
7. Test notifications

### Alternative: Simple Webhook Scripts
If full bot is too complex, we can use basic curl commands for:
- Manual server start/stop announcements
- Scheduled status updates
- No continuous monitoring (limited functionality)

---

## 💾 BACKUP STRATEGY (TO BE IMPLEMENTED)

### Automated Daily Backups
**Goal**: Protect world data from corruption, griefing, or mistakes

### Files to Back Up
```
~/valheim_data/worlds_local/upsidedown.db   # World data
~/valheim_data/worlds_local/upsidedown.fwl  # World metadata
```

### Proposed Backup Schedule
- **Daily**: 3:00 AM automatic backup (cron job)
- **Pre-update**: Manual backup before server updates
- **On-demand**: Discord bot command (!backup)

### Backup Script (To Be Created)
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/home/ubuntu/valheim_backups"
mkdir -p $BACKUP_DIR
cp ~/valheim_data/worlds_local/upsidedown.* $BACKUP_DIR/backup_$DATE/
echo "Backup created: $BACKUP_DIR/backup_$DATE/"
```

### Retention Policy
- Keep last 7 daily backups
- Keep weekly backups for 1 month
- Manual cleanup of old backups

---

## 🔧 RAID CONFIGURATION OPTIONS

### Current Status
Raids are ENABLED by default (normal Valheim behavior)

### Configuration Options

**Option 1: Keep Raids (Default)**
- No changes needed
- Normal Valheim experience
- Admins can `stopevent` if raid becomes overwhelming

**Option 2: Disable Raids Completely**
```bash
# Edit: ~/valheim_server/start_server.custom.sh
# Add flag: -modifier raids 0
```

**Option 3: Reduce Raid Frequency**
```bash
# Add flag: -modifier raids 0.5  # 50% less frequent
```

**Option 4: Increase Raid Frequency**
```bash
# Add flag: -modifier raids 2    # 2x more frequent
```

### Admin Control During Raids
Any admin can stop active raids:
1. Press F5 (console)
2. Type: `stopevent`
3. Current raid ends immediately

---

## Project Overview
**Goal**: Set up a free Valheim dedicated server using Oracle Cloud Infrastructure (OCI)  
**Repository**: https://github.com/husjon/valheim_server_oci_setup  
**Original Author**: husjon (not created by current user)  
**Purpose**: Leverage existing setup scripts and documentation to deploy a working Valheim server

## Important Context
- This project was NOT created by the user - we're leveraging an existing community solution
- The project owner (husjon) has terminated their Oracle tenancy but maintains the guide
- Setup is designed for Oracle's Always Free tier (4 OCPUs, 24GB RAM)
- Uses ARM architecture (aarch64) with x86_64 emulation for Valheim server

## Project Structure

```
valheim_server_oci_setup/
├── Readme.md                    # Comprehensive setup guide (main reference)
├── setup_valheim_server.sh      # Automated installation script
├── SERVER_INFO.md               # Server connection and management info
├── CLAUDE.md                    # This file - project context for AI
└── .editorconfig                # Editor configuration
```

## Key Technical Details

### Platform Requirements
- **OS**: Ubuntu 22.04 LTS (Canonical Ubuntu 22.04 Minimal aarch64)
- **Architecture**: ARM64 (aarch64) with x86_64 emulation
- **Emulation Layer**: FEX (default as of Sept 2025, previously Box86/Box64)
- **Cloud Provider**: Oracle Cloud Infrastructure (OCI)

### Server Specifications (OCI Free Tier)
- **Shape**: VM.Standard.A1.Flex
- **OCPUs**: 4
- **Memory**: 24GB
- **Network Ports**: 2456-2459 (UDP)
- **Max Players**: 10 (official limit), potentially more with mods

### Important Warnings from Project Owner
1. **Self-upgrade bug** (Jan 14 - Sept 9, 2025): Manual upgrade required if script used during this period
2. **Ubuntu 24.04 NOT supported**: armhf compatibility issues due to 2038 timestamp changes
3. **Crossplay is experimental**: May cause server crashes
4. **Modding**: Now potentially possible with FEX emulator (BepInEx previously didn't support ARM)

## Installation Process Overview (COMPLETED ✅)

### Phase 1: OCI Setup ✅
1. ✅ Created OCI free account at https://cloud.oracle.com/
2. ✅ Created VM instance with Ubuntu 22.04 aarch64
3. ✅ Configured Virtual Cloud Network (VCN): valheim-vcn
4. ✅ Set up firewall/security rules for ports 2456-2459 (UDP)
5. ✅ Generated and configured SSH keys (PuTTY + OpenSSH)

### Phase 2: Server Installation ✅
1. ✅ Connected to VM via SSH (both PuTTY and CMD working)
2. ✅ Downloaded setup script
3. ✅ Ran script (handled OS updates, FEX installation, SteamCMD)
4. ✅ Completed installation after reboot
5. ✅ World generation successful (282 seconds)

### Phase 3: Configuration ✅
1. ✅ Server credentials file generated with random password
2. ✅ World "upsidedown" created
3. ✅ Server started and systemd service enabled
4. ✅ User successfully connected and tested gameplay
5. ✅ Crossplay enabled (experimental)

## Key Files on Server

### Configuration Files
- `~/server_credentials` - Server name, world name, password, public flag, port
- `~/valheim_server/start_server.custom.sh` - Custom startup parameters and flags
- `~/valheim_data/adminlist.txt` - Admin Steam IDs and Xbox IDs
- `~/.box64rc` - Box64 configuration (if using Box instead of FEX)

### Data Locations
- `/home/ubuntu/valheim_server/` - Server installation directory
- `/home/ubuntu/valheim_data/worlds_local/` - World save files (.db and .fwl)
- `/home/ubuntu/steamcmd/` - SteamCMD installation
- `/home/ubuntu/valheim_backups/` - Backup directory (to be created)

### Log Files
- `~/install_valheim_server.log` - Installation log
- View with: `journalctl --user -u valheim_server` or `valheim_server logs`

## Valheim Server Helper Commands

```bash
valheim_server help        # Show help
valheim_server start       # Start the server
valheim_server stop        # Stop the server
valheim_server restart     # Restart the server
valheim_server update      # Update server (stops, updates, requires manual start)
valheim_server logs        # View logs
valheim_server logs-live   # Live tail logs (Ctrl+C to exit)
```

## Common Tasks

### Updating the Server
When Valheim client updates, server must update:
```bash
valheim_server update
valheim_server start
```

### Adding Pre-existing Worlds
1. Locate world files on local PC:
   - Windows: `%userprofile%/AppData/LocalLow/IronGate/Valheim/Worlds`
   - Linux: `$HOME/.config/unity3d/IronGate/Valheim/worlds`
2. Stop server: `valheim_server stop`
3. Upload `.db` and `.fwl` files to `/home/ubuntu/valheim_data/worlds_local/`
4. Edit `~/server_credentials` to set `WORLD_NAME` to match your files
5. Start server: `valheim_server start`

### Manual Backup (Right Now)
```bash
cd /home/ubuntu/valheim_data/worlds_local/
cp upsidedown.db upsidedown.db.backup_$(date +%Y%m%d_%H%M%S)
cp upsidedown.fwl upsidedown.fwl.backup_$(date +%Y%m%d_%H%M%S)
```

### Restore from Backup
```bash
valheim_server stop
cd /home/ubuntu/valheim_data/worlds_local/
cp upsidedown.db.backup_YYYYMMDD_HHMMSS upsidedown.db
cp upsidedown.fwl.backup_YYYYMMDD_HHMMSS upsidedown.fwl
valheim_server start
```

### Crossplay Join Methods

**Steam Players (One-Click Link)**
```
steam://rungameid/892970//+connect%20150.136.183.220:2456/
```
Post this link in Discord - clicking launches Valheim and connects directly

**Xbox/Game Pass Players**
- **Option 1**: Use 6-digit join code (changes every restart)
  - Find in logs: `valheim_server logs | grep "join code"`
- **Option 2**: Direct IP entry: `150.136.183.220:2456`

⚠️ **Join code changes on every server restart** - manual Discord updates needed OR use Discord bot automation

### Switching Valheim Versions
**Previous Stable**: Use `default_old` branch in SteamCMD  
**Public Beta**: Use `public-test` branch with password `yesimadebackups`  
**Revert to Public**: Use `public` branch

## Troubleshooting

### Common Issues

**Issue**: `valheim_server logs-live` won't exit with Ctrl+C
**Solution**: Close SSH session entirely, reconnect. Server keeps running (systemd).

**Issue**: Installation stuck on "Calculating upgrade"
**Solution**: Ctrl+C, run `sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y`

**Issue**: nano not installed
**Solution**: `sudo apt install nano -y`

### Getting Help
- **Discord**: https://discord.gg/ExnzM4E7pE
- **GitHub Issues**: Original repository issues

### Collecting Logs for Support
```bash
journalctl --no-pager --since=-1d --user -u valheim_server > ~/valheim_server.systemd.log
```
Download both `install_valheim_server.log` and `valheim_server.systemd.log` for analysis.

### Oracle Idle Instance Reclamation
- Oracle may flag instances idle for 7 days
- Will receive email warning 7 days before shutdown
- **Solution**: Simply restart instance from OCI console to reset timer

## Important Environment Variables

```bash
CROSSPLAY_SUPPORT=false    # Enable/disable crossplay
USE_BOX=true              # Use Box86/64 instead of FEX
BOX64_VERSION=v0.2.6      # Pin Box64 version
ARM_INSTRUCTION_SET=8.2   # ARM version (auto-detected)
NO_SELF_UPDATE=true       # Skip setup script self-update
```

## Networking Details

### Required Ports (UDP)
- 2456 - Game traffic (main connection port)
- 2457 - Game traffic
- 2458 - Game traffic
- 2459 - Game traffic

### Firewall Configuration
Script automatically configures iptables rules. Rules saved in `/etc/iptables/rules.v4`

## SSH Connection Details

### Windows (PuTTY) ✅
- Host: 150.136.183.220
- Port: 22
- Auth: C:\Users\omnid\Desktop\valheim_private.ppk

### Windows (CMD/PowerShell) ✅
```bash
ssh -i C:\Users\omnid\.ssh\valheim_key ubuntu@150.136.183.220
```

### Mac/Linux
```bash
ssh -i ~/.ssh/valheim_key ubuntu@150.136.183.220
```

## Implementation Timeline

### ✅ COMPLETED
- [x] OCI account creation
- [x] VM instance setup
- [x] SSH key configuration
- [x] Server installation (FEX, SteamCMD, Valheim)
- [x] World generation
- [x] Crossplay enablement
- [x] Initial testing and connection
- [x] Documentation (SERVER_INFO.md created)

### 🔄 IN PROGRESS
- [ ] Server administration setup
- [ ] Discord integration
- [ ] Backup automation
- [ ] Raid configuration
- [ ] Xbox admin testing

### 📅 PLANNED
- [ ] Performance monitoring
- [ ] Advanced Discord bot features
- [ ] Mod exploration (experimental)
- [ ] Multi-world management

## Notes for AI Assistant (Claude)

### Context Awareness
- ✅ Server is LIVE and working - no need to reinstall
- ✅ User has successfully connected and played
- ✅ Current focus: Server management, Discord integration, backups
- ⏳ Next session: Implement checklist items from top of this document

### User Skill Level
- Moderate technical knowledge
- Comfortable with SSH and basic commands
- New to server administration
- Needs step-by-step guidance for complex tasks

### Critical Reminders
- User's local path: `C:\Users\omnid\GitHub\valheim_server_oci_setup`
- Server IP: 150.136.183.220
- World name: upsidedown
- SSH keys configured for both PuTTY and OpenSSH
- Crossplay is enabled (experimental - watch for stability issues)

### Documentation Standards
- Keep SERVER_INFO.md for quick reference and connection info
- Keep CLAUDE.md for comprehensive project context and AI handoff
- Update both files when completing tasks
- Mark completed tasks with ✅ and date

### Safety Practices
- Always backup before major changes
- Test admin commands in controlled environment first
- Document all configuration changes
- Keep adminlist.txt limited to trusted players only

## Useful Links

- **Oracle Cloud**: https://cloud.oracle.com/
- **PuTTY Download**: https://www.putty.org/
- **Original Reddit Post**: https://www.reddit.com/r/valheim/comments/s1os21/create_your_own_free_dedicated_server
- **Discord Support**: https://discord.gg/ExnzM4E7pE
- **Valheim Wiki (Console Commands)**: https://valheim.fandom.com/wiki/Console_Commands
- **Discord Bot Repo**: https://github.com/jaumebecks/valheim-server-notifier

---

**Last Updated**: December 24, 2025  
**Status**: Server operational, awaiting implementation of management features  
**Next Steps**: Execute checklist items at top of document
