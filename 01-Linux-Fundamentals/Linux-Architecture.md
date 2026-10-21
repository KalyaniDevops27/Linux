# Linux Architecture

## 1. Introduction

Linux architecture describes how different components of a Linux system work together to execute applications and manage hardware resources.

As a DevOps engineer, understanding Linux architecture helps me troubleshoot application failures, investigate resource usage, and understand how processes interact with the operating system.

For example, when an application runs on an AWS EC2 instance, it needs CPU time, memory, filesystem access, and network connectivity. Linux coordinates these operations through its kernel and supporting components.

The major components of Linux architecture are:

1. Hardware
2. Kernel
3. System calls
4. System libraries
5. Shell and system utilities
6. User applications

---

## 2. Linux Architecture Overview

A simplified view of Linux architecture looks like this:

```text
+--------------------------------------+
|          User Applications           |
|     Nginx, Python, Docker, etc.      |
+--------------------------------------+
|       Shell and System Utilities     |
|       Bash, ls, grep, systemctl      |
+--------------------------------------+
|       System Libraries and APIs      |
|       glibc and other libraries      |
+--------------------------------------+
|             System Calls             |
+--------------------------------------+
|             Linux Kernel             |
| Process | Memory | Filesystem | Net  |
+--------------------------------------+
|               Hardware               |
|       CPU | RAM | Disk | Network     |
+--------------------------------------+
```

This is a conceptual diagram, not a strict execution sequence. Shells and utilities are themselves user-space programs, and applications can use libraries or invoke system calls through other interfaces.

Let's understand each component.

---

## 3. Hardware

Hardware consists of the physical resources a computer or server uses to operate.

The main components are:

- **CPU:** Executes instructions and performs computations.
- **RAM:** Holds data and instructions needed by running programs.
- **Storage:** Stores operating-system files, application code, logs, and persistent data.
- **Network interface:** Sends and receives network traffic.

### Example

Suppose a web application runs on an AWS EC2 instance.

If the application receives many requests, CPU usage may increase. If it caches large amounts of data, memory consumption may increase. If logs grow continuously, disk space may run out.

A DevOps engineer must understand these resources to investigate performance and availability problems.

Useful commands:

```bash
lscpu
free -h
lsblk
df -h
ip addr
```

These commands provide information about CPU, memory, block devices, filesystem space, and network interfaces, respectively.

---

## 4. Linux Kernel

The kernel is the core of the Linux operating system.

It acts as the main resource manager between applications and hardware. It provides controlled access to CPU, memory, storage, and networking resources.

### Major responsibilities

**Process management**

The kernel schedules processes and threads so they can use CPU resources.

**Memory management**

The kernel manages virtual memory, physical memory allocation, and memory protection.

**Filesystem management**

The kernel provides filesystem interfaces and coordinates access to files and directories.

**Device management**

The kernel works with device drivers to communicate with supported hardware.

**Networking**

The kernel implements important networking functionality, including network protocols and packet processing.

**Security and permissions**

The kernel enforces access controls and other security boundaries.

### Example

Suppose a Python application on a Linux server consumes excessive CPU.

The kernel schedules its processes and threads. Tools such as `top` help us identify CPU consumption and investigate which process is responsible.

```bash
top
```

The command helps us observe the problem, while further investigation is needed to determine its cause.

---

## 5. User Space and Kernel Space

Linux separates execution into two broad areas: user space and kernel space.

### User space

User space is where ordinary applications and most system utilities run.

Examples include:

- Nginx
- Python applications
- Bash
- `ls`
- `grep`

These programs normally have restricted privileges. They request operating-system services through defined interfaces rather than directly performing privileged kernel operations.

### Kernel space

Kernel space is where the Linux kernel executes with elevated privileges.

It manages processes, memory, device access, filesystems, and networking.

### Why is this separation important?

Imagine that a web application needs to read a file.

The application runs in user space. It requests file access through operating-system interfaces. The kernel checks the relevant permissions and coordinates the operation.

This separation helps protect the operating system and other processes from unauthorized access.

### DevOps example

If an application receives a `Permission denied` error, the problem may involve file ownership, access permissions, directory permissions, or other security restrictions.

Understanding user space, kernel space, and permissions helps us investigate the failure systematically.

---

## 6. System Calls

A system call is a mechanism through which a user-space program requests a service from the kernel.

Common examples include:

- `openat()` — Opens a file or directory relative to a directory file descriptor.
- `read()` — Reads data from a file descriptor.
- `write()` — Writes data to a file descriptor.
- `execve()` — Executes a program.
- `socket()` — Creates a socket for network communication.

These are Linux system-call interfaces. Programs often access them through higher-level library functions.

### What happens during a system call?

Consider a program reading a file.

1. The program requests the operation through a library or system-call interface.
2. The CPU transitions from user mode to kernel mode through the appropriate system-call mechanism.
3. The kernel validates the request and checks relevant permissions.
4. The kernel performs or coordinates the requested operation.
5. Control returns to the application with a result or an error.

The exact work performed depends on the request. For example, reading a file may use data already cached in memory rather than accessing physical storage.

### Example

When an application reads a configuration file, it relies on operating-system interfaces to obtain the file's contents.

If access is denied, the application may receive an error. A DevOps engineer can investigate file permissions, ownership, application identity, and the relevant logs.

---

## 7. System Libraries

System libraries provide reusable functions that applications can use to perform common operations and interact with operating-system services.

On many Linux systems, the GNU C Library, commonly called `glibc`, provides important functionality for C programs and other software that uses it.

For example, a program may call a library function to open a file. That function can invoke the appropriate system call when needed.

Libraries make application development easier because programmers do not need to implement every low-level operation themselves.

### Important distinction

- **System library:** Provides reusable functions.
- **System call:** Provides an interface for requesting a kernel service.

They are related, but they are not the same thing.

Not every Linux application uses `glibc`; some use other libraries or make system calls through different mechanisms.

---

## 8. Shell and System Utilities

A shell provides an interface for entering commands and running programs.

Common shells include:

- Bash
- Zsh
- Dash

System utilities are programs that perform common administrative and file-management tasks.

Examples include:

- `ls` — Lists directory entries.
- `grep` — Searches text for matching patterns.
- `find` — Searches for files and directories.
- `ps` — Displays process information.
- `systemctl` — Manages and inspects services on systemd-based systems.

### What happens when we execute a command?

Consider:

```bash
ls -l /var/log
```

A simplified explanation is:

1. The shell reads and interprets the command.
2. The shell locates the `ls` executable using command-resolution rules.
3. The shell launches the program, commonly using process-creation and execution mechanisms.
4. The `ls` process requests directory information through operating-system interfaces.
5. The kernel checks permissions and provides the requested information.
6. The program formats the output and writes it to standard output.
7. The terminal displays the result.

This is a simplified model. Some commands are shell built-ins, and individual programs may perform many additional operations.

### Example

During production troubleshooting, I may run:

```bash
ps aux
df -h
journalctl -u nginx -n 50 --no-pager
```

The shell runs these utilities, which obtain the information needed to investigate processes, filesystem usage, and service logs.

---

## 9. How Linux Components Work Together

Imagine a web application running behind Nginx on a Linux server.

A simplified request flow looks like this:

1. A client sends an HTTP request over the network.
2. The server's networking stack processes the incoming traffic.
3. Nginx receives the request through its network socket.
4. Nginx processes the request and may communicate with an application backend.
5. The application may request filesystem, memory, or networking services.
6. The kernel coordinates the required resources and operations.
7. The response travels back to the client.

The kernel does not interpret the application's business logic. Nginx and the application handle their respective tasks, while Linux provides the operating-system services they need.

### How this helps during troubleshooting

If users cannot reach the application, I would investigate the relevant layers:

- Is the process running?
- Is the expected port listening?
- Is the application returning errors?
- Is the server under resource pressure?
- Are firewall rules or cloud security groups blocking traffic?
- Do service logs show configuration or startup errors?

This layered approach helps narrow down the cause instead of guessing.

---

## 10. Practical Commands for Exploring Linux Architecture

Run these commands in a Linux lab or another authorized environment.

### Inspect the operating system

```bash
uname -r
cat /etc/os-release
```

These show the running kernel version and distribution information.

### Inspect CPU and memory

```bash
lscpu
free -h
```

These provide information about the CPU and memory.

### Inspect processes

```bash
ps aux
top
```

Use these commands to observe running processes and resource usage.

### Inspect filesystems

```bash
lsblk
df -h
```

These help you inspect block devices and mounted filesystem space.

### Inspect network interfaces and listening sockets

```bash
ip addr
ss -lnt
```

These show network-interface information and listening TCP sockets.

### Trace system calls

On a Linux lab where `strace` is installed, try:

```bash
strace -e trace=openat,read,write ls
```

`strace` helps reveal system calls made by the command and the results returned by the kernel.

The exact output varies by distribution, environment, and program implementation. You may see many system calls beyond the three listed, depending on the command and its startup behavior.

---

## 11. Interview Questions and Answers

### Q1. What is Linux architecture?

Linux architecture describes how hardware, the kernel, system libraries, system utilities, and user applications interact to run programs and manage resources.

### Q2. What is the role of the Linux kernel?

The kernel manages CPU scheduling, memory, processes, filesystems, device access, networking, and important security controls.

### Q3. What is the difference between user space and kernel space?

User space is where ordinary applications and utilities execute with restricted privileges. Kernel space is where the kernel performs privileged operating-system operations.

### Q4. What is a system call?

A system call allows a user-space program to request a service from the kernel, such as reading a file or creating a socket.

### Q5. What is the difference between a system call and a system library?

A system call provides an interface to a kernel service. A system library provides reusable functions that may use system calls to perform operating-system operations.

### Q6. What happens when you run `ls -l`?

The shell interprets the command and launches the `ls` program. The program obtains directory information through operating-system interfaces, formats the result, and writes it to standard output for display in the terminal.

### Q7. Why is Linux architecture important for DevOps?

It helps engineers understand how applications use system resources and how to troubleshoot failures involving processes, permissions, filesystems, networking, and resource limits.

### Q8. How would you investigate an application that cannot access a file?

I would verify the file path, ownership, permissions, directory access, and identity under which the application runs. I would then check application logs and relevant system information before applying a fix.

---

## 12. Summary

Linux architecture is built around the interaction between hardware, the kernel, and user-space programs.

The kernel manages system resources and provides essential services. System calls provide access to those services, libraries offer reusable functions, and shells and utilities let users administer the system.

For a DevOps engineer, understanding these interactions makes troubleshooting more systematic and helps explain what happens behind the scenes when applications run on Linux servers.

