# Matrix-800 Software Guide

The Matrix-800 comes with two Gigabit Ethernet ports with the following factory defaults:

| Port Label | Device | Mode   | IP Address      |
|------------|--------|--------|-----------------|
| LAN1       | end0   | DHCP   | Auto-assigned   |
| LAN2       | end1   | Static | 192.168.2.127   |


## 1. Connecting via SSH

### Step 1 — Configure your PC

To connect over LAN2, set your PC's network adapter to the following static IP settings:

| Setting     | Value           |
|-------------|-----------------|
| IP Address  | 192.168.2.100   |
| Subnet Mask | 255.255.255.0   |
| Gateway     | 192.168.2.1     |
| DNS         | 8.8.8.8         |

### Step 2 — Connect as Guest

Root login over SSH is disabled by default. Log in using the `guest` account:

```
$ ssh guest@192.168.2.127
guest@192.168.2.127's password: guest
```

A successful login will display the system banner and basic health information.

### Step 3 — Switch to Root

Once logged in as `guest`, switch to the root account when elevated privileges are needed:

```
guest@matrix800:~$ su -
Password: root
root@matrix800:~#
```

> [!NOTE]
> Matrix 800 Default Credentials:
> | Account | Username | Password |
> |---------|----------|----------|
> | guest | `guest` | `guest` |
> | root | `root` | `root` |


## 2. Network Configuration

As mentioned before, the Matrix-800 comes with two Gigabit Ethernet ports with the following factory defaults:

| Port Label | Device | Mode   | IP Address      |
|------------|--------|--------|-----------------|
| LAN1       | end0   | DHCP   | Auto-assigned   |
| LAN2       | end1   | Static | 192.168.2.127   |
 
### Viewing Network Settings
 
```
root@matrix800:~# ip a show end0   # LAN1
root@matrix800:~# ip a show end1   # LAN2
```
 
### Changing Network Settings
 
Network configuration is managed via Netplan. Edit the YAML file, validate, then apply:
 
```
root@matrix800:~# vi /etc/netplan/00-installer-config.yaml   # Modify Configuration
root@matrix800:~# netplan generate                           # Validate configuration
root@matrix800:~# netplan apply                              # Apply changes
```


## 3. System Information

### Linux Kernel

```
root@matrix800:~# uname -a
Linux matrix800 6.18.23-artila #28 SMP PREEMPT Sun Apr 19 22:10:59 CST 2026 aarch64 aarch64 aarch64 GNU/Linux
```

### Operating System

```
root@matrix800:~# lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.4 LTS
Release:        24.04
Codename:       noble
```  
### Storage Layout

The Matrix-800 comes with 16GB on-board eMMC Flash memory, which contains boot loader, Linux kernel, root file system and user disk (/home).  

```
root@matrix800:~# lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
mmcblk0      179:0    0 14.7G  0 disk
├─mmcblk0p1  179:1    0    2G  0 part
└─mmcblk0p2  179:2    0 12.7G  0 part /
mmcblk0boot0 179:32   0    4M  1 disk
mmcblk0boot1 179:64   0    4M  1 disk

root@matrix800:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        13G  1.2G   11G  10% /
tmpfs           995M     0  995M   0% /dev/shm
tmpfs           398M  6.4M  392M   2% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           199M  8.0K  199M   1% /run/user/0
```


## 4. Setting the System Time

The Matrix-800 uses `timedatectl` to manage system time. By default, the system timezone is set to **Asia/Taipei (CST, UTC+8)** and syncs with an NTP server automatically.

### Check Current Time Status

```
root@matrix800:~# timedatectl
               Local time: Mon 2026-05-11 13:02:35 CST
           Universal time: Mon 2026-05-11 05:02:35 UTC
                 RTC time: Mon 2026-05-11 05:02:35
                Time zone: Asia/Taipei (CST, +0800)
System clock synchronized: no
              NTP service: active
          RTC in local TZ: no
```

### Sync Automatically with NTP (Default)

```
timedatectl set-ntp yes
```

### Set Time Manually

If you want to, you can disable automatic sync and set the time manually:
```
root@matrix800:~# timedatectl set-ntp no
root@matrix800:~# timedatectl set-time "2024-07-18 14:00:00"
```


## 5. Digital I/O  

### Hardware Overview

The Matrix-800 provides:
- **2× opto-isolated digital inputs** (DI1, DI2)
- **1× relay digital output** (DO, normally open)

### Pin Mapping

|DI/DO Number|Device Mapping|
|---|---|
|DI1|/dev/gpiochip4 line 5|
|DI2|/dev/gpiochip5 line 5|
|DO|/dev/gpiochip4 line 4|

Inspect the full GPIO configuration at any time:

```
root@matrix800:~# gpioinfo gpiochip4 gpiochip5
```

### Reading Digital Inputs
 
```
root@matrix800:~# gpioget gpiochip4 5   # Read DI1 (returns 0 or 1)
root@matrix800:~# gpioget gpiochip5 5   # Read DI2 (returns 0 or 1)
```
 
### Writing Digital Output
 
```
root@matrix800:~# gpioset gpiochip4 4=1   # Close the DO relay
root@matrix800:~# gpioset gpiochip4 4=0   # Open the DO relay
``` 


## 6. Serial Ports
The Matrix-800 comes with four RS-485 serial ports supporting baud rates up to **3 Mbps**:

| Port | Interface | Device Path   |
|------|-----------|---------------|
| P1   | RS-485    | /dev/ttyUSB0  |
| P2   | RS-485    | /dev/ttyUSB1  |
| P3   | RS-485    | /dev/ttyUSB2  |
| P4   | RS-485    | /dev/ttyUSB3  |

Use standard Linux tools (stty, pyserial, etc.) to configure baud rate, parity, stop bits, etc.


## 7. Storage

### MicroSD Card

The Matrix-800 includes a built-in microSD card slot for optional storage expansion. The system detects inserted cards automatically.

**Check for the SD card device after insertion:**
```
root@matrix800:~# lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
mmcblk0      179:0    0 14.7G  0 disk
├─mmcblk0p1  179:1    0    2G  0 part
└─mmcblk0p2  179:2    0 12.7G  0 part /
mmcblk0boot0 179:32   0    4M  1 disk
mmcblk0boot1 179:64   0    4M  1 disk
mmcblk1      179:24   0  7.3G  0 disk   <<<<<<<<<< THIS
└─mmcblk1p1  179:25   0  7.3G  0 part   <<<<<<<<<< THIS
```  
 
**Mount the SD card:**

```
root@matrix800:~# mount /dev/mmcblk1p1 /media/
```

**Unmount before removal:**

```
root@matrix752:~# umount /media/
```

## 8. Software Package Management
The Matrix-800 runs **Ubuntu 24.04 LTS** and uses APT for package management. 
> [!NOTE]
> Most commands require root privileges.

### Installing and Removing Packages
 
```
apt install <package>     # Install a package
apt remove <package>      # Remove a package (keeps config files)
apt purge <package>       # Remove a package and its config files
apt autoremove            # Clean up unused dependencies
```
 
### Searching and Inspecting Packages
 
```
apt search <keyword>      # Search for a package by keyword
apt show <package>        # Show detailed package information
apt list --installed      # List all currently installed packages
```
 
### Updating the System
 
```
apt update                # Refresh the package index
apt upgrade               # Upgrade all installed packages
apt full-upgrade          # Upgrade with automatic dependency handling
```
