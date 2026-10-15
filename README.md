# 🐧 Linux for DevOps Engineers

## 📌 About This Repository

This repository documents my Linux learning journey, focusing on the concepts, commands, system internals, and troubleshooting techniques used in real-world DevOps environments.

My goal is to understand not only **what Linux commands do, but also what happens behind the scenes when they execute** and how Linux supports modern DevOps tools and infrastructure.

Topics are organized from fundamentals to practical applications, with examples, hands-on exercises, and interview preparation.

## 🎯 Learning Objectives

- Understand Linux architecture and operating system fundamentals.
- Learn essential Linux commands and filesystem navigation.
- Manage users, groups, file permissions, and processes.
- Understand services, system logs, networking, and storage.
- Write Bash scripts to automate repetitive tasks.
- Troubleshoot common Linux server issues.
- Understand how Linux supports Docker, Kubernetes, Jenkins, and cloud infrastructure.
- Practise explaining Linux concepts through real-world DevOps scenarios.

## 🗺️ Linux Learning Roadmap

### 01. Linux Fundamentals
- What is Linux?
- Linux kernel and operating system architecture
- Linux distributions: Ubuntu, Debian, and RHEL
- Linux boot process
- Shell vs. terminal
- System calls and kernel responsibilities

### 02. Linux Filesystem
- Linux directory hierarchy
- Absolute and relative paths
- File types and inodes
- Hard links and symbolic links
- Important directories: `/etc`, `/var/log`, `/home`, `/tmp`, `/usr`

### 03. Basic Linux Commands
- Navigation: `pwd`, `ls`, `cd`
- File operations: `touch`, `mkdir`, `cp`, `mv`, `rm`
- Viewing files: `cat`, `less`, `head`, `tail`
- Searching: `find`, `grep`
- Pipes, redirection, and command chaining

### 04. Users, Groups, and Permissions
- User and group management
- File ownership
- `chmod`, `chown`, `chgrp`, `umask`
- `sudo` and least privilege
- Special permissions: setuid, setgid, and sticky bit

### 05. Process Management
- Processes and process IDs (PIDs)
- Process states and signals
- `ps`, `top`, `pgrep`, `kill`
- CPU and memory usage
- Foreground and background processes

### 06. Package Management
- Package managers and repositories
- APT for Ubuntu and Debian
- DNF for modern RHEL-family systems
- Installing, updating, and removing packages
- Package dependencies

### 07. Services and systemd
- Services vs. processes
- `systemctl` commands
- Service startup and restart behavior
- Systemd units and targets
- Troubleshooting failed services

### 08. Logs and Troubleshooting
- Linux log locations
- `journalctl` and log inspection
- Log rotation
- Application troubleshooting
- Root-cause analysis

### 09. Networking and SSH
- IP addresses, ports, and routing
- DNS fundamentals
- `ip`, `ping`, `ss`, `curl`, `dig`
- SSH authentication and key management
- `scp` and remote file transfers
- Network connectivity troubleshooting

### 10. Storage and Filesystems
- Disks, partitions, and filesystems
- Mount points
- `df`, `du`, `lsblk`
- Inodes and disk-space troubleshooting
- Logical Volume Management (LVM)

### 11. Bash Scripting
- Variables and environment variables
- Conditions and loops
- Functions and command-line arguments
- Exit codes and error handling
- Logging and automation scripts

### 12. Cron and Scheduling
- Cron syntax
- Scheduled jobs
- Crontab management
- Job output and error logging
- Automated maintenance tasks

### 13. Linux Security
- Least privilege
- Secure SSH configuration
- File permission best practices
- Firewall fundamentals
- System patching
- Secrets management basics

### 14. Linux for DevOps
- Linux and Docker internals
- Processes, namespaces, and cgroups
- Jenkins agents and shell execution
- Environment variables and deployments
- Server health checks
- Container and application troubleshooting

### 15. Hands-on Labs and Projects
- Linux server health-check script
- Log analysis and error investigation
- Disk-space troubleshooting
- Service failure diagnosis
- Bash automation
- Basic server monitoring

### 16. Linux Interview Preparation
- Linux fundamentals interview questions
- Command-based questions
- Permissions and process scenarios
- Networking and storage troubleshooting
- Bash scripting questions
- Real-world DevOps interview scenarios

## 🛠️ Practice Environment

I will practise Linux using a Linux virtual machine or another authorized lab environment.

Some commands I will use regularly:

```bash
whoami
pwd
uname -r
cat /etc/os-release
free -h
df -h
ps aux
```

These commands help inspect the current user, working directory, kernel version, operating system, memory, disk usage, and running processes.

## 🔍 How I Approach Each Topic

For every topic, I aim to document:

1. What it is and why it matters.
2. How it works behind the scenes.
3. Syntax and practical command examples.
4. Expected output and its interpretation.
5. Real-world DevOps use cases.
6. Common errors and troubleshooting.
7. Interview questions and answers.
8. A hands-on exercise to reinforce the concept.

## 🚀 Why Linux Matters in DevOps

Linux provides essential operating-system capabilities used by many DevOps tools and workloads.

- **Docker:** Uses Linux kernel features for container isolation and resource management.
- **Kubernetes:** Runs container workloads on nodes managed by an operating system and container runtime.
- **Jenkins:** Executes build and deployment commands through agents and processes.
- **Cloud platforms:** Provide Linux virtual machines for hosting and operating applications.

Understanding Linux helps engineers investigate issues at the operating-system level instead of relying only on tool-specific commands.

## 📈 Learning Goal

My objective is to develop practical Linux skills, understand system behavior, automate repetitive tasks, and troubleshoot common infrastructure problems with confidence.

**The focus is on understanding, practising, documenting, and applying — not just memorizing commands.**

---

*This repository is a work in progress and will grow as I learn and practise each topic.*

#Linux #DevOps #LinuxAdministration #Docker #Kubernetes #Jenkins #CloudComputing
