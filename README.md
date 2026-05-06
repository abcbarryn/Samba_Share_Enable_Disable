# Samba Share Toggle

A lightweight Bash utility to dynamically enable or disable Samba shares by modifying 
the `available` parameter in your `smb.conf` file. This script automates the 
configuration editing, handles backups, verifies syntax, and manages active file 
locks to ensure a smooth transition without manual intervention.

## 🚀 Features

- **Automated Configuration**: Automatically finds and modifies the `available = 
yes/no` parameter within a specific Samba share block.
- **Safety First**: 
    - Creates an automatic backup of your `/etc/samba/smb.conf` (as `smb.conf.bak`) 
before making changes.
    - Performs a `testparm` validation before finalizing; if the new configuration is 
invalid, it automatically reverts to the backup.
- **Process Management**: Detects if files within the share path are actively being 
used by other processes. It offers to use `fuser` to terminate those processes to 
ensure the share can be disabled cleanly.
- **Idempotency**: Checks the current state of the share first. If the share is 
already in the requested state, the script exits without performing redundant 
operations or reloads.
- **Smart Reloading**: Only triggers a `smbcontrol` reload if changes were 
successfully applied and verified.

## 📋 Prerequisites

Before running the script, ensure your system has the following installed:

- **Samba**: The primary service being managed.
- **lsof**: Used to detect if files in the share path are currently open.
 
  *On Debian/Ubuntu:* `sudo apt install lsof samba-common`
  *On RHEL/CentOS:* `sudo yum install lsof samba-common`
- **psmisc**: Required for the `fuser` command to manage active processes.
  *On Debian/Ubuntu:* `sudo apt install psmisc`

## 🛠 Installation

1. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/yourusername/samba-share-toggle.git
   cd samba-share-toggle
   ```
2. Make the script executable:
   ```bash
   chmod +x share
   ```
3. (Optional) Move it to your local bin directory to run it from anywhere:
   ```bash
   sudo cp -av share /usr/local/bin/share
   ```

## 📖 Usage

The script requires `root` or `sudo` privileges to modify `/etc/samba/smb.conf`.

### Syntax
```bash
sudo ./share {enable|disable} [share_name]
```

### Parameters
| Parameter | Description |
| :--- | :--- |
| `enable` | Sets `available = yes` for the specified share. |
| `disable` | Sets `available = no` for the specified share. |
| `share_name` | The name of the share block as defined in your `smbe.conf`. |

### Examples

**To enable a share named "work_docs":**
```bash
sudo ./share enable work_docs
```

**To disable a share named "public_share":**
```bash
sudo ./share disable public_share
```

## ⚠️ Important Notes

- **Permissions**: This script **must** be run with `sudo`.
- **Process Termination**: If you choose to "Force disconnect" when prompted, the 
script will kill any processes currently accessing the files in that share path. Use 
this with caution in production environments.
- **Configuration Structure**: The script expects the share block in `smb.conf` to 
follow standard format and specifically looks for the `available` parameter.
