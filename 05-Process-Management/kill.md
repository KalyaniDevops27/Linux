# kill Command in Linux

## 1. What is the `kill` command?

The `kill` command is used to send signals to processes in Linux.

These signals allow us to request process termination, interrupt execution, stop a process, or resume a stopped process.

Despite its name, `kill` does not always terminate a process. Its actual behavior depends on the signal being sent.

**Syntax:**

```bash
kill [options] <PID>
```

To specify a signal:

```bash
kill -SIGNAL <PID>
```

Example:

```bash
kill -TERM 1450
```

This sends `SIGTERM` to the process with PID `1450`.

---

## 2. Why do we use `kill`?

In Linux administration and DevOps, processes sometimes need to be stopped or interrupted.

For example:

- An application needs to shut down.
- A test process needs to be stopped.
- A process is not responding as expected.
- A process needs to be paused and resumed.
- A developer needs to troubleshoot process behavior.

The `kill` command provides a standard way to send signals to processes.

---

## 3. Common Linux signals

| Signal | Number on common Linux architectures | Purpose |
|---|---:|---|
| `SIGTERM` | 15 | Requests graceful termination |
| `SIGKILL` | 9 | Forces process termination |
| `SIGINT` | 2 | Interrupts a process |
| `SIGSTOP` | 19 | Stops a process |
| `SIGCONT` | 18 | Resumes a stopped process |
| `SIGHUP` | 1 | Traditionally indicates terminal hangup; some applications use it to reload configuration |

Signal numbers can vary across architectures and operating systems. Using signal names is generally clearer and more portable.

---

## 4. Common `kill` commands

### Send SIGTERM

```bash
kill -TERM 1450
```

Requests the process to terminate gracefully.

The process may handle the signal, perform cleanup, and exit.

### Send SIGKILL

```bash
kill -KILL 1450
```

Forces the process to terminate.

The process cannot catch, block, or ignore `SIGKILL`.

Use this only when a normal termination request is insufficient.

### Send SIGINT

```bash
kill -INT 1450
```

Sends an interrupt signal to the process.

This is similar to the signal commonly generated when pressing `Ctrl+C` in a terminal.

### Stop a process

```bash
kill -STOP 1450
```

Suspends the process. It cannot catch or ignore `SIGSTOP`.

### Resume a stopped process

```bash
kill -CONT 1450
```

Resumes a process that was stopped.

### List available signal names

```bash
kill -l
```

Displays the signal names recognized by the shell's `kill` command.

---

## 5. Under the Hood: How `kill` Works

When we execute:

```bash
kill -TERM 1450
```

Linux performs several operations.

### Step 1: Shell processes the command

The shell interprets the command and identifies the requested signal and target PID.

Many shells provide `kill` as a built-in command.

### Step 2: The signal request reaches the kernel

The shell uses a process-signaling system call, commonly `kill(2)`, to request signal delivery.

The kernel checks whether the sender has permission to signal the target process.

### Step 3: The kernel records the pending signal

The kernel records the signal for the target process. Signal delivery takes place when the process is in a position to handle it.

### Step 4: The process handles the signal

For a catchable signal such as `SIGTERM`, the process may execute a registered signal handler or follow the signal's default action.

The application can perform tasks such as:

- Stop accepting new work.
- Finish or reject ongoing requests.
- Close files and connections.
- Release application-level resources.

The exact behavior depends on how the application is implemented.

### Step 5: The process exits

After the process exits, the kernel releases its resources. The parent process can collect its exit status.

**Important:** `SIGKILL` and `SIGSTOP` cannot be caught, blocked, or ignored. A process cannot perform its own signal-handler cleanup when it receives `SIGKILL`.

---

## 6. Practical Example: Test with a Safe Process

We can understand process signaling using a temporary `sleep` process rather than experimenting with important system services.

### Step 1: Start a temporary process

```bash
sleep 300
```

This command runs for 300 seconds unless interrupted.

Keep it running in the terminal.

### Step 2: Open another terminal

Find the process:

```bash
pgrep -a sleep
```

Example output:

```text
1450 sleep 300
```

The PID in this example is `1450`. Your actual PID will be different.

### Step 3: Request graceful termination

```bash
kill -TERM 1450
```

The `sleep` process should terminate.

### Step 4: Verify the process

```bash
pgrep -a sleep
```

If no matching process appears, it is no longer visible as a running matching process.

---

## 7. SIGTERM vs SIGKILL

| Feature | SIGTERM | SIGKILL |
|---|---|---|
| Purpose | Graceful termination request | Forced termination |
| Can be caught? | Yes | No |
| Can be ignored? | Yes | No |
| Application cleanup handler | Can run | Cannot run |
| Recommended first choice? | Yes | No, last resort |

In production environments, prefer graceful shutdown because applications may need time to finish work and release resources.

---

## 8. Important Considerations for DevOps Engineers

- Always identify the correct process before sending a signal.
- Prefer `SIGTERM` before considering `SIGKILL`.
- Forced termination may interrupt application work.
- A successful signal request does not necessarily mean the process has already exited.
- Process IDs can be reused after a process exits. Avoid relying on an old PID for later operations.
- For services managed by systemd, prefer `systemctl stop <service>` rather than manually signaling the service process.

---

## 9. Interview Questions

**Q1. What is the purpose of the `kill` command?**

It sends signals to Linux processes using their process IDs.

**Q2. Does `kill` always terminate a process?**

No. It sends a signal. The process behavior depends on the signal received.

**Q3. What is the difference between SIGTERM and SIGKILL?**

`SIGTERM` requests graceful termination and can be handled by the application. `SIGKILL` forces termination and cannot be caught or ignored.

**Q4. Which signal does `kill <PID>` send by default?**

It sends `SIGTERM` by default.

**Q5. Can a process ignore SIGKILL?**

No. `SIGKILL` cannot be caught, blocked, or ignored.

**Q6. How do you resume a stopped process?**

```bash
kill -CONT <PID>
```

**Q7. Why should we avoid using SIGKILL as the first option?**

Because the application does not get an opportunity to perform its own graceful shutdown and cleanup.

**Q8. What should you use to stop a systemd-managed service?**

Use:

```bash
sudo systemctl stop <service-name>
```

This allows systemd to manage the service's shutdown process.

---

## Conclusion

The `kill` command is an important part of Linux process management. Understanding signals helps DevOps engineers control processes safely and troubleshoot applications without unnecessarily interrupting running workloads.