# Linux File System Hierarchy

## 1. Introduction

The Linux file system hierarchy defines how files and directories are organized in a Linux operating system.

Unlike Windows, where users commonly work with drive letters such as `C:` and `D:`, Linux organizes files under one main directory called the **root directory (`/`)**.

System configuration, user files, applications, logs, devices, and runtime information all fit into this hierarchy.

For a DevOps engineer, understanding this structure is important when configuring services, troubleshooting application failures, investigating logs, and managing disk space. 

---

## 2. What Is the Root Directory?

The root directory, represented by `/`, is the starting point of the Linux file system.

A simplified hierarchy looks like this:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── sys
├── tmp
├── usr
└── var
```

The exact directory structure varies by Linux distribution.

**Important distinction:**

- `/` is the root of the entire filesystem hierarchy.
- `/root` is the home directory of the root administrator account.

They have different purposes.

---

## 3. Important Linux Directories

### `/etc` — System Configuration

The `/etc` directory stores system-wide configuration files.

Examples:

- `/etc/ssh/` — SSH configuration files.
- `/etc/hosts` — Local hostname-to-address mappings.
- `/etc/os-release` — Information about the Linux distribution.
- `/etc/fstab` — Configuration for filesystems that may be mounted during startup.

**DevOps use case:** If an SSH service or another system service is not behaving as expected, inspect its configuration files. Make changes carefully and validate them before restarting the service.

### `/var` — Variable Data

The `/var` directory contains data that changes during normal system operation.

Examples:

- `/var/log/` — Many system and application logs.
- `/var/cache/` — Cache data.
- `/var/spool/` — Queued work, depending on the service.

**DevOps use case:** When a server runs out of disk space, investigate which directories are growing. Large logs are one possible cause.

Useful commands:

```bash
du -sh /var/log
df -h
```

### `/home` — User Home Directories

The `/home` directory usually contains home directories for regular users.

Example:

```text
/home/
├── ubuntu/
├── devops/
└── developer/
```

Each user may have personal files and configuration in their own home directory. Access depends on file permissions.

**DevOps use case:** Find a user's files, scripts, or SSH-related files when troubleshooting an account.

### `/root` — Root User's Home Directory

`/root` is the home directory of the root administrator account.

It is not the same as `/`.

Because root has elevated privileges, use this account carefully and follow the access controls of your environment.

### `/usr` — Programs and Shared Resources

The `/usr` directory contains many installed programs, libraries, and shared resources.

Common locations include:

- `/usr/bin/` — Many user commands and executables.
- `/usr/sbin/` — Many system administration utilities.
- `/usr/lib/` — Libraries and supporting files.
- `/usr/share/` — Architecture-independent shared data and documentation.

**DevOps use case:** Understand where installed commands and supporting software may be located when diagnosing a missing command or application dependency.

### `/bin` and `/sbin` — Essential Commands

Traditionally:

- `/bin` contains essential commands.
- `/sbin` contains essential system administration utilities.

On many modern Linux distributions, these directories are symbolic links into `/usr`, such as `/usr/bin` and `/usr/sbin`.

Do not assume every distribution uses the exact same layout.

### `/lib` and `/lib64` — Essential Libraries

These directories traditionally hold essential shared libraries and, depending on the system, kernel modules.

Programs often depend on shared libraries to run.

The exact layout varies, and some distributions merge these directories into `/usr`.

### `/tmp` — Temporary Files

The `/tmp` directory is used for temporary files created by applications and users.

Important points:

- Files may be removed automatically.
- Some systems clear temporary files during startup or through scheduled cleanup.
- It should not be treated as permanent storage.

**DevOps use case:** Investigate temporary files created by scripts or applications. Store important application data in an appropriate persistent location instead.

### `/opt` — Optional or Additional Software

The `/opt` directory is commonly used for additional software packages that are not installed into the standard system directories.

For example, an organization may install a third-party application under `/opt`.

The exact installation location depends on the software and deployment standards.

### `/dev` — Device Files

Linux represents many devices through special files under `/dev`.

Examples include:

- `/dev/sda` — A possible name for a disk device.
- `/dev/nvme0n1` — A possible name for an NVMe storage device.
- `/dev/null` — A special device that discards data written to it.

Device names vary between systems. Do not assume a particular disk name identifies the same physical or virtual disk on every server.

### `/proc` — Process and Kernel Information

`/proc` is a virtual filesystem that exposes information about running processes and the kernel.

Examples:

- `/proc/cpuinfo` — CPU information.
- `/proc/meminfo` — Memory information.
- `/proc/<PID>/` — Information associated with a process ID.

Most of this information is generated by the running kernel rather than read from ordinary files stored on disk.

**DevOps use case:** Inspect system information and running processes while investigating performance issues.

### `/sys` — Kernel and Device Information

`/sys` is another virtual filesystem. It exposes structured information about kernel objects, devices, and drivers.

It is useful for examining certain hardware and kernel-related details.

Some entries are writable interfaces, so do not modify them unless you understand the consequences.

### `/boot` — Boot-Related Files

The `/boot` directory commonly contains files needed to start Linux, such as:

- Linux kernel images.
- Initial RAM filesystem images, often called initramfs.
- Bootloader-related files, depending on the configuration.

**DevOps use case:** Understand where boot-related files reside when investigating operating system startup problems.

### `/run` — Runtime State

`/run` stores volatile runtime information created after the system starts.

Services may store process identifiers, sockets, and other runtime state here.

Its contents are generally not intended to persist across reboots.

### `/mnt` and `/media` — Mount Points

These directories are commonly used as mount points.

- `/mnt` is often used for temporary or manual mounts.
- `/media` is commonly used for removable media.

A mounted filesystem becomes accessible through its mount point.

**DevOps use case:** Understand where additional disks or filesystems are made available to the operating system.

---

## 4. How Linux Organizes These Resources

A Linux application may interact with different parts of the filesystem during normal operation.

For example:

1. A service reads configuration from a file under `/etc`.
2. The operating system starts the service as a process.
3. The service writes logs to a configured location, possibly under `/var/log`.
4. Process information is exposed through `/proc`.
5. The application may store persistent data on a separate mounted filesystem.

These paths are conventions, not universal rules. Applications can use custom configuration, logging, and data locations.

---

## 5. Practical Commands

### View the root directory

```bash
ls -l /
```

Lists entries directly under the root directory.

### Identify the Linux distribution

```bash
cat /etc/os-release
```

Displays distribution identification information.

### Explore configuration files

```bash
ls /etc
```

Lists entries in the system configuration directory.

### Check filesystem capacity

```bash
df -h
```

Displays filesystem capacity and available space in a human-readable format.

### Check log-directory usage

```bash
du -sh /var/log
```

Estimates the total disk usage of the log directory's contents that the current user can access.

### View mounted filesystems

```bash
findmnt
```

Displays mounted filesystems and their mount points.

### List running processes

```bash
ps aux
```

Displays a snapshot of running processes.

### Explore process information

```bash
ls /proc
```

Lists entries exposed by the `/proc` virtual filesystem.

### List device entries

```bash
ls /dev
```

Lists device files and other special entries.

---

## 6. DevOps Troubleshooting Scenarios

### Scenario 1: A service fails to start

A service might have an invalid configuration or a missing dependency.

A reasonable starting approach:

1. Identify the service and its configuration location.
2. Inspect the relevant configuration under `/etc`, if applicable.
3. Check service status and logs.
4. Validate configuration before restarting the service.

For systems using systemd, useful commands include:

```bash
systemctl status <service-name>
journalctl -u <service-name>
```

Replace `<service-name>` with the actual service name.

### Scenario 2: A server is running out of disk space

Start with:

```bash
df -h
du -sh /var/log
```

`df -h` helps identify full filesystems, while `du` helps investigate directory usage. If logs are not the cause, inspect other directories and mounted filesystems.

Avoid deleting files blindly. First identify what they are and whether the application or system still needs them.

### Scenario 3: You need to find out whether an application is running

Use:

```bash
ps aux
```

You can also inspect `/proc` entries for process-specific information. Remember that process IDs change as processes start and stop.

### Scenario 4: A data disk is mounted somewhere unexpected

Use:

```bash
findmnt
df -h
```

These commands help you understand which filesystems are mounted and where they are accessible.

---

## 7. Common Interview Questions

### Q1. What is the Linux file system hierarchy?

It is the organized directory structure through which Linux exposes system files, configuration, user data, applications, devices, and runtime information under a single root directory, `/`.

### Q2. What is the difference between `/` and `/root`?

`/` is the root of the filesystem hierarchy. `/root` is the home directory of the root administrator account.

### Q3. What is the purpose of `/etc`?

It stores system-wide configuration files, such as SSH settings, host mappings, and filesystem mount configuration.

### Q4. Where are Linux logs stored?

Many logs are stored under `/var/log`, but logging locations depend on the application and system configuration. Systems using systemd may also store logs in the journal.

### Q5. What is the difference between `/home` and `/usr`?

`/home` generally contains users' personal directories. `/usr` contains many installed programs, libraries, documentation, and shared resources.

### Q6. What are `/proc` and `/sys`?

They are virtual filesystems that expose information about running processes, the kernel, devices, and other system state. They are not ordinary collections of files stored on disk.

### Q7. What is the difference between `df` and `du`?

`df` reports space usage and availability for filesystems. `du` estimates the disk space used by files and directories.

### Q8. What is the purpose of `/boot`?

It commonly contains the Linux kernel, initial RAM filesystem images, and bootloader-related files needed for system startup.

### Q9. What is a mount point?

A mount point is a directory through which a mounted filesystem becomes accessible.

### Q10. Why should a DevOps engineer understand the Linux filesystem hierarchy?

It helps with configuration management, log analysis, disk-space troubleshooting, application deployment, storage management, and system administration.

---

## 8. Key Takeaway

Linux directories have conventional purposes, and understanding those conventions makes system administration much easier.

For a DevOps engineer, a useful starting point is:

- Configuration: `/etc`
- Logs and changing data: `/var`
- User files: `/home`
- Installed programs and resources: `/usr`
- Temporary files: `/tmp`
- Process and kernel information: `/proc` and `/sys`
- Boot-related files: `/boot`

Learn where to look first, then verify the actual configuration of the system you're troubleshooting.