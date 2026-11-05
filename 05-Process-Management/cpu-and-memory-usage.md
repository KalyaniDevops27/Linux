# CPU and Memory Usage in Linux

## 1. Introduction

CPU and memory are two important resources used by every running Linux system.

When applications run on a server, they consume CPU processing time and memory (RAM). Monitoring these resources helps DevOps engineers identify performance problems, investigate application behavior, and understand server capacity.

For example, if an application becomes slow, we may need to investigate:

- Is the CPU heavily utilized?
- Is the application consuming too much memory?
- Is the system experiencing memory pressure?
- Is the server using swap?
- Is a particular process consuming more resources than expected?

Understanding these metrics is an important part of Linux administration and production troubleshooting.

---

## 2. What is CPU Usage?

CPU usage represents the amount of CPU processing time being consumed during a particular interval.

The CPU executes instructions from running processes and the operating system.

Examples of CPU-intensive activities include:

- Processing a large number of application requests.
- Compressing or extracting large files.
- Running database queries.
- Executing build and compilation tasks.
- Performing data processing operations.

When CPU demand is consistently high, applications may experience increased response times.

### Check CPU usage

```bash
top
```

The `top` command provides a live view of CPU utilization and running processes.

You can also inspect CPU information:

```bash
lscpu
```

This displays details such as CPU architecture, logical processors, and CPU capabilities.

### Inspect CPU accounting information

```bash
cat /proc/stat
```

Linux exposes accumulated CPU time counters through `/proc/stat`.

Important CPU categories include:

| Category | Meaning |
|---|---|
| user | Time spent executing normal user-space processes |
| nice | Time spent executing processes with adjusted scheduling priority |
| system | Time spent executing kernel code |
| idle | CPU idle time |
| iowait | Idle time while I/O is outstanding |
| irq | Time spent handling hardware interrupts |
| softirq | Time spent handling software interrupts |

Monitoring tools compare successive counter readings to estimate CPU utilization over a time interval.

---

## 3. What is Memory Usage?

Memory usage represents the RAM currently used by applications, the kernel, and other system activities.

RAM is much faster than disk storage, so running applications keep frequently needed code and data in memory.

Examples include:

- Application processes.
- Database caches.
- Web server processes.
- Operating system memory.
- Filesystem caches.

### Check system memory

```bash
free -h
```

The `-h` option displays values in human-readable units.

Example output:

```text
               total        used        free      shared  buff/cache   available
Mem:            15Gi        8Gi        1Gi        500Mi       6Gi         6Gi
Swap:          2.0Gi        0B         2.0Gi
```

Important columns:

| Column | Meaning |
|---|---|
| total | Total physical memory |
| used | Memory currently accounted as used |
| free | Completely unused memory |
| shared | Memory used by shared-memory mechanisms |
| buff/cache | Memory used by buffers and caches |
| available | Estimate of memory available for new applications without swapping |

**Important:** Low free memory does not automatically indicate a memory problem. Linux uses otherwise-unused RAM for caching. The `available` value is generally more useful when assessing memory capacity.

---

## 4. Under the Hood: How Linux Tracks CPU and Memory

### A. CPU accounting

The Linux kernel maintains CPU accounting counters.

These are exposed through:

```bash
cat /proc/stat
```

Monitoring tools read the counters at different times and calculate the differences.

For example, if a monitoring tool reads the counters at two different points, it can estimate how much time was spent executing processes and how much time the CPU was idle during that interval.

This is how tools such as `top` calculate CPU utilization.

### B. Process memory accounting

Linux manages physical memory in units called pages.

A process can have virtual memory mappings for executable code, libraries, stack, heap, and other memory regions.

Inspect a process's memory information:

```bash
cat /proc/<PID>/status
```

Important fields include:

| Field | Meaning |
|---|---|
| VmSize | Total virtual memory size |
| VmRSS | Resident memory currently in physical RAM |
| VmSwap | Memory associated with the process that has been swapped out |

Virtual memory size is not the same as physical RAM consumption. A process may have a large virtual address space without all of it occupying physical memory.

### C. Filesystem cache

Linux uses RAM to cache filesystem data.

For example, when an application reads a file, the kernel may keep the file's contents in memory so that subsequent reads can be faster.

This cached memory can be reclaimed when applications need more RAM.

That is why high memory usage does not automatically mean the system is running out of memory.

### D. Swap memory

Swap provides additional virtual-memory capacity by using disk storage as backing for memory pages.

Check swap information:

```bash
swapon --show
```

Check memory and swap statistics:

```bash
vmstat 1
```

Swap is significantly slower than RAM. Sustained heavy swapping can affect application performance.

### E. Out-Of-Memory (OOM) killer

When the system cannot satisfy memory demands after reclaiming available memory, Linux may invoke the OOM killer.

The kernel selects a process to terminate to protect the system from an unrecoverable memory shortage.

This can cause application failures or unexpected restarts.

---

## 5. Commands to Monitor CPU and Memory

### View live resource usage

```bash
top
```

Displays CPU usage, memory usage, and running processes.

### View interactive resource usage

```bash
htop
```

Provides an interactive process-monitoring interface.

### View memory information

```bash
free -h
```

Displays system memory and swap usage.

### Find processes consuming the most CPU

```bash
ps aux --sort=-%cpu
```

Sorts processes by CPU usage in descending order.

### Find processes consuming the most memory

```bash
ps aux --sort=-%mem
```

Sorts processes by memory usage in descending order.

### Monitor system statistics continuously

```bash
vmstat 1
```

Displays system statistics at one-second intervals.

### Inspect detailed memory information

```bash
cat /proc/meminfo
```

Displays detailed kernel memory information.

---

## 6. Practical DevOps Troubleshooting Example

Imagine an application running on a Linux server becomes slow.

As a DevOps engineer, we should not immediately assume that the application itself is the problem.

We first inspect the server.

### Step 1: Check overall CPU and memory usage

```bash
top
```

Look for processes consuming unusually high CPU or memory.

### Step 2: Identify CPU-intensive processes

```bash
ps aux --sort=-%cpu
```

This helps identify processes using the most CPU at the time the command runs.

### Step 3: Identify memory-intensive processes

```bash
ps aux --sort=-%mem
```

This helps identify processes with the highest memory usage.

### Step 4: Check available memory

```bash
free -h
```

Look at available memory and swap usage rather than relying only on the free-memory column.

### Step 5: Check memory and swap activity

```bash
vmstat 1
```

Observe memory pressure and swap activity over time.

### Step 6: Investigate further

Once a resource-consuming process is identified, inspect its logs, configuration, workload, and application behavior.

Do not assume that high CPU or memory usage is automatically a bug. Some workloads naturally require significant resources.

---

## 7. CPU Usage vs Memory Usage

| Feature | CPU | Memory |
|---|---|---|
| Full form | Central Processing Unit | Random Access Memory |
| Main purpose | Executes instructions | Stores data needed by running processes |
| Resource measurement | CPU time and utilization | Bytes or pages used |
| Common issue | High CPU utilization | Memory pressure or exhaustion |
| Useful commands | `top`, `ps`, `vmstat` | `free`, `top`, `vmstat` |

---

## 8. Important Points for DevOps Engineers

- CPU usage should be observed over time, not only from one snapshot.
- High CPU usage is not always a problem; understand the workload.
- Low free RAM does not automatically mean memory exhaustion.
- The `available` field in `free -h` helps estimate memory available to applications.
- Virtual memory size and resident memory are different.
- Heavy swap activity can affect application performance.
- OOM killer events can explain unexpected process termination.
- Always correlate resource metrics with application logs and workload patterns.

---

## 9. Interview Questions

**Q1. What is CPU utilization?**

CPU utilization represents the proportion of CPU processing time being used during a measurement interval.

**Q2. What is memory usage in Linux?**

It represents RAM used by processes, the kernel, and other system activities.

**Q3. How do you check memory usage in Linux?**

```bash
free -h
```

**Q4. How do you identify processes consuming the most CPU?**

```bash
ps aux --sort=-%cpu
```

**Q5. How do you identify processes consuming the most memory?**

```bash
ps aux --sort=-%mem
```

**Q6. What is swap memory?**

Swap uses disk storage as backing for memory pages when RAM is under pressure.

**Q7. What is the OOM killer?**

It is a Linux kernel mechanism that may terminate selected processes when the system cannot satisfy memory demands.

**Q8. Why is high memory usage not always a problem in Linux?**

Because Linux uses available RAM for filesystem caching and other reclaimable purposes. The memory can be reclaimed when applications need it.

**Q9. What is the difference between VmSize and VmRSS?**

`VmSize` represents a process's virtual memory size, while `VmRSS` represents its resident memory in physical RAM.

**Q10. Which commands are useful for resource monitoring?**

Common commands include `top`, `htop`, `free`, `ps`, `vmstat`, and `/proc` inspection.

---

## Conclusion

Understanding CPU and memory usage helps DevOps engineers investigate slow applications, resource bottlenecks, and server performance issues.

The goal is not just to identify a process consuming resources. It is to understand why the resource is being consumed and whether that behavior is expected for the workload.