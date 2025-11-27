# kanage

A bash script to manage kanata keyboard configurations on macOS using launchctl.

## Installation

1. Clone or download this repository
2. Make the script executable (if not already):
   ```bash
   chmod +x kanage
   ```
3. Install to your PATH (optional but recommended):
   ```bash
   # Option 1: Symlink to /usr/local/bin
   sudo ln -s $(pwd)/kanage /usr/local/bin/kanage
   
   # Option 2: Copy to /usr/local/bin
   sudo cp kanage /usr/local/bin/kanage
   
   # Option 3: Add to your PATH by adding to ~/.zshrc or ~/.bashrc
   export PATH="$PATH:/path/to/kanage"

   # Option 4: Create an alias in ~/.zshrc or ~/.bashrc
   alias kanage="/path/to/kanage"\
   ```

## Prerequisites

- macOS with launchctl
- Homebrew (for installing kanata)
- Sudo access (for managing launchctl services)

## First-Time Setup

### Install Kanata and Karabiner Services

The `kanage install` command will set up everything you need:

```bash
kanage install
```

This command will:
1. Install Karabiner DriverKit (required for kanata)
2. Install Kanata via Homebrew
3. Create and configure launchctl service plist files
4. Bootstrap and enable all services
5. Guide you through granting necessary macOS permissions

**Note**: You'll need to grant permissions for:
- System Extensions (for Karabiner DriverKit)
- Accessibility (for Kanata)
- Input Monitoring (for Kanata)

The install command will open the relevant System Settings windows to guide you through this process.

## Usage

### Install Services

Install kanata and karabiner services (first-time setup):

```bash
kanage install
```

### Uninstall Services

Uninstall kanata and karabiner services:

```bash
kanage uninstall
```

This will:
1. Unload and disable all services
2. Remove plist files
3. Uninstall kanata via Homebrew
4. Remove Karabiner DriverKit

### Switch Config

Switch to a different kanata config and restart the service:

```bash
kanage use colemak
```

This will:
1. Look for `~/.config/kanata/colemak.kbd`
2. Create a symlink from `~/.config/kanata/kanata.kbd` to `colemak.kbd`
3. Restart the kanata launchctl service

### Edit Config

Edit a kanata config file:

```bash
kanage edit colemak
```

This will:
1. Open the config file in your default editor (nvim by default)
2. If editing the currently active config, prompt to reload the service after saving

To use a different editor, set the `KANAGE_EDITOR` environment variable:
```bash
export KANAGE_EDITOR=vim
kanage edit colemak
```

### View Logs

View kanata service logs:

```bash
kanage log
```

This shows logs from `/var/log/kanata.log` and follows new entries in real-time.

### List Configs

List all available kanata configs:

```bash
kanage list
```

The currently active config will be marked with an asterisk (*).

### Show Status

Show current kanata service status and active config:

```bash
kanage status
```

This displays:
- **Current Config**: The currently active kanata config
- **Kanata Service**: Service status, plist file path, and plist validation
- **Karabiner Manager Service**: Service status, plist file path, and plist validation
- **Karabiner Daemon Service**: Service status, plist file path, and plist validation

The status command validates all plist files using `plutil` to ensure they are properly formatted. Invalid plist files are highlighted in red and may prevent services from running correctly.

### Start Service

Start the kanata service:

```bash
kanage start
```

This will load and start the service if it's not already running.

### Stop Service

Stop the kanata service:

```bash
kanage stop
```

This will stop and unload the service.

### Restart Service

Restart the kanata service without changing the config:

```bash
kanage restart
```

### Help

Show usage information:

```bash
kanage help
```

## Configuration

The script uses the following defaults:

- **Config directory**: `~/.config/kanata/`
- **Active config file**: `~/.config/kanata/kanata.kbd` (symlinked to the active config)
- **Service name**: `com.kanage.kanata`
- **Plist location**: `/Library/LaunchDaemons/com.kanage.kanata.plist`
- **Log file**: `/var/log/kanata.log`
- **Editor**: `nvim` (configurable via `KANAGE_EDITOR` environment variable)

### Environment Variables

- `KANAGE_EDITOR` - Editor to use for the `edit` command (default: `nvim`)

## How It Works

1. **Config Switching**: When you run `kanage use <name>`, the script creates a symlink from `kanata.kbd` to `<name>.kbd`. The kanata service is configured to read from `kanata.kbd`, allowing you to easily switch between different configs.

2. **Service Management**: The script uses `launchctl` commands with sudo to manage the kanata service. Services are managed as system-level launch daemons.

3. **Service Dependencies**: The kanata service depends on Karabiner Manager, which depends on Karabiner Daemon. The script handles these dependencies correctly during install/uninstall operations.

## Troubleshooting

### Service Not Found

If you get an error about the service not being found:
- Make sure you've run `kanage install` first
- Check service status: `kanage status`
- Verify the service is loaded: `sudo launchctl list | grep kanage`

### Permission Errors

If you encounter permission errors:
- Make sure the script is executable: `chmod +x kanage`
- Ensure you have write access to `~/.config/kanata/`
- Some operations require sudo (the script will prompt when needed)

### Logs Not Showing

If logs don't appear:
- Make sure kanata service is running: `kanage status`
- Check if the log file exists: `ls -l /var/log/kanata.log`
- Verify log file permissions: `sudo ls -l /var/log/kanata.log`

### Service Won't Start

If the service won't start:
- Check that Karabiner DriverKit is installed and approved in System Settings
- Verify kanata has Accessibility and Input Monitoring permissions
- Check service status: `kanage status` - look for invalid plist files
- If plist files are invalid, reinstall: `kanage uninstall` then `kanage install`
- Check logs: `kanage log`
- Try restarting: `kanage restart`

### Invalid Plist Files

If `kanage status` shows invalid plist files:
- The plist files may have been corrupted or manually edited incorrectly
- Reinstall the services: `kanage uninstall` then `kanage install`
- This will recreate all plist files with the correct format

## Examples

```bash
# First-time setup: install kanata and karabiner
kanage install

# Switch to colemak layout
kanage use colemak

# Edit a config
kanage edit colemak

# Switch to qwerty layout
kanage use qwerty

# Check what configs are available
kanage list

# See current status
kanage status

# View logs to debug issues
kanage log

# Start/stop/restart the service
kanage start
kanage stop
kanage restart

# Uninstall everything
kanage uninstall
```
