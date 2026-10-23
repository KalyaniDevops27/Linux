# Absolute vs Relative Paths in Linux

## 1. Introduction

A path specifies the location of a file or directory in a filesystem.

In Linux, there are two main ways to specify a path:

- **Absolute path:** Specifies a location starting from the root directory (`/`).
- **Relative path:** Specifies a location relative to the current working directory.

Understanding paths is essential for Linux administration, shell scripting, application deployment, and CI/CD automation.

---

## 2. What Is an Absolute Path?

An absolute path describes the complete location of a file or directory starting at `/`.

Example:

```text
/home/ubuntu/scripts/deploy.sh
```

This path identifies `deploy.sh` inside `scripts`, which is inside `ubuntu`, which is inside `home`.

An absolute path does not depend on the current working directory. Its destination remains the same as long as the relevant filesystem layout and path resolution remain unchanged.

### Examples

```bash
cat /etc/os-release
ls /var/log
cd /home/ubuntu
```

Each path begins at the root directory.

---

## 3. What Is a Relative Path?

A relative path specifies a location starting from the current working directory.

Use `pwd` to display the current working directory:

```bash
pwd
```

Suppose the current directory is:

```text
/home/ubuntu/app
```

And the directory structure is:

```text
/home/ubuntu/
├── app/
│   ├── deploy.sh
│   └── config/
│       └── app.conf
└── logs/
    └── app.log
```

From `/home/ubuntu/app`, these relative paths work as follows:

```bash
./deploy.sh
cat config/app.conf
cat ../logs/app.log
```

- `./deploy.sh` refers to the script in the current directory.
- `config/app.conf` refers to a file under the current directory's `config` directory.
- `../logs/app.log` goes up one directory and then enters `logs`.

These commands assume the example directories and files exist.

---

## 4. Understanding `.` and `..`

### Current directory: `.`

A single dot represents the current directory.

```bash
ls .
```

This lists entries in the current directory.

You may also use `./` when referring to a file in the current directory:

```bash
./deploy.sh
```

For a script to run this way, it must have suitable execute permissions and a valid interpreter or executable format.

### Parent directory: `..`

Two dots represent the parent directory.

```bash
cd ..
```

This moves to the parent directory.

For example, if the current directory is `/home/ubuntu/app/config`:

| Expression | Resolved location |
|---|---|
| `.` | `/home/ubuntu/app/config` |
| `..` | `/home/ubuntu/app` |
| `../..` | `/home/ubuntu` |
| `../../..` | `/home` |

Each additional `..` moves one level up, until reaching the root directory.

## 5. Absolute Path vs Relative Path

| Feature | Absolute path | Relative path |
|---|---|---|
| Starting point | Root directory `/` | Current working directory |
| Begins with `/` | Yes, for ordinary absolute filesystem paths | No |
| Depends on current directory | No | Yes |
| Example | `/etc/hosts` | `../logs/app.log` |
| Common benefit | Explicit location | Convenient for nearby files and directories |

Both types are useful. The right choice depends on the task and how the script or command will be executed.

---

## 6. Useful Path-Related Commands

### Display the current directory

```bash
pwd
```

### Change to a specific directory

```bash
cd /var/log
```

### Move to the parent directory

```bash
cd ..
```

### Return to the previous working directory

```bash
cd -
```

### List the current directory

```bash
ls .
```

### List a directory using an absolute path

```bash
ls /etc
```

### Inspect a file using a relative path

```bash
cat config/app.conf
```

The last command assumes that `config/app.conf` exists relative to your current directory.

---

## 7. Paths in DevOps and Shell Scripting

Path handling is important in deployment automation because a script may be executed from different working directories.

For example, this command relies on the current working directory:

```bash
cat config/app.conf
```

It may fail if the command runs from an unexpected location.

### Resolve a path relative to a Bash script

A common Bash pattern is:

```bash
#!/usr/bin/env bash
set -euo pipefail

SCRIPT_DIR="$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd)"
CONFIG_FILE="$SCRIPT_DIR/config/app.conf"

cat "$CONFIG_FILE"
```

This calculates the directory containing the Bash script and builds a path relative to it.

For this example, `config/app.conf` must be inside a `config` directory located beside the script. This is a Bash-specific approach and assumes the script can be resolved through the filesystem.

### Common DevOps applications

- **Shell scripting:** Locate scripts, configuration files, and output directories.
- **Jenkins:** Understand the workspace and the directory in which build commands execute.
- **Docker:** Understand build contexts and paths used by `COPY` and `ADD`.
- **CI/CD pipelines:** Avoid failures caused by unexpected working directories.
- **Linux administration:** Navigate logs, configuration, and application directories.

---

## 8. Common Mistakes

### Mistake 1: Assuming a relative path always starts from the script's directory

A relative path normally resolves from the process's current working directory, not automatically from the directory containing the script.

### Mistake 2: Forgetting the leading slash

`etc/hosts` is a relative path, while `/etc/hosts` is an absolute path.

### Mistake 3: Using the wrong number of parent-directory references

`../` moves up one directory. `../../` moves up two directories.

### Mistake 4: Assuming a path exists

A correctly written path can still fail if the file or directory does not exist or the user lacks permission to access it.

---

## 9. Interview Questions

### Q1. What is an absolute path?

An absolute path specifies a file or directory's location starting from the root directory (`/`).

### Q2. What is a relative path?

A relative path specifies a location relative to the current working directory.

### Q3. What is the difference between `.` and `..`?

`.` represents the current directory, while `..` represents the parent directory.

### Q4. What does `pwd` do?

It prints the current working directory.

### Q5. Why can a relative path fail in a CI/CD pipeline?

The pipeline may execute a command from a different working directory than the script expects, so the relative path resolves to the wrong location.

### Q6. How can a Bash script locate a file relative to itself?

It can resolve the directory containing the script using `BASH_SOURCE[0]` and `dirname`, then construct the required path.

---

## 10. Key Takeaway

Absolute paths start from `/`. Relative paths start from the current working directory.

Knowing the difference helps you navigate Linux reliably and write scripts that behave predictably in DevOps environments.

Next topic: **Linux File Types and Inodes**.