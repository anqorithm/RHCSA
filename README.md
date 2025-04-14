# RHCSA

## Introduction
This document provides a comprehensive guide to preparing for the RHCSA exam. It includes network configuration instructions, task lists, and solutions to common tasks.

## Table of Contents
- [Crush the RHCSA Exam!](#crush-the-rhcsa-exam)
  - [Create Users](#create-users)
  - [Password Policy](#password-policy)
  - [Switch User](#switch-user)
  - [Sudo Config](#sudo-config)
  - [Find Files](#find-files)
  - [Grep](#grep)
  - [sed](#sed)
  - [Default File](#default-file)
  - [File Permissions](#file-permissions)
  - [Fix Permissions](#fix-permissions)
  - [Directory Inheritance](#directory-inheritance)
  - [Create Symlink](#create-symlink)
  - [SCP](#scp)
  - [at](#at)
  - [Cron & Tar](#cron-tar)
  - [Configure NTP](#configure-ntp)
  - [Logrotate](#logrotate)
  - [Journalctl Export](#journalctl-export)
  - [Processes](#processes)
  - [Renice](#renice)
  - [SELinux Enforce](#selinux-enforce)
  - [SELinux Context](#selinux-context)
  - [SELinux Boolean](#selinux-boolean)
  - [Network Config](#network-config)
  - [Add Repository](#add-repository)
  - [Autofs for NFS](#autofs-for-nfs)
  - [NFS](#nfs)
  - [Changing Targets](#changing-targets)
  - [Tuned](#tuned)
  - [Create Partition](#create-partition)
  - [Swap Partition](#swap-partition)
  - [Volume Group (VG)](#volume-group-vg)
  - [Logical Volume (LV)](#logical-volume-lv)
  - [Extend LV](#extend-lv)
  - [Reset Root Password](#reset-root-password)
  - [Firewalld Zones](#firewalld-zones)
  - [Service Setup](#service-setup)
  - [Podman- Pull & Tag](#podman--pull-tag)
  - [Podman- Volumes](#podman--volumes)
  - [Podman- Systemd](#podman--systemd)

## Create Users

### Question
Create three users (`natasha`, `harry`, and `sarah`) and two groups (`starwars`, `startrek`) with specific conditions:

`natasha` and `harry` are in the `starwars` group.
`sarah` is in the `startrek` group and should not be able to log in.
Set `harry`'s UID to `2000`.
All three users should have the password `nerdzrule`.

### Tasks
Ensure the users are created with the specified conditions.
Verify the group memberships and login capabilities.
Check

### Solution

```bash
# Create groups
$ sudo groupadd starwars
$ sudo groupadd startrek

# Create users with specific group memberships
$ sudo useradd -G starwars natasha
$ sudo useradd -G starwars harry -u 2000
$ sudo useradd -G startrek sarah -s /sbin/nologin

# Set passwords for all users
$ sudo passwd natasha
~ Enter nerdzrule
~ Confirm nerdzrule

$ sudo passwd harry
~ Enter nerdzrule
~ Confirm nerdzrule

$ sudo passwd sarah
~ Enter nerdzrule
~ Confirm nerdzrule
```

## Password Policy

### Question
Configure the system to ensure the following:

All user passwords expire after `90` days.
All user passwords require a minimum length of `8` characters.

### Tasks
Update the system password policy to enforce the requirements.

### Solution

```bash
# Edit the login.defs file to set password expiration
$ sudo vi /etc/login.defs
# PASS_MAX_DAYS   90

# Configure password quality requirements
$ sudo vi /etc/security/pwquality.conf
# minlen = 8
```

## Switch User

### Question
Ensure that you can switch to a different user account named `shrek`.

### Tasks
- Verify the existence of the user `shrek` on the system.
- You have sudo privilege to use `su`.
- Confirm that you have successfully switched to the `shrek` user.

### Solution

```bash
# Check if shrek user exists
$ id shrek

# Switch to shrek user
$ sudo su - shrek

# Verify current user
$ whoami
```

## Sudo Config

### Question
Configure the user `neo` to be able to use `sudo` for the `systemctl restart httpd` command without being prompted for a password.

### Tasks
Create a sudo configuration to allow `neo` to run `systemctl restart httpd` without using a password.

### Solution

```bash
# Create or edit sudo configuration for neo
$ sudo visudo
# Add the following line at the end of the file:
# neo ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart httpd
```

## Find Files

### Question
Use the `find` command to locate all files owned by the user `frank` and copy them to `/root/findresults`.

### Tasks
- Ensure the user `frank` exists.
- Use `find` to locate all files owned by `frank`.
- Copy the files to `/root/findresults`.

### Solution

```bash
# Check if frank user exists
$ id frank

# Create destination directory
$ sudo mkdir -p /root/findresults

# Find and copy files owned by frank
$ sudo find / -user frank -type f -exec cp {} /root/findresults \;

# Verify copied files
$ sudo ls /root/findresults
```

## Grep

### Question
In the document `/home/student/animals.txt` find all lines that contain the string `fish`, regardless of case. Put all those lines into a separate file at `/home/student/aquarium.txt`. Any blank spaces at the top or bottom of the file will cause this task to be marked WRONG.

### Tasks
- Use `grep` to filter `/home/student/animals.txt` for lines containing `fish`, regardless of case.
- Redirect the filtered output to the file `aquarium.txt`, located in `/home/student`.
- Confirm the filtered output file contains only the desired strings.

### Solution

```bash
# Use grep with case-insensitive search and redirect output
$ grep -i "fish" /home/student/animals.txt > /home/student/aquarium.txt

# Verify the contents of the new file
$ cat /home/student/aquarium.txt
```

## sed

### Question
This task involves replacing every instance of a specified substring within a document. You will edit the document in place, so each occurrence is modified directly in the file.

### Tasks
- Replace all occurrences of `from` with `appears in` within `/home/student/characters.txt`.
- Ensure the replacement is applied directly to the file, with no extra changes.

### Solution

```bash
# Replace text in place using sed
$ sed -i 's/from/appears in/g' /home/student/characters.txt

# Verify the changes
$ cat /home/student/characters.txt
```

## Default File

### Question
Ensure that an empty file named `NEWFILE` is copied to the home directory of every new user automatically.

### Tasks
- Create an empty file named `NEWFILE`.
- Ensure that `NEWFILE` is copied to the home directory of each new user when they are created.

### Solution

```bash
# Create the skeleton directory if it doesn't exist
$ sudo mkdir -p /etc/skel

# Create the empty NEWFILE in the skeleton directory
$ sudo touch /etc/skel/NEWFILE

# Verify the file exists in skeleton
$ ls -l /etc/skel/NEWFILE
```

## File Permissions

### Question
Configure the default file and directory permissions for the user `daffy`:

- Files created by `daffy` should have permissions `rw-r--r--`.
- Directories created by `daffy` should have permissions `rwxr-xr-x`.

### Tasks
Set the required permissions for `daffy`.

### Solution

```bash
# Set default file permissions (umask 022)
$ sudo su - daffy
$ umask 022

# Verify the umask setting
$ umask

# Create test file and directory to verify permissions
$ touch testfile
$ mkdir testdir
$ ls -l
```

## Fix Permissions

### Question
Copy the file `/etc/fstab` to `/var/tmp` and configure its permissions as follows:

- The file `/var/tmp/fstab` is owned by `harry`.
- The file `/var/tmp/fstab` belongs to the group `starwars`.
- The file `/var/tmp/fstab` should not be executable by anyone.
- `harry` should be able to read and write to the file.
- All other users (current or future) should only be able to read `/var/tmp/fstab`.

### Tasks
- Copy `/etc/fstab` to `/var/tmp`.
- Apply the required ownership and permissions.

### Solution

```bash
# Copy the file
$ sudo cp /etc/fstab /var/tmp/

# Set ownership and group
$ sudo chown harry:starwars /var/tmp/fstab

# Set permissions (rw-r--r--)
$ sudo chmod 644 /var/tmp/fstab

# Verify the settings
$ ls -l /var/tmp/fstab
```

## Directory Inheritance

### Question
Create a directory `/home/student/caveofwonders` with the following conditions:

- Only members of the group `agrabah` can read, write, and execute to this directory (it is understood that the root user can read/write/execute anything)
- All files and subdirectories within inherit the `agrabah` group.

### Tasks
Create the directory and configure the required permissions.

### Solution

```bash
# Create the directory
$ sudo mkdir /home/student/caveofwonders

# Set the group ownership
$ sudo chgrp agrabah /home/student/caveofwonders

# Set permissions (rwxrwx---)
$ sudo chmod 770 /home/student/caveofwonders

# Set the SGID bit for group inheritance
$ sudo chmod g+s /home/student/caveofwonders

# Verify the settings
$ ls -ld /home/student/caveofwonders
```

## Create Symlink

### Question
Create a soft link to the `/etc/passwd` file and place the soft link in `/tmp/passwd_link`.

### Tasks
- Create a soft link to the `/etc/passwd` file.
- Place the soft link in `/tmp/passwd_link`.

### Solution

```bash
# Create the symbolic link
$ sudo ln -s /etc/passwd /tmp/passwd_link

# Verify the link
$ ls -l /tmp/passwd_link
```

## SCP

### Question
Copy the file `/home/student/longfile.txt` from your local machine to the `/tmp` directory on a remote machine (BCHD). Use `scp` to copy the file, authenticating as user `student` with the password `alta3`. You should also have BCHD's IP address from the setup step above.

### Tasks
- Use `scp` to copy `/home/student/longfile.txt` to the remote machine's `/tmp` directory.
- Authenticate using the username `student` and the password `alta3`.
- Verify that the file exists on the remote machine in `/tmp`.

### Solution

```bash
# Copy file to remote machine
$ scp /home/student/longfile.txt student@BCHD:/tmp/

# Verify file on remote machine
$ ssh student@BCHD "ls -l /tmp/longfile.txt"
```

## at

### Question
Write a bash script that, when executed, displays a message and appends it to a file in `/home/student`. Schedule this script to run at midnight using the `at` command by the user `student`. The message should be a Homer Simpson quote: `Oh, so they have Internet on computers now!`.

### Tasks
- Create a bash script named `/home/student/homer_quote.sh` that outputs `Oh, so they have Internet on computers now!` to the terminal and appends it to `/home/student/homer_output.txt`.
- Make the script executable.
- Schedule the script to run at midnight using `at` by the user `student`.

### Solution

```bash
# Create the script
$ cat > /home/student/homer_quote.sh << 'EOF'
#!/bin/bash
echo "Oh, so they have Internet on computers now!"
echo "Oh, so they have Internet on computers now!" >> /home/student/homer_output.txt
EOF

# Make the script executable
$ chmod +x /home/student/homer_quote.sh

# Schedule the script to run at midnight
$ at midnight -f /home/student/homer_quote.sh

# Verify the scheduled job
$ atq
```

## Cron & Tar

### Question
In this task, create a cron job that repeats every five minutes as the user `student`. The job should tar and gzip the `/etc` directory into a compressed file, saving it in `/home/student` each time it runs.

### Tasks
- Set up a cron job that executes every five minutes.
- Ensure the job tars and compresses the `/etc` directory to a file named `bowser_capture.tar.gz` in `/home/student`.
- Verify that the cron job correctly saves the compressed tar file in `/home/student`.

### Solution

```bash
# Create the cron job
$ crontab -e
# Add the following line:
# */5 * * * * tar -czf /home/student/bowser_capture.tar.gz /etc

# Verify the cron job
$ crontab -l

# Check if the file is being created
$ ls -l /home/student/bowser_capture.tar.gz
```

## Resources
- [Official RHCSA Exam Guide](https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam)
- [RHCSA Study Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/ex200_red_hat_certified_system_administrator_rhcsa_exam/index)

## SELinux Enforce

### Question
Ensure that SELinux is running persistently in enforcing mode.

### Tasks
- Check the current mode of SELinux.
- If SELinux is not in enforcing mode, change it to enforcing.
- Confirm that SELinux is now in enforcing mode.

### Solution

```bash
# Check current SELinux mode
$ getenforce

# If not enforcing, set to enforcing mode
$ sudo setenforce 1

# Make the change persistent
$ sudo vi /etc/selinux/config
# Set SELINUX=enforcing

# Verify the mode
$ getenforce
```

## SELinux Context

### Question
Restore the SELinux context of the service file `/etc/systemd/system/container-pikachu.service`.

### Tasks
- Restore the SELinux context of the service file `/etc/systemd/system/container-pikachu.service`.
- Ensure the context change is persistent across reboots.

### Solution

```bash
# Restore the default SELinux context
$ sudo restorecon -v /etc/systemd/system/container-pikachu.service

# Make the context change persistent
$ sudo semanage fcontext -a -t systemd_unit_file_t "/etc/systemd/system/container-pikachu.service"

# Verify the context
$ ls -Z /etc/systemd/system/container-pikachu.service
```

## Network Config

### Question
Configure the `ens4` network interface with the following settings through the Network Manager Text User Interface (nmtui):
- IP Address: `192.168.50.100`
- Gateway: `192.168.50.1`
- DNS: `8.8.8.8`
- Change the system hostname to `millennium-falcon`.

### Tasks
- Launch nmtui and configure network settings.
- Set the system hostname through nmtui.
- Verify the network configuration.

### Solution

```bash
# Launch the Network Manager Text User Interface
$ sudo nmtui

# In the nmtui interface:
# 1. Select "Edit a connection"
# 2. Choose "ens4"
# 3. Select "Edit"
# 4. Navigate to "IPv4 CONFIGURATION"
# 5. Set "Automatic" to "Manual"
# 6. Add IP address: 192.168.50.100/24
# 7. Add Gateway: 192.168.50.1
# 8. Add DNS: 8.8.8.8
# 9. Select "OK" and "Back"
# 10. Select "Set system hostname"
# 11. Enter "millennium-falcon"
# 12. Select "OK"
# 13. Select "Activate a connection"
# 14. Choose "ens4"
# 15. Select "Deactivate" and then "Activate"

# Verify the network configuration
$ ip addr show ens4
$ hostname
```

## Add Repository

### Question
Add the repository located at `https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm` to the system.

### Tasks
- Use the appropriate command to add the repository.
- Verify the repository has been added successfully.

### Solution

```bash
# Add the EPEL repository
$ sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

# Verify the repository has been added
$ sudo dnf repolist | grep epel
```

## Autofs for NFS

### Question
Use autofs to automatically mount the NFS export `/srv/nfs` from the NFS server at `nfs-server.example.com`. Mount it locally to `/mnt/nfs`.

### Tasks
- Install and configure autofs.
- Set up the mount point and configuration.
- Start and enable the autofs service.

### Solution

```bash
# Install autofs package
$ sudo dnf install autofs

# Create the mount point directory
$ sudo mkdir -p /mnt/nfs

# Edit the master map file
$ sudo vi /etc/auto.master
# Add the following line:
# /mnt/nfs /etc/auto.nfs

# Create and edit the NFS map file
$ sudo vi /etc/auto.nfs
# Add the following line:
# * -fstype=nfs,rw,sync nfs-server.example.com:/srv/nfs/&

# Start and enable the autofs service
$ sudo systemctl enable --now autofs

# Verify the mount
$ ls /mnt/nfs
```
