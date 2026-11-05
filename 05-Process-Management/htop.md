# Linux `htop` Command

## 1. What Is `htop`?

`htop` is an interactive, terminal-based process-monitoring tool for Linux. It displays running processes, CPU usage, memory consumption, swap usage, and other system information in a continuously updating interface.

Compared with `top`, `htop` provides a more visual layout and convenient interactive controls for navigating and filtering processes.

It is commonly used by system administrators and DevOps engineers to investigate resource usage and troubleshoot Linux servers.

**Note:** `htop` may not be installed by default on every Linux distribution.

---

## 2. Install `htop`

### Ubuntu and Debian

```bash
sudo apt update
sudo apt install htop
```

### Fedora

```bash
sudo dnf install htop
```

### RHEL-compatible distributions

Depending on the distribution and enabled repositories, use:

```bash
sudo dnf install htop
```

If the package is unavailable, check which repositories are enabled and whether the package is supported by your distribution.

---

## 3. Start `htop`

Run:

```bash
htop
```

The interface normally contains three important areas:

1. **System summary:** CPU, memory, swap, and other system information.
2. **Process list:** Process IDs, users, resource usage, and command names.
3. **Interactive controls:** Keyboard shortcuts or function-key actions for managing the display and selected processes.

The exact appearance depends on the installed version, terminal, and configuration.

---

## 4. Understand the Display

### CPU usage

`htop` can show CPU activity for individual logical CPUs.

This helps you investigate whether work is concentrated on particular CPUs or whether CPU activity is high across the system.

Remember that a process's CPU usage depends on the monitoring tool's reporting convention and system configuration.

### Memory and swap

The memory indicators show how much memory is used and available according to the tool's reporting method.

Linux also uses RAM for filesystem caches. Therefore, memory that is not listed as completely free is not necessarily unavailable to applications.

Check available memory, swap activity, and signs of memory pressure before concluding that the system is running out of RAM.

### Process list

Common columns include:

| Column | Meaning |
|---|---|
| `PID` | Process ID |
| `USER` | User associated with the process |
| `PRI` | Scheduling priority |
| `NI` | Nice value |
| `VIRT` | Virtual memory size |
| `RES` | Resident memory |
| `SHR` | Shared memory estimate |
| `S` | Process state |
| `CPU%` | Reported CPU usage |
| `MEM%` | Reported memory usage |
| `Command` | Command associated with the process |

The displayed columns may differ by version and configuration.

---

## 5. Useful `htop` Shortcuts

The following are common shortcuts in standard interactive `htop` configurations. Exact keys may vary with version and configuration.

| Key | Purpose |
|---|---|
| `F1` | Open help |
| `F2` | Open setup and display options |
| `F3` | Search for a process |
| `F4` | Filter the process list |
| `F5` | Toggle tree view |
| `F6` | Choose a sorting column |
| `F9` | Open the signal menu for a selected process |
| `F10` | Quit |

Some terminals intercept function keys. If a shortcut doesn't work, use the on-screen controls or help menu.

### Search for a process

Press `F3` and enter a process name, such as `nginx`.

This helps you locate a process without manually scanning the entire list.

### Filter processes

Press `F4` and enter a filter term.

Filtering helps narrow the display to processes matching the specified text.

### View process relationships

Press `F5` to switch to tree view when supported by your configuration.

The tree makes parent-child process relationships easier to inspect.

### Sort by resource usage

Use `F6` to choose a sorting column. Select CPU or memory usage to bring relevant processes into view.

### Send a signal

`F9` opens the signal menu for the selected process.

Be careful when using this option. Confirm the process identity, understand the signal's effect, and make sure you have permission to perform the action.

---

## 6. `htop` vs `top`

| Feature | `top` | `htop` |
|---|---|---|
| Live process monitoring | Yes | Yes |
| Visual CPU and memory indicators | Basic text-oriented display | More visual display |
| Interactive sorting | Yes | Yes |
| Search and filtering | Available, with controls depending on version | Convenient interactive controls |
| Tree view | Available in supported versions | Commonly available |
| Installation | Commonly preinstalled | May require installation |
| Scripting | Supports batch mode | Primarily an interactive tool |

Neither tool is universally better. `top` is widely available on Linux servers, while `htop` can be more convenient for interactive troubleshooting.

---

## 7. DevOps Example

Imagine that a Linux server hosting a web application has become slow.

### Step 1: Start the monitor

```bash
htop
```

### Step 2: Inspect CPU usage

Look at the CPU indicators and identify processes consuming significant CPU resources.

### Step 3: Check memory

Inspect the memory and swap indicators. Look for signs of memory pressure rather than relying only on the amount of free RAM.

### Step 4: Find the application

Use the search or filter controls to locate the application process.

### Step 5: Investigate further

Record its PID and inspect it with:

```bash
ps -p 1234 -o pid,ppid,stat,%cpu,%mem,etime,cmd
```

Replace `1234` with the actual PID.

Then check application logs, recent deployments, and other system metrics.

**Important:** A process using substantial resources may be performing legitimate work. Investigate the cause before taking action. For a systemd-managed service, use `systemctl` to inspect and manage the service when appropriate.

---

## 8. Why `htop` Matters in DevOps

`htop` is useful for:

- Investigating high CPU usage.
- Identifying processes consuming significant memory.
- Monitoring swap activity.
- Searching for specific processes.
- Understanding parent-child process relationships.
- Performing initial troubleshooting on Linux servers and virtual machines.

For production monitoring, use `htop` alongside persistent metrics, logs, and alerting systems. Its interactive display is helpful for immediate investigation but is not a replacement for historical monitoring.

---

## 9. Interview Questions

**Q1. What is `htop`?**

An interactive Linux utility that displays processes and system resource usage in real time.

**Q2. What is the difference between `top` and `htop`?**

Both provide live monitoring. `htop` generally offers a more visual interface and convenient interactive search, filtering, and process navigation.

**Q3. Is `htop` installed by default on every Linux distribution?**

No. It may need to be installed using the distribution's package manager.

**Q4. How do you search for a process in `htop`?**

Press `F3` in a standard configuration and enter the process name.

**Q5. How do you display processes as a tree?**

Press `F5` in a standard configuration to toggle tree view.

**Q6. Can you terminate a process from `htop`?**

Yes. Select the process and use the signal menu, commonly opened with `F9`. Confirm the correct target and choose the appropriate signal.

**Q7. Does high memory usage always indicate a problem?**

No. Linux uses RAM for caching, and an application may legitimately need substantial memory. Investigate available memory, swap activity, and actual performance symptoms.

**Q8. When would you use `htop` instead of `ps`?**

Use `htop` for interactive, continuously updating monitoring. Use `ps` when you need a snapshot, selected process details, or output for scripts.

---

## Key Takeaway

`htop` makes it easier to explore processes and resource usage interactively. For DevOps troubleshooting, use it to identify where to investigate, then confirm the cause with process details, logs, and system metrics.