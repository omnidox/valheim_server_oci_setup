# Valheim Server on Oracle Cloud Infrastructure - Project Guide

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

### Important Warnings from Project Owner
1. **Self-upgrade bug** (Jan 14 - Sept 9, 2025): Manual upgrade required if script used during this period
2. **Ubuntu 24.04 NOT supported**: armhf compatibility issues due to 2038 timestamp changes
3. **Crossplay is experimental**: May cause server crashes
4. **Modding**: Now potentially possible with FEX emulator (BepInEx previously didn't support ARM)

## Installation Process Overview

### Phase 1: OCI Setup
1. Create OCI free account at https://cloud.oracle.com/
2. Create VM instance with Ubuntu 22.04 aarch64
3. Configure Virtual Cloud Network (VCN)
4. Set up firewall/security rules for ports 2456-2459 (UDP)
5. Generate and configure SSH keys

### Phase 2: Server Installation
1. Connect to VM via SSH
2. Download setup script: `wget https://raw.githubusercontent.com/husjon/valheim_server_oci_setup/refs/heads/main/setup_valheim_server.sh`
3. Run script (first run updates OS and reboots): `bash ./setup_valheim_server.sh`
4. Run script again after reboot to complete installation

### Phase 3: Configuration
1. Edit `~/server_credentials` file with server settings
2. Customize `~/valheim_server/start_server.custom.sh` if needed
3. Start server: `valheim_server start`

## Key Files on Server

### Configuration Files
- `~/server_credentials` - Server name, world name, password, public flag, port
- `~/valheim_server/start_server.custom.sh` - Custom startup parameters and flags
- `~/.box64rc` - Box64 configuration (if using Box instead of FEX)

### Data Locations
- `/home/ubuntu/valheim_server/` - Server installation directory
- `/home/ubuntu/valheim_data/worlds_local/` - World save files (.db and .fwl)
- `/home/ubuntu/steamcmd/` - SteamCMD installation

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
valheim_server logs-live   # Live tail logs
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

### Enabling Crossplay (Experimental)
1. Add `-crossplay` flag to `~/valheim_server/start_server.custom.sh`
2. Restart server
3. Join code will be in logs: `valheim_server logs-live`

### Switching Valheim Versions
**Previous Stable**: Use `default_old` branch in SteamCMD  
**Public Beta**: Use `public-test` branch with password `yesimadebackups`  
**Revert to Public**: Use `public` branch

## Troubleshooting

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
- 2456 - Game traffic
- 2457 - Game traffic
- 2458 - Game traffic
- 2459 - Game traffic

### Firewall Configuration
Script automatically configures iptables rules. Rules saved in `/etc/iptables/rules.v4`

## SSH Connection Details

### Windows (PuTTY)
- Host: OCI VM Public IP
- Port: 22
- Auth: Private key generated by PuTTYgen

### Mac/Linux
```bash
ssh ubuntu@<PUBLIC_IP>
```
Uses `~/.ssh/id_rsa` private key

## Current Project Status

### What We Know
- Project exists and is well-documented
- User wants to use it to set up their own server
- User has NOT yet created OCI account or VM
- User has NOT yet run the installation

### What We Need to Do
1. Help user create OCI account
2. Guide through VM creation
3. Assist with SSH key setup
4. Support installation process
5. Help with configuration
6. Troubleshoot any issues

## Next Steps for User

1. **Create Oracle Cloud Account**: https://cloud.oracle.com/
2. **Generate SSH Keys** (Windows: PuTTY/PuTTYgen, Mac/Linux: ssh-keygen)
3. **Create VM Instance** following Readme.md instructions
4. **Configure Network/Firewall** for ports 2456-2459
5. **Connect via SSH** to the VM
6. **Run Installation Script**
7. **Configure Server Credentials**
8. **Start Server**

## Notes for AI Assistant (Claude)

- User is new to this setup - provide detailed, step-by-step guidance
- Verify each step before moving to next phase
- Watch for common pitfalls mentioned in disclaimers
- Reference the Readme.md as authoritative source
- Remind about Ubuntu 22.04 requirement (not 24.04)
- Explain technical concepts when needed (ARM, emulation, etc.)
- Keep track of where user is in the setup process
- Be ready to troubleshoot OCI-specific issues
- User's local path: `C:\Users\omnid\GitHub\valheim_server_oci_setup`

## Useful Links

- **Oracle Cloud**: https://cloud.oracle.com/
- **PuTTY Download**: https://www.putty.org/
- **Original Reddit Post**: https://www.reddit.com/r/valheim/comments/s1os21/create_your_own_free_dedicated_server
- **Discord Support**: https://discord.gg/ExnzM4E7pE
- **Valheim Server Commands**: https://www.valheimgame.com/support/a-guide-to-dedicated-servers/
