# Basic Linux Commands

## Introduction

Linux commands allow us to interact with the operating system through a terminal. As a DevOps engineer, we use them daily to navigate servers, manage files, inspect application logs, search for errors, and troubleshoot deployments.

This document covers essential Linux commands, with practical examples and common DevOps use cases.

## 1. Navigation Commands

### 1.1 `pwd` — Print Working Directory

The `pwd` command displays the current working directory.

```bash
pwd
```

Example output:

```text
/home/ubuntu
```

**DevOps use case:** Confirm your working directory before running a deployment script or managing application files.

Useful variations:

```bash
pwd -L
pwd -P
```

- `pwd -L` displays the logical path.
- `pwd -P` displays the physical path, resolving symbolic links.

### 1.2 `ls` — List Directory Contents

The `ls` command lists files and directories.

```bash
ls
ls -l
ls -a
ls -lh
ls -lah
```

| Option | Purpose |
|---|---|
| `-l` | Detailed listing, including permissions and ownership |
| `-a` | Includes hidden entries |
| `-h` | Displays file sizes in a human-readable format |

**DevOps use case:** Inspect application files, check permissions, and identify hidden configuration files.

### 1.3 `cd` — Change Directory

The `cd` command changes the current working directory.

```bash
cd /var/log
cd ..
cd ~
cd -
```

- `cd /var/log` moves to `/var/log`.
- `cd ..` moves to the parent directory.
- `cd ~` moves to your home directory.
- `cd -` returns to the previous working directory.

**DevOps use case:** Navigate to application directories and logs while troubleshooting a server.

## 2. File and Directory Operations

### 2.1 `mkdir` — Create Directories

The `mkdir` command creates directories.

```bash
mkdir project
mkdir -p project/config/dev
```

The `-p` option creates missing parent directories as needed and does not fail merely because an existing parent directory is present.

**DevOps use case:** Create directory structures for applications, configuration, and deployment artifacts.

### 2.2 `touch` — Create an Empty File or Update Timestamps

```bash
touch app.log
touch config.yaml
```

If a file does not exist, `touch` normally creates an empty file. If it already exists, it updates its access and modification timestamps, subject to filesystem and permission rules.

**DevOps use case:** Create a placeholder file or prepare a file for a practice exercise.

### 2.3 `cp` — Copy Files and Directories

Copy a file:

```bash
cp app.conf app.conf.backup
```

Copy a directory recursively:

```bash
cp -r config config-backup
```

Preserve common file attributes where possible:

```bash
cp -p app.conf app.conf.backup
```

**DevOps use case:** Back up configuration files before making changes. For production backups, use a suitable backup process rather than relying only on a manual copy.

### 2.4 `mv` — Move or Rename Files

Rename a file:

```bash
mv old-name.txt new-name.txt
```

Move a file into a directory:

```bash
mv app.log /tmp/
```

**DevOps use case:** Organize deployment files, rename configuration files, or move artifacts between directories.

Be careful: depending on the destination and options, `mv` can replace an existing destination.

### 2.5 `rm` — Remove Files and Directories

Remove a file:

```bash
rm old-file.txt
```

Remove an empty directory:

```bash
rmdir empty-directory
```

Remove a directory and its contents recursively:

```bash
rm -r old-directory
```

**Important:** `rm` normally does not move files to a recycle bin. Deleted files may be difficult or impossible to recover. Verify the path before removing anything, especially on production servers.

Avoid using broad commands such as `rm -rf` unless you fully understand the target and consequences.

## 3. Commands for Viewing Files

### 3.1 `cat` — Display File Contents

```bash
cat app.conf
```

Display multiple files:

```bash
cat file1.txt file2.txt
```

**Best for:** Small files that can comfortably fit on screen.

For large files, prefer `less` to avoid printing the entire file at once.

### 3.2 `less` — View Files Page by Page

```bash
less app.log
```

Useful keyboard controls:

- `Space` — move forward one page.
- `b` — move backward one page.
- `/error` — search for `error`.
- `n` — move to the next search match.
- `q` — quit.

**DevOps use case:** Inspect large application logs or configuration files without flooding the terminal.

### 3.3 `head` — Display the Beginning of a File

Display the first 10 lines:

```bash
head app.log
```

Display the first 20 lines:

```bash
head -n 20 app.log
```

**DevOps use case:** Check the beginning of a log or configuration file.

### 3.4 `tail` — Display the End of a File

Display the last 10 lines:

```bash
tail app.log
```

Display the last 50 lines:

```bash
tail -n 50 app.log
```

Follow a log as new lines are added:

```bash
tail -f app.log
```

Stop following with `Ctrl+C`.

**DevOps use case:** Monitor application logs while reproducing an error or testing a deployment.

## 4. Searching Commands

### 4.1 `find` — Search for Files and Directories

Find a file by name:

```bash
find /var/log -name "app.log"
```

Find regular files:

```bash
find . -type f
```

Find directories:

```bash
find . -type d
```

Find files ending in `.log`:

```bash
find . -type f -name "*.log"
```

Search is performed within the specified starting path. Permission restrictions may prevent `find` from accessing some locations.

**DevOps use case:** Locate configuration files, logs, or deployment artifacts when you don't know their exact directory.

### 4.2 `grep` — Search Text for a Pattern

Search for a word in a file:

```bash
grep "ERROR" app.log
```

Search without case sensitivity:

```bash
grep -i "error" app.log
```

Show matching line numbers:

```bash
grep -n "failed" app.log
```

Search recursively through files in a directory:

```bash
grep -r "database" ./config
```

**DevOps use case:** Find error messages, failed requests, or configuration values in application files and logs.

## 5. Practical DevOps Example

Imagine an application is failing after deployment. You need to find the logs, inspect recent messages, and locate a configuration file.

### Step 1: Check your current location

```bash
pwd
```

### Step 2: Navigate to the log directory

```bash
cd /var/log
```

### Step 3: List available files

```bash
ls -lah
```

### Step 4: Find the application's log

```bash
find /var/log -type f -name "*app*.log"
```

### Step 5: Inspect recent log entries

```bash
tail -n 50 /var/log/myapp/app.log
```

Replace the example path with the actual log path on your system.

### Step 6: Search for errors

```bash
grep -in "error" /var/log/myapp/app.log
```

These commands help you establish where you are, locate the relevant file, and inspect evidence before changing the application.

## 6. Quick Reference

| Command | Purpose |
|---|---|
| `pwd` | Display the current working directory |
| `ls` | List directory contents |
| `cd` | Change directory |
| `mkdir` | Create directories |
| `touch` | Create an empty file or update timestamps |
| `cp` | Copy files or directories |
| `mv` | Move or rename files |
| `rm` | Remove files or directories |
| `cat` | Display file contents |
| `less` | View files page by page |
| `head` | Display the beginning of a file |
| `tail` | Display the end of a file |
| `find` | Search for files and directories |
| `grep` | Search text for patterns |

## 7. Interview Questions

### Q1. What is the difference between `pwd` and `ls`?

`pwd` displays the current directory path. `ls` lists the contents of a directory.

### Q2. What is the difference between `cp` and `mv`?

`cp` creates a copy. `mv` moves or renames an item.

### Q3. What is the difference between `cat` and `less`?

`cat` displays file contents directly. `less` lets you navigate through a file page by page.

### Q4. How do you monitor a log file as new entries appear?

Use `tail -f filename`.

### Q5. How do you find a file by name?

Use `find /starting/path -name "filename"`.

### Q6. How do you search for errors in a log file?

Use `grep -i "error" filename` for a case-insensitive search.

### Q7. Why should you be careful with `rm`?

It deletes files or directories directly and normally does not send them to a recycle bin.

## Key Takeaways

- Navigation commands help you understand where you are and move around the filesystem.
- File-operation commands create, copy, move, rename, and remove items.
- Viewing commands help you inspect configuration and logs.
- Search commands help locate files and find important text.
- Practicing these commands in a test environment builds confidence before using them on real servers.

This document covers the basic commands only. Pipes, redirection, and command chaining will be documented separately.