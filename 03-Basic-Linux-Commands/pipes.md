# Linux Pipes (`|`)

## 1. What is a pipe?

A pipe in Linux connects the standard output of one command to the standard input of another command.

The pipe symbol is:

```bash
|
```

Instead of handling the output of one command manually, you can pass it directly to another command for filtering, sorting, counting, or further processing.

---

## 2. Why do we use pipes?

Pipes help Linux users combine small commands to perform more useful tasks.

Common uses include:

- Filtering application logs.
- Searching for running processes.
- Sorting command output.
- Counting matching lines.
- Investigating system issues.
- Building shell scripts for automation.

---

## 3. Basic syntax

```bash
command1 | command2
```

The output produced by `command1` becomes the input for `command2`, when that command reads from standard input.


---

## 4. Practical examples

### Example 1: Search logs for errors

```bash
cat app.log | grep "ERROR"
```

This displays lines containing `ERROR`.

A simpler and generally preferred version is:

```bash
grep "ERROR" app.log
```

Use a pipe when the output of one command needs to be processed by another command.

### Example 2: Find a running process

```bash
ps aux | grep nginx
```

`ps aux` lists processes, and `grep nginx` filters the output.

Note that this may also show the `grep` command itself. For service troubleshooting, consider using `pgrep -a nginx` or checking the service with `systemctl status nginx`.

### Example 3: Count error messages

```bash
grep "ERROR" app.log | wc -l
```

`grep` selects matching lines, and `wc -l` counts them.

### Example 4: Sort processes by CPU usage

```bash
ps aux | sort -k 3 -nr | head -n 5
```

This sorts process output by the third column in descending numerical order and displays the first five lines. The header may be included.

### Example 5: Read long output page by page

```bash
ps aux | less
```

This is useful when command output is too long to fit on the terminal screen.

---

## 5. Connecting multiple commands

A pipeline can contain more than two commands:

```bash
cat app.log | grep "ERROR" | sort | uniq -c
```

This filters error lines, sorts them, and counts repeated identical lines.

For searching and counting, a shorter alternative may be:

```bash
grep "ERROR" app.log | sort | uniq -c
```

The best pipeline depends on the format of the input and the task.

---

## 6. What happens behind the scenes?

Linux commands commonly use three standard streams:

- **Standard input (stdin):** file descriptor `0`
- **Standard output (stdout):** file descriptor `1`
- **Standard error (stderr):** file descriptor `2`

A pipe connects the stdout of one process to the stdin of the next. Linux coordinates the processes so data can flow between them without requiring an intermediate file.

By default, a normal pipe carries standard output, not standard error.

For example:

```bash
find / -name "*.log" 2>/dev/null | grep "app"
```

Here, `2>/dev/null` discards error messages from `find`, while the pipe passes its standard output to `grep`.

---

## 7. DevOps use cases

### Application troubleshooting

```bash
grep "ERROR" /var/log/myapp/app.log | tail -n 20
```

Find error lines and display the last 20 matching lines.

### Process investigation

```bash
ps aux | grep nginx
```

Quickly filter process listings when investigating a service.

### Disk usage investigation

```bash
du -ah /var/log | sort -h | tail -n 10
```

Identify large files or directories under `/var/log`. Access permissions and directory size can affect the results.

---

## 8. Common mistakes

- Confusing a pipe (`|`) with output redirection (`>`).
- Assuming a pipe automatically saves output to a file.
- Forgetting that standard error is not passed through an ordinary pipe by default.
- Building a long pipeline without checking the output of each command.
- Using `cat file | grep pattern` when `grep pattern file` is simpler.

---

## 9. Interview questions

**Q1. What is a pipe in Linux?**

A pipe passes one command's standard output to another command's standard input.

**Q2. What is the difference between a pipe and redirection?**

A pipe connects commands. Redirection sends input or output to or from a file or another destination.

**Q3. Does a pipe pass standard error by default?**

No. A normal pipe passes standard output. Standard error must be redirected separately if needed.

**Q4. Can we use multiple pipes in one command?**

Yes. Multiple pipes form a pipeline in which each command processes data from the previous stage.

**Q5. Give a DevOps example of a pipe.**

```bash
grep "ERROR" app.log | wc -l
```

This counts matching error lines in an application log.

---

## 10. Key takeaway

Pipes let you combine Linux commands into practical workflows. Learn to filter, sort, count, and inspect command output before using longer pipelines in production.

Useful references: `man bash`, `man grep`, `man sort`, and `man wc`.