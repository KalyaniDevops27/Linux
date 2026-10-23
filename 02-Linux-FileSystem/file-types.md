# Linux File Types

## Introduction

Linux uses a common filesystem interface to represent different kinds of objects, including regular files, directories, symbolic links, device files, pipes, and sockets.

Understanding these types helps with Linux administration, troubleshooting, scripting, and DevOps tasks.

## 1. Regular Files (`-`)

Regular files store data. They may contain text, logs, scripts, configuration, binary executables, or other content.

Examples:
- Application logs
- Shell scripts
- Configuration files
- Application binaries

```bash
ls -l app.log
file app.log
```

A filename extension is not a reliable way to determine the actual content type. Use `file` when you need to inspect a file.

---

## 2. Directories (`d`)

A directory organizes filenames and references to filesystem objects. It helps users navigate and manage files.

Common examples include:

- `/etc` — system and application configuration
- `/home` — user home directories
- `/var/log` — commonly used for system and application logs
- `/tmp` — temporary files

Useful commands:

```bash
ls -ld /etc
find . -type d
```

---

## 3. Symbolic Links (`l`)

A symbolic link, or symlink, points to another filesystem path. It works similarly to a shortcut.

Create and inspect one:

```bash
ln -s /opt/myapp/releases/v1 /opt/myapp/current
ls -l /opt/myapp/current
readlink /opt/myapp/current
```

The target path may be absolute or relative. A symlink can also become broken if its target is moved or deleted.

Check whether a path resolves to an existing target:

```bash
test -e /opt/myapp/current && echo "Target exists" || echo "Target missing"
```

---

## 4. Character Device Files (`c`)

Character device files provide a stream-oriented interface to devices or kernel functionality. They are commonly found under `/dev`.

Example:

```bash
ls -l /dev/null
```

`/dev/null` discards data written to it. It is often used to discard command output:

```bash
some_command > /dev/null
```

Device access is controlled by system permissions and other security mechanisms.

---

## 5. Block Device Files (`b`)

Block device files provide block-based access to storage devices. They are commonly associated with disks, partitions, and other block storage.

Examples may include `/dev/sda` or `/dev/nvme0n1`, depending on the system.

Inspect available block devices:

```bash
lsblk
```

Device names vary by distribution, hardware, virtual machine, and storage configuration.

---

## 6. Named Pipes or FIFOs (`p`)

A named pipe allows processes to exchange data through a filesystem object. Data is read and written as a stream.

Create a practice FIFO:

```bash
mkfifo demo.pipe
ls -l demo.pipe
```

The first character should be `p`.

Named pipes can block while waiting for a reader or writer, so test them carefully in a separate terminal. Remove the practice FIFO when finished:

```bash
rm demo.pipe
```

---

## 7. Sockets (`s`)

A socket is an endpoint for communication between processes. Unix domain sockets often appear as filesystem paths.

For example, `/run/docker.sock` may be used for communication with the Docker daemon on systems configured that way.

Inspect a socket if one exists:

```bash
ls -l /run/docker.sock
```

A socket's existence does not guarantee that its service is running or accepting connections. Access to sensitive sockets can grant significant privileges, so permissions matter.

---

## How to Identify File Types

### Using `ls -l`

```bash
ls -l
```

The first character indicates the filesystem object's type:

| Character | Type |
|---|---|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device |
| `b` | Block device |
| `p` | Named pipe (FIFO) |
| `s` | Socket |

### Using `file`

```bash
file app.log
file /bin/ls
```

The `file` command examines content and metadata to classify an object. It complements, rather than replaces, the filesystem type shown by `ls -l`.

### Using `stat`

```bash
stat app.log
```

`stat` displays metadata such as file type, size, permissions, ownership, and timestamps.

### Using `find`

Find regular files:

```bash
find . -type f
```

Find directories:

```bash
find . -type d
```

Find symbolic links:

```bash
find . -type l
```

---

## DevOps Example

During a deployment, an application may use a symbolic link such as `/opt/myapp/current` to point to the active release directory.

If the deployment fails, check the link and its target:

```bash
ls -l /opt/myapp/current
readlink /opt/myapp/current
```

If an application cannot write to its log, verify that the log path is a file and that its permissions and ownership are appropriate:

```bash
ls -l /var/log/myapp/
stat /var/log/myapp/app.log
```

These checks help identify incorrect paths, broken symlinks, and unexpected filesystem objects.

---

## Key Takeaways

- Linux filesystems support several types of objects, not just regular files and directories.
- The first character in `ls -l` output identifies the filesystem object type.
- File extensions do not reliably identify actual file content.
- Symbolic links point to paths and may become broken.
- Device files and sockets are important interfaces, and their permissions matter.
- `ls -l`, `file`, `stat`, and `find` are useful troubleshooting commands.
