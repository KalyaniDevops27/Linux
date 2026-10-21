# Introduction to Linux

## 1. What Is Linux?

Linux is an open-source operating system kernel that manages communication between software and hardware.

It manages resources such as:

- CPU
- Memory (RAM)
- Storage devices
- Network interfaces
- Running processes

Technically, Linux is the **kernel**, not the entire operating system. A Linux distribution combines the kernel with system utilities, libraries, package managers, and other software needed to operate a computer or server.

Examples of Linux distributions include Ubuntu, Debian, Red Hat Enterprise Linux (RHEL), and Amazon Linux.

### Why does Linux matter in DevOps?

As a DevOps engineer, I work with applications running on servers, cloud infrastructure, containers, and CI/CD pipelines.

Many of these environments use Linux. Understanding Linux helps me deploy applications, automate tasks, investigate failures, manage permissions, and troubleshoot production issues.

For example, when an application becomes unavailable on an AWS EC2 instance, I may need to investigate its running process, memory usage, disk space, network connectivity, service status, and application logs.

Linux provides the tools needed to investigate these problems.

---

## 2. Linux vs. Linux Distribution

These terms are related but have different meanings.

### Linux kernel

The kernel is the core component of the operating system. It manages hardware resources and provides essential services to applications.

Its responsibilities include:

- Process scheduling
- Memory management
- Device management
- Filesystem operations
- Network communication
- Access control and security enforcement

### Linux distribution

A Linux distribution packages the kernel with system utilities, libraries, software, and package-management tools.

Examples:

| Distribution | Common use cases |
|---|---|
| Ubuntu | Cloud servers, development, application hosting |
| Amazon Linux | AWS workloads |
| RHEL | Enterprise infrastructure |
| Debian | Servers and general-purpose computing |
| Rocky Linux | Enterprise-oriented environments |

**Example:** Linux is the kernel, while Ubuntu is a distribution built around it.

Different distributions may use different package managers and default configurations. For example, Ubuntu commonly uses `apt`, while modern RHEL-based distributions commonly use `dnf`.

---

## 3. Linux Architecture: A Basic Overview

To understand Linux, it helps to know how its main components interact.

The major components are:

1. Hardware
2. Kernel
3. System libraries and utilities
4. Shell
5. User applications

### Hardware

Hardware includes the CPU, RAM, storage devices, and network interfaces.

Applications need these resources to execute, store information, and communicate over networks.

### Kernel

The kernel manages access to hardware resources and provides operating-system services.

Applications generally do not access hardware directly. They request services from the kernel through defined interfaces.

### System libraries and utilities

System libraries provide reusable functions that applications can use to interact with operating-system services.

Utilities such as `ls`, `cp`, and `grep` provide common command-line operations.

### Shell

A shell accepts commands, interprets them, and launches programs.

Examples include Bash and Zsh.

### Applications

Applications perform tasks such as serving web pages, processing requests, storing data, and running background jobs.

A web server such as Nginx is an example of an application that can run on Linux.

We will explore these components in detail in `linux-architecture.md`.

---

## 4. What Happens Under the Hood When a Linux Command Runs?

Understanding what happens behind the scenes is more valuable than memorizing commands.

Consider this command:

```bash
ls -l /var/log
```

It lists directory entries and displays details such as permissions, ownership, file sizes, and modification times.

Here is a simplified explanation of what happens.

### Step 1: The shell reads the command

The shell receives the command and identifies the program name, `ls`, and its arguments, `-l` and `/var/log`.

### Step 2: The shell locates the program

The shell locates the executable using its command-resolution rules. For a typical external command such as `ls`, this usually involves searching directories listed in the `PATH` environment variable.

### Step 3: A process is created or executed

The shell launches the program. On a typical Linux system, this involves process-creation and program-execution mechanisms such as `fork()` and `execve()`, although implementations can vary.

The operating system schedules the process so it can use CPU time.

### Step 4: The program requests filesystem information

The `ls` program requests information about entries in `/var/log` using operating-system interfaces, including system calls.

The kernel checks permissions and coordinates access to filesystem information.

### Step 5: The program formats the result

The `ls` program processes the returned information and prepares a human-readable listing.

### Step 6: The output reaches the terminal

The program writes its output to standard output, commonly called `stdout`. The terminal displays the result.

The simplified flow is:

**User → Shell → Process → System calls → Kernel → Filesystem → Output**

The exact sequence depends on the command and the system. Some commands are shell built-ins, and others may perform additional operations.

### Why does this matter to a DevOps engineer?

Suppose a command fails with a permission error.

Understanding the interaction between the process, the kernel, and filesystem permissions helps explain why the current user cannot access a particular file.

Similarly, understanding processes and resource management helps when investigating high CPU usage, memory pressure, or an application that refuses to start.

---

## 5. Why Linux Is Important in DevOps

Linux is widely used across modern infrastructure.

### Cloud computing

Cloud virtual machines commonly run Linux to host APIs, web applications, databases, and background services.

Example: Connecting to an Ubuntu EC2 instance using SSH.

### Docker and containers

Many containers use Linux-based images. Linux kernel features such as namespaces and cgroups help isolate processes and control resource usage.

Understanding Linux makes it easier to troubleshoot container permissions, networking, and resource limits.

### Kubernetes

Many Kubernetes worker nodes run Linux. Engineers need Linux knowledge to investigate node resources, container runtime issues, networking, and system services.

### CI/CD pipelines

Jenkins agents and other build runners frequently use Linux to execute build commands, tests, scripts, and deployment steps.

### Infrastructure automation

Tools such as Ansible commonly use SSH to manage Linux hosts. Terraform can provision the infrastructure on which those hosts run.

### Monitoring and troubleshooting

Linux provides tools to inspect processes, resource usage, services, network connections, and logs.

Linux is not mandatory for every DevOps tool or environment, but it is a foundational skill in many production environments.

---

## 6. Essential Linux Commands for Beginners

### Identify your environment

```bash
whoami
pwd
uname -r
cat /etc/os-release
```

| Command | Purpose |
|---|---|
| `whoami` | Displays the current username |
| `pwd` | Displays the current working directory |
| `uname -r` | Displays the running kernel version |
| `cat /etc/os-release` | Displays Linux distribution information |

### Navigate the filesystem

```bash
ls -l
cd /var/log
pwd
```

- `ls -l` lists directory entries with additional details.
- `cd` changes the current directory.
- `pwd` confirms the current directory.

### Inspect processes and resources

```bash
ps aux
top
free -h
df -h
uptime
```

| Command | Purpose |
|---|---|
| `ps aux` | Displays a snapshot of running processes |
| `top` | Displays processes and resource usage interactively |
| `free -h` | Displays memory and swap statistics |
| `df -h` | Displays filesystem space usage |
| `uptime` | Displays uptime and load averages |

Remember the difference between `df` and `du`.

- `df -h` reports space usage for mounted filesystems.
- `du -sh /var/log` estimates the space used by the specified directory.

### Inspect services and logs

On systems using systemd:

```bash
systemctl status nginx
journalctl -u nginx -n 50 --no-pager
```

These commands check the status of the Nginx service and display recent logs associated with it.

They assume Nginx is installed and managed as a systemd service. The actual service name and log configuration may differ.

---

## 7. Real-World DevOps Scenario: Application Unavailable

Imagine a web application running on an Ubuntu server in AWS. Users report that the application is unavailable.

As a DevOps engineer, I would investigate systematically instead of immediately restarting the server.

### Step 1: Connect to the server

```bash
ssh -i devops-key.pem ubuntu@<server-ip>
```

Replace the placeholders with the appropriate key file and server address. The key and server must be configured for SSH access.

### Step 2: Check the service

```bash
sudo systemctl status nginx
```

If Nginx is stopped or has failed, inspect the reported status and logs.

### Step 3: Check CPU and memory

```bash
top
free -h
```

Look for unusually high CPU usage, memory pressure, or unexpected processes.

### Step 4: Check filesystem space

```bash
df -h
du -sh /var/log
```

A full filesystem can prevent applications from writing logs, temporary files, or application data.

The `du` command helps estimate the space consumed by the log directory, while `df` reports filesystem usage.

### Step 5: Check service logs

```bash
sudo journalctl -u nginx -n 50 --no-pager
```

Look for errors that explain why the service failed or why requests are not being handled correctly.

### Step 6: Investigate network connectivity

```bash
ss -lntp
```

This displays listening TCP sockets and, where permissions allow, associated process information.

Check whether the expected service is listening on the correct port. Also investigate firewall rules, cloud security groups, reverse-proxy settings, and application health as appropriate.

### Step 7: Apply the appropriate fix

Once the cause is identified, apply the relevant correction and verify that the application works.

For example, the fix might involve correcting a configuration error, freeing disk space safely, or resolving a network rule.

**Production lesson:** Collect evidence first. Identify the cause, fix it, and verify the result. Restarting a service without understanding the failure may hide the underlying problem.

---

## 8. Linux Permissions: An Important Foundation

Linux uses permissions to control who can read, modify, or execute files and directories.

For a typical file, the permission categories are:

- **User:** The file owner
- **Group:** Members of the file's group
- **Others:** Everyone else

Example:

```bash
ls -l script.sh
```

Possible output:

```text
-rwxr-xr-- 1 ubuntu developers 1200 Oct 15 10:00 script.sh
```

In this example:

- `rwx` means the owner can read, write, and execute the file.
- `r-x` means the group can read and execute it.
- `r--` means others can only read it.

The execute permission is particularly important when running shell scripts and managing application files.

To investigate a permission issue, inspect the file's permissions and ownership before changing them.

Avoid using broad permissions such as `chmod 777` as a quick fix. Grant only the access that is actually required.

We will cover permissions, ownership, users, and groups in more detail in later Linux topics.

---

## 9. Common Linux Interview Questions

### Q1. What is Linux?

Linux is an open-source operating system kernel that manages hardware resources and provides essential services to applications. Complete Linux distributions combine the kernel with utilities, libraries, and other software.

### Q2. What is the difference between Linux and Ubuntu?

Linux is the kernel, while Ubuntu is a Linux distribution built around it.

### Q3. Why is Linux important for DevOps?

Linux is widely used for cloud servers, containers, CI/CD agents, automation, and production workloads. Linux knowledge helps engineers manage resources, investigate failures, and troubleshoot applications.

### Q4. What happens when you execute a Linux command?

The shell interprets the command and launches a program or executes a shell built-in. For a typical external command, the program runs as a process and may request operating-system services through system calls. It then produces output or an exit status.

### Q5. What is the difference between `df` and `du`?

`df` reports space usage for filesystems, while `du` estimates the disk space used by files and directories.

### Q6. How do you check whether a service is running?

On a system using systemd, use:

```bash
systemctl status nginx
```

Replace `nginx` with the actual service name.

### Q7. How would you investigate high CPU usage?

I would start with `top` or `ps aux` to identify processes consuming CPU. Then I would examine the relevant process, application behavior, logs, and recent changes before deciding on a fix.

### Q8. How would you investigate a full disk?

I would use `df -h` to identify the affected filesystem and `du` to investigate which directories are consuming space. I would then determine what is safe to remove, archive, or rotate without affecting the application.

### Q9. What is a Linux process?

A process is an instance of a running program, with its own execution state and associated operating-system resources.

### Q10. Is Linux required for Docker and Kubernetes?

Not in every environment, but Linux knowledge is highly valuable because many container workloads and Kubernetes nodes rely on Linux kernel features and system services.

---

## 10. Practical Exercises

Run these commands in a Linux virtual machine, personal lab, or authorized environment.

```bash
whoami
pwd
uname -r
cat /etc/os-release
uptime
free -h
df -h
ps aux
```

---

## 11. Key Takeaways

- Linux is the kernel at the heart of many server operating systems.
- A Linux distribution packages the kernel with the tools needed to operate a system.
- The shell provides a way to interact with the operating system through commands.
- Processes use CPU, memory, filesystems, and network resources managed by the operating system.
- Linux is important for cloud infrastructure, Docker, Kubernetes, CI/CD, and automation.
- Troubleshooting requires understanding the evidence provided by commands, logs, and system metrics.

The goal is not to memorize every command. It is to understand how Linux works and use that knowledge to solve real infrastructure problems.

---

## Next Topic

**Linux Architecture**

In the next document, we will explore hardware, the kernel, system calls, system libraries, shells, and user-space applications—and trace how these components interact when a Linux command executes.