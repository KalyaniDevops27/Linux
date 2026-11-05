# Linux `top` Command

## 1. What Is the `top` Command?

The `top` command is a Linux utility used to monitor system performance and running processes in real time.

It provides information about CPU usage, memory consumption, load averages, system uptime, and individual processes.

Unlike `ps`, which normally displays a snapshot, `top` refreshes its display periodically while it runs.

As a DevOps engineer, `top` is useful when investigating slow applications, high CPU usage, memory pressure, and unexpected server load.

---

## 2. Basic Syntax

```bash
top
```

Run the command in a Linux terminal. Press `q` to exit.

The display has two main sections:

1. **System summary:** Overall system activity and resource usage.
2. **Process list:** Details about individual processes.

---

## 3. Understanding the System Summary

A typical `top` display contains information similar to this:

```text
top - 10:30:15 up 5 days,  2:14,  2 users,  load average: 0.45, 0.62, 0.71
Tasks: 180 total, 2 running, 178 sleeping, 0 stopped, 0 zombie
%Cpu(s): 12.0 us, 4.0 sy, 0.0 ni, 80.0 id, 3.0 wa, 0.0 hi, 1.0 si, 0.0 st
MiB Mem :  7980.0 total,  1200.0 free,  4200.0 used,  2580.0 buff/cache
MiB Swap:  2048.0 total,  2000.0 free,    48.0 used
```

### A. Uptime and Load Average

The first line displays how long the system has been running and the load averages for the last 1, 5, and 15 minutes.

Load average represents the average number of tasks running or waiting for CPU time, along with tasks in uninterruptible sleep.

Interpret it relative to the system's CPU capacity and workload. A load average of 4 can mean different things on a 2-CPU system and a 16-CPU system.

### B. Tasks

The task summary shows how many processes or threads are in different states, depending on the display configuration.

Common categories include:

- Running
- Sleeping
- Stopped
- Zombie

A large number of sleeping processes is not automatically a problem. Many servers spend much of their time waiting for work.

### C. CPU Usage

The CPU summary includes several categories:

| Field | Meaning |
|---|---|
| `us` | CPU time spent running user-space code |
| `sy` | CPU time spent in kernel code |
| `ni` | CPU time spent on niced user-space processes |
| `id` | Idle CPU time |
| `wa` | CPU time waiting for I/O completion |
| `hi` | Time handling hardware interrupts |
| `si` | Time handling software interrupts |
| `st` | CPU time taken by the hypervisor from a virtual machine |

For example, sustained high `us` may indicate CPU-intensive application work. High `sy` may warrant investigation into kernel activity, system calls, or other operating-system work.

High `wa` can indicate I/O delays. It is not a direct measurement of disk utilization, so investigate storage metrics as well.

### D. Memory Usage

The memory summary typically displays total, free, used, and buffer/cache memory.

Linux uses RAM for applications and filesystem caching.

**Do not judge memory pressure from the `free` value alone.** Check available memory and swap activity, and look for signs of out-of-memory conditions or application performance problems.

---

## 4. Understanding the Process List

A typical process list contains columns such as:

| Column | Meaning |
|---|---|
| `PID` | Process ID |
| `USER` | User associated with the process |
| `PR` | Scheduling priority displayed by `top` |
| `NI` | Nice value |
| `VIRT` | Total virtual memory size |
| `RES` | Resident memory in physical RAM |
| `SHR` | Shared memory reported for the process |
| `S` | Process state |
| `%CPU` | Reported CPU usage |
| `%MEM` | Percentage of physical memory represented by the process's resident memory |
| `TIME+` | Accumulated CPU time |
| `COMMAND` | Command or program name |

The precise display and calculations can vary by system and `top` implementation.

### VIRT vs RES

- **VIRT:** The total virtual address space associated with a process. It is not the same as physical RAM usage.
- **RES:** The amount of physical memory currently resident for that process, including memory that may be shared.
- **SHR:** An estimate of memory that may be shared with other processes.

Do not assume that all virtual memory is physically resident in RAM.

---

## 5. Useful `top` Keyboard Shortcuts

These shortcuts work in the interactive display of standard Linux `top` implementations.

### Sort by CPU usage

Press:

```text
P
```

This sorts the process list by CPU usage.

### Sort by memory usage

Press:

```text
M
```

This sorts the list by memory usage.

### Show individual CPU information

Press:

```text
1
```

On supported displays, this toggles the view of individual logical CPUs.

### Change the refresh delay

Press:

```text
d
```

Then enter the desired delay in seconds, if prompted.

### Filter processes by user

Press:

```text
u
```

Then enter the username when prompted.

### Send a signal to a process

Press:

```text
k
```

`top` prompts for a PID and signal. Verify the target and understand the effect before confirming. On production servers, prefer a deliberate, documented action over experimenting with this shortcut.

### Exit

Press:

```text
q
```

---

## 6. Run `top` in Batch Mode

Batch mode is useful when you want to capture process information without the interactive display.

```bash
top -b -n 1
```

This runs one batch iteration and prints the output.

To capture several iterations:

```bash
top -b -n 3
```

You can redirect the output to a file:

```bash
top -b -n 3 > top-output.txt
```

This is useful for basic diagnostics and scripting, although dedicated monitoring tools are better for long-term metrics and alerting.

---

## 7. Practical DevOps Example: Investigating High CPU Usage

Imagine a Linux server hosting an application. Users report slow responses.

### Step 1: Open the live process monitor

```bash
top
```

### Step 2: Sort by CPU

Press `P` to bring CPU-intensive processes to the top.

### Step 3: Identify the process

Note its PID, command, and user. Confirm that you have identified the correct application.

### Step 4: Inspect it further

```bash
ps -p 1234 -o pid,ppid,stat,%cpu,%mem,etime,cmd
```

Replace `1234` with the actual PID.

### Step 5: Investigate the cause

Check application logs, workload changes, recent deployments, and relevant system metrics.

A high CPU reading may be caused by legitimate traffic, inefficient application code, a background job, or a problem such as an unexpected retry loop.

**Do not terminate a process just because it appears at the top of the CPU list.** Determine whether it is responsible for the issue and choose the appropriate corrective action.

---

## 8. `ps` vs `top` vs `htop`

| Feature | `ps` | `top` | `htop` |
|---|---|---|---|
| Main purpose | Process snapshots | Live monitoring | Interactive live monitoring |
| Refreshes automatically | Normally no | Yes | Yes |
| Interactive sorting | Limited | Yes | Yes |
| Scripting | Very useful | Supports batch mode | Primarily interactive |
| Availability | Commonly installed | Commonly installed | May need installation |

---

## 9. Why `top` Matters in DevOps

The `top` command is useful for:

- Investigating CPU-intensive processes.
- Identifying processes with high resident memory usage.
- Checking overall system load.
- Observing process states.
- Troubleshooting slow Linux servers.
- Gathering initial information before deeper application or infrastructure investigation.

In production environments, combine `top` with application logs, system metrics, and monitoring tools. One screen is a useful starting point, not the complete diagnosis.

---

## 10. Interview Questions

**Q1. What is the `top` command?**

It is a Linux utility that displays a periodically refreshed view of system resource usage and running processes.

**Q2. What is the difference between `ps` and `top`?**

`ps` normally provides a snapshot, whereas `top` continuously refreshes its display.

**Q3. How do you sort processes by CPU usage in `top`?**``

Press `P`.

**Q4. How do you sort processes by memory usage?**

Press `M`.

**Q5. What is the difference between VIRT and RES?**

VIRT represents a process's virtual address space, while RES represents its resident memory in physical RAM.

**Q6. What does load average mean?**

It represents the average number of tasks running or waiting for CPU time, together with tasks in uninterruptible sleep, over 1-, 5-, and 15-minute periods.

**Q7. Does high load average always mean high CPU utilization?**

No. Load average can include tasks waiting in uninterruptible sleep, often associated with I/O. Compare it with CPU utilization, CPU count, and I/O metrics.

**Q8. How would you troubleshoot a high-CPU process?**

Use `top` to identify it, inspect it with `ps`, and investigate application logs, workload, and relevant system metrics before taking corrective action.

---

## Key Takeaway

`top` gives you a live view of Linux system activity. Learn to read CPU, memory, load average, and process information together so you can investigate performance issues instead of guessing.
