# Linux Command Chaining

## 1. Introduction

Command chaining allows you to execute multiple Linux commands from a single command line.

Instead of typing and executing each command separately, you can connect commands using operators that control when the next command runs.

The three common operators are:

- `;` — execute commands sequentially.
- `&&` — execute the next command only if the previous command succeeds.
- `||` — execute the next command only if the previous command fails.

These operators are useful in Linux administration, troubleshooting, deployment workflows, and shell scripting.

---

## 2. Semicolon (`;`)

### What does it do?

The semicolon separates commands and executes them in sequence, regardless of whether an earlier command succeeded or failed.

### Example

```bash
pwd; ls; date
```

Linux attempts to execute all three commands in order.

### DevOps example

```bash
mkdir reports; date; ls
```

This attempts to create a directory, display the current date, and list the current directory.

Even if `mkdir` fails, the later commands are still attempted.

### When should you use it?

Use `;` when you want to execute multiple independent commands without making each one depend on the previous command's success.

---

## 3. AND operator (`&&`)

### What does it do?

The `&&` operator executes the next command only if the preceding command succeeds.

In a shell, success is normally indicated by an exit status of `0`. A nonzero status generally indicates failure.

### Example

```bash
mkdir demo && cd demo
```

If `mkdir demo` succeeds, Linux executes `cd demo`. If directory creation fails, the `cd` command is skipped.

### DevOps example

```bash
mkdir deployment && cd deployment && touch status.txt
```

The steps are:

1. Create the `deployment` directory.
2. Enter the directory only if creation succeeds.
3. Create `status.txt` only if the previous steps succeed.

### Another example

```bash
sudo systemctl restart nginx && systemctl is-active nginx
```

The second command runs only if the restart command returns a successful exit status. This does not guarantee that the service is healthy; `systemctl is-active` provides a separate status check.

### When should you use it?

Use `&&` when one step depends on the previous step succeeding.

---

## 4. OR operator (`||`)

### What does it do?

The `||` operator executes the next command only if the preceding command returns a nonzero exit status.

### Example

```bash
mkdir demo || echo "Directory creation failed"
```

If `mkdir demo` fails, the message is printed. If it succeeds, the `echo` command is skipped.

### DevOps example

```bash
systemctl is-active --quiet nginx || echo "Nginx is not active"
```

The message is displayed when the check returns a nonzero status.

This is a basic diagnostic example, not a complete service recovery mechanism.

### When should you use it?

Use `||` to provide a fallback action, display a message, or handle a failed command.

Be careful with automatic recovery commands: the failure might have several causes, and retrying blindly may not solve the underlying problem.

---

## 5. Combining `&&` and `||`

You can combine the operators to create conditional workflows.

```bash
mkdir demo && echo "Directory created" || echo "Operation failed"
```

This prints a success message if the directory creation succeeds. Otherwise, it prints the failure message.

However, operator precedence and the exit status of each command can make longer expressions difficult to interpret. The failure message here can also refer to the `echo` command on the success path if that command itself fails.

For important automation, use a readable shell script with explicit `if` statements rather than relying on a long chain of operators.

---

## 6. Difference between the operators

| Operator | Behavior | Typical use |
|---|---|---|
| `;` | Attempt the next command regardless of the previous result | Independent commands |
| `&&` | Run the next command if the previous command succeeds | Dependent steps |
| `||` | Run the next command if the previous command fails | Fallback handling |

---

## 7. Understanding exit status

Linux commands return an exit status when they finish.

- `0` normally means success.
- A nonzero status normally indicates failure.

You can inspect the most recent command's exit status with:

```bash
echo $?
```

For example:

```bash
true
echo $?
```

The output is `0`.

Now try:

```bash
false
echo $?
```

The output is `1`.

`$?` contains the exit status of the most recently completed foreground command, so inspect it immediately if you need to check a particular command.

---

## 8. DevOps use cases

### Create a directory and a file

```bash
mkdir app && cd app && touch application.log
```

Each step depends on the previous step succeeding.

### Check a service and report failure

```bash
systemctl is-active --quiet nginx || echo "Check the Nginx service"
```

Useful for simple diagnostic commands.

### Run independent information commands

```bash
date; hostname; uptime
```

Displays the date, hostname, and system uptime in sequence.

### Use redirection with command chaining

```bash
df -h > disk-usage.txt && echo "Disk report saved"
```

Saves disk usage information and prints the confirmation only if the `df` command succeeds.

---

## 9. Common mistakes

- Using `;` when a later command should depend on an earlier command's success.
- Assuming `&&` means that the entire workflow is guaranteed to be successful.
- Assuming `||` identifies the precise reason a command failed.
- Forgetting that an exit status describes a command's reported result, not necessarily the health of the entire application.
- Writing complicated chains that are difficult to troubleshoot.
- Assuming that a successful service restart always means the application is healthy.

---

## 10. Interview questions

**Q1. What is command chaining in Linux?**

It is the practice of connecting multiple commands using operators that control execution.

**Q2. What is the difference between `;` and `&&`?**

`;` attempts the next command regardless of the previous result, while `&&` runs it only if the previous command succeeds.

**Q3. What does `||` do?**

It runs the next command when the preceding command returns a nonzero exit status.

**Q4. What is an exit status?**

It is a numeric value returned by a command. `0` conventionally indicates success, while a nonzero value indicates an error or another unsuccessful result.

**Q5. How do you display the previous command's exit status?**

```bash
echo $?
```

**Q6. Give a practical DevOps example of `&&`.**

```bash
mkdir deployment && cd deployment && touch status.txt
```

Each command runs only if the preceding command succeeds.

---

## 11. Key takeaway

Command chaining helps you combine Linux operations efficiently. Understand when to use `;`, `&&`, and `||`, and prefer clear, testable scripts for complex or production-critical workflows.

