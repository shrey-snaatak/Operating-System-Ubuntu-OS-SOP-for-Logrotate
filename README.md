# SOP for Logrotate

![image](https://github.com/user-attachments/assets/858d11cb-a7eb-4b80-a009-b649d106f691)

| **Author** | **Created on** | **Version** | Pre Reviewer |           L0 |            L1 |             L2 |
| --- | --- | --- | --- | --- | --- | --- |
| Shrey | 14-08-25 | version 1 | Komal Jaiswal |         Imran |         Shahsi |  Mahesh Kumar |

## Introduction
Effective log management in Linux is crucial for maintaining system health, performance, and security. Logs are invaluable records of system events, application behavior, and security incidents. However, these logs can quickly grow out of control without proper management, consuming valuable disk space and leading to potential system issues. This is where log rotation comes into play. Regularly rotating logs ensures they are archived systematically, preventing disk space exhaustion and enhancing overall system performance.

**Logrotate** is a system utility designed to manage the automatic rotation, compression, removal, and mailing of log files. It helps in administering systems that generate large volumes of log data by ensuring logs do not consume excessive disk space and remain manageable. 

## Purpose

Logrotate addresses the challenges associated with growing log files by:

- **Rotating** logs: Renaming current logs and starting fresh ones.
- **Compressing** old logs: Saving disk space by compressing archived logs.
- **Removing** outdated logs: Deleting logs that are no longer needed.
- **Mailing** logs: Sending logs to system administrators if required.

This utility ensures that log files are maintained at a reasonable size, facilitating easier log management and analysis. 

## Key Features

- **Automated Rotation**: Schedule log rotations daily, weekly, monthly, or based on file size.
- **Compression Support**: Compress old log files using tools like gzip.
- **Customizable Configuration**: Define rotation policies per application or globally.
- **Script Execution**: Run custom scripts before or after log rotation.
- **State Tracking**: Maintains a state file to track the last rotation time for each log.

## Getting Started

### Prerequisites

- **Operating System**: Linux (Ubuntu 22.04 or compatible distributions)
- **User Permissions**: Root or sudo privileges
- **Dependencies**:
    - `gzip` for compression
    - `cron` for scheduling rotations

### Installation

Logrotate is typically pre-installed on most Linux distributions. To verify its presence:

```bash
logrotate --version

```

If not installed, you can install it using:

```bash
sudo apt update
sudo apt install logrotate

```

Let’s create a sample application log and rotate it using logrotate.

### 1. Create a Custom Log Directory & Log File

```bash

mkdir -p ~/demo-logs
echo "This is a test log entry." > ~/demo-logs/myapp.log
```

### 2. Create a Custom Logrotate Configuration

Create a file under `/etc/logrotate.d/` to manage this log:

```bash

sudo nano /etc/logrotate.d/myapp
```

Paste this into the file:

```
nginx
CopyEdit
/home/YOUR_USERNAME/demo-logs/myapp.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    create 0644 $USER $USER
}

```

> 🔁 Replace YOUR_USERNAME with your actual Linux username (you can run whoami to get it).
> 

Save and exit.

---

### 3. Force a Log Rotation (Test)

To manually trigger rotation and check if everything works:

```bash

sudo logrotate -f /etc/logrotate.d/myapp

```

Check the contents of the log directory:

```bash

ls -lh ~/demo-logs

```

You should see a compressed file like:

```bash

myapp.log.1.gz
myapp.log

```
### System Requirements

| Requirement | Minimum Specification |
| --- | --- |
| Processor | Dual-Core |
| RAM | 4 GB |
| Disk Space | 25 GB |
| Operating System | Ubuntu 22.04 |

## Configuration

### Global Configuration

The main configuration file is located at `/etc/logrotate.conf`. It sets default behaviors and can include other configuration files:

```bash
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
# dateext

# uncomment this if you want your log files compressed
# compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

```

### Application-Specific Configuration

Individual applications can have their own rotation policies defined in `/etc/logrotate.d/`. For example, Nginx's log rotation might be configured as:

```bash
/var/log/nginx/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    prerotate
        if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
            run-parts /etc/logrotate.d/httpd-prerotate; \
        fi
    endscript
    postrotate
        invoke-rc.d nginx rotate >/dev/null 2>&1
    endscript
}

```

**Directive Explanations**:

- `daily`: Rotate logs daily.
- `missingok`: Proceed without error if the log file is missing.
- `rotate 14`: Keep 14 rotated logs before deleting.
- `compress`: Compress rotated logs.
- `delaycompress`: Delay compression until the next rotation cycle.
- `notifempty`: Do not rotate empty logs.
- `create 0640 www-data adm`: Create new log files with specified permissions and ownership.
- `sharedscripts`: Run postrotate script only once, even if multiple logs are rotated.
- `prerotate`/`postrotate`: Scripts to run before/after rotation.

## Usage

To manually test log rotation:

```bash
sudo logrotate --debug /etc/logrotate.conf

```

To force log rotation:

```bash
sudo logrotate --force /etc/logrotate.conf

```

## Dependencies

- **Runtime Dependencies**:
    - `gzip`: For compressing log files.
    - `cron`: To schedule automatic rotations.

## Licensing

Logrotate is released under the **GNU General Public License v2.0**.

## **Contact Information**

| **Name** | **Email address** |
| --- | --- |
| Shrey Tyagi | [shrey.tyagi.snaatak@mygurukulam.co](mailto:abc@mygurukulam.co) |

## References

- [Logrotate Manual](https://linux.die.net/man/8/logrotate)
- [Logrotate GitHub Repository](https://github.com/logrotate/logrotate)
- [Understanding logrotate utility - Rackspace Technology](https://docs.rackspace.com/docs/understanding-logrotate-utility)
