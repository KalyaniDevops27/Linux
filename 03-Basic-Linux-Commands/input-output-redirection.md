# Linux Input/Output Redirection

## 1. Introduction

Linux commands normally read input from standard input and send their results to standard output. Error messages are usually sent to standard error.

Input/output redirection lets us change these default destinations.

Instead of displaying everything on the terminal, we can save results to files, append information to logs, read input from files, or capture errors separately.

---

## 2. Standard streams

Linux uses three standard streams:

| Stream | File descriptor | Purpose |
|---|---:|---|
| Standard input (`stdin`) | `0` | Input received by a command |
| Standard output (`stdout`) | `1` | Normal output |
| Standard error (`stderr`) | `2` | Error messages |

Understanding these streams makes redirection easier to troubleshoot.

---

## 3. Output redirection (`>`)

The `>` operator redirects standard output to a file.

```bash
ls -l > files.txt
```

This creates `files.txt` if it does not exist. If the file already exists, its previous contents are overwritten.

Another example:

```bash
date > current-date.txt
```

This saves the command's output to `current-date.txt` instead of displaying it on the terminal.

**Important:** Redirection can overwrite existing data. Check the destination filename before using `>`.

---

## 4. Append output (`>>`)

The `>>` operator adds standard output to the end of a file.

```bash
date >> activity.log
```

Run this multiple times and each result will be appended as a new line of output.

Unlike `>`, this operator does not replace the existing contents.

### DevOps example

Record a simple maintenance activity:

```bash
echo "Maintenance check completed" >> maintenance.log
```

For production automation, include timestamps and appropriate logging practices so events can be traced reliably.

---

## 5. Input redirection (`<`)

The `<` operator makes a command read its standard input from a file.

```bash
sort < names.txt
```

Here, `sort` reads the contents of `names.txt` and sorts the lines.

Another example:

```bash
wc -l < names.txt
```

This counts the lines read from the file. Unlike `wc -l names.txt`, the redirected form generally prints the count without the filename.

---

## 6. Redirect standard error (`2>`)

By default, error messages usually appear on the terminal.

```bash
ls /missing-path
```

If the path does not exist, the command prints an error.

Save the error instead:

```bash
ls /missing-path 2> errors.log
```

The number `2` represents standard error.

---

## 7. Redirect standard output and standard error

Save normal results and errors in separate files:

```bash
find /var/log -name "*.log" > found-files.txt 2> search-errors.txt
```

This is useful when some directories cannot be accessed but you still want to collect available results.

To send both standard output and standard error to the same file in Bash:

```bash
command > output.log 2>&1
```

This redirects standard output to `output.log`, then redirects standard error to the same destination.

A shorter Bash form is:

```bash
command &> output.log
```

The `&>` form is Bash-specific and may not work in every shell.

The order matters. For example, `command 2>&1 > output.log` does not redirect both streams to `output.log` in the same way.

---

## 8. Discard output with `/dev/null`

Linux provides `/dev/null`, a special device that discards data written to it.

Discard normal output:

```bash
command > /dev/null
```

Discard error messages:

```bash
command 2> /dev/null
```

Discard both in Bash:

```bash
command &> /dev/null
```

Use this carefully. Discarding errors can hide useful information during troubleshooting.

---

## 9. Here-document (`<<`)

A here-document supplies multiple lines of input to a command.

```bash
cat <<EOF
Linux
Docker
Kubernetes
EOF
```

The shell passes the text between the markers to `cat`, which prints it.

Here-documents are useful in shell scripts when supplying multi-line input or creating configuration files. Be careful when generating real configuration files because shell expansion, quoting, and permissions can affect the result.

---

## 10. DevOps use cases

### Save system information

```bash
df -h > disk-usage.txt
```

Stores a snapshot of filesystem usage.

### Capture a service's diagnostic output

```bash
systemctl status nginx > nginx-status.txt 2>&1
```

Saves both normal output and error output from the command.

### Keep a simple activity log

```bash
echo "$(date): deployment check completed" >> deployment.log
```

Adds a timestamped entry to a log file.

### Separate search results and errors

```bash
find /var/log -name "*.log" > log-files.txt 2> find-errors.txt
```

Keeps discovered files separate from access or search errors.

---

## 11. Common mistakes

- Using `>` when you intended to append with `>>`.
- Forgetting that redirection can truncate an existing file immediately.
- Assuming `>` captures standard error as well as standard output.
- Redirecting errors to `/dev/null` and then losing important troubleshooting information.
- Assuming redirection works identically in every shell.

---

## 12. Interview questions

**Q1. What is output redirection in Linux?**

It sends a command's standard output to a destination such as a file.

**Q2. What is the difference between `>` and `>>`?**

`>` overwrites a file's contents; `>>` appends to the end.

**Q3. What does `2>` mean?**

It redirects standard error, whose file descriptor is `2`.

**Q4. What does `<` do?**

It makes a command read standard input from a file.

**Q5. What is `/dev/null`?**

It is a special device that discards data written to it.

**Q6. How do you save both standard output and standard error in Bash?**

```bash
command > output.log 2>&1
```

**Q7. Why is redirection useful in DevOps?**

It helps capture diagnostics, save command results, build automation scripts, and retain troubleshooting evidence.

---

## 13. Key takeaway

Redirection controls where Linux commands read input and send output. Understand `>`, `>>`, `<`, and `2>` before using them in scripts or production troubleshooting.

Useful references: `man bash`, `man sort`, `man find`, and `man tee`.