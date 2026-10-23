# Linux `umask` Command

## 1. Introduction

Whenever a Linux application creates a file or directory, the system determines its initial permissions.

As a DevOps engineer, I need to understand these defaults because applications create logs, temporary files, configuration files, and deployment artifacts.

The `umask` command controls which permission bits are removed from the permissions requested when new files and directories are created.

---

## 2. What Is `umask`?

`umask` stands for **user file-creation mode mask**.

It defines a mask that removes permission bits from the initial mode requested by a program.

### Check the current umask

```bash
umask
```

Example output:

```text
0022
```

The leading zero is part of the displayed octal representation. The permission mask here is `022`.

To display permissions symbolically, use:

```bash
umask -S
```

Example output:

```text
u=rwx,g=rx,o=rx
```

This describes the permissions that are masked out for user, group, and others. It is not the permission mode that every newly created file will receive.

---

## 3. Understand Default Permissions

Linux commonly starts with these maximum modes when creating files and directories:

| Object | Starting mode | Meaning |
|---|---:|---|
| Regular file | `666` | Read and write for owner, group, and others |
| Directory | `777` | Read, write, and execute for owner, group, and others |

These are starting modes, not guaranteed final permissions. A program can request a more restrictive mode, and `umask` removes additional permissions.

Regular files do not normally receive execute permission by default.

---

## 4. How umask 022 Works

Suppose the current mask is:

```bash
umask 022
```

The mask removes group-write and others-write permission from the starting modes.

### Regular file

```text
Starting mode: 666
umask:         022
Result:        644
```

The resulting permissions are:

```text
rw-r--r--
```

- Owner: read and write
- Group: read
- Others: read

### Directory

```text
Starting mode: 777
umask:         022
Result:        755
```

The resulting permissions are:

```text
rwxr-xr-x
```

- Owner: read, write, and execute
- Group: read and execute
- Others: read and execute

**Remember:** This is a common example. The actual permissions depend on the mode requested by the application creating the object.

---

## 5. Common umask Values

| umask | Typical file mode | Typical directory mode | General effect |
|---|---:|---:|---|
| `022` | `644` | `755` | Removes group and others write |
| `027` | `640` | `750` | Removes group write and all basic permissions for others |
| `077` | `600` | `700` | Removes all basic group and others permissions |

These examples assume the usual starting modes of `666` for files and `777` for directories.

### Example: umask 027

```bash
umask 027
touch example.txt
mkdir example-dir
ls -l example.txt
ls -ld example-dir
```

Under the usual creation modes, the file receives `640` and the directory receives `750`.

This can be useful when files should be accessible to their owner and authorized group but not to others.

### Example: umask 077

```bash
umask 077
touch private.txt
mkdir private-dir
```

Under the usual creation modes:

- `private.txt` receives mode `600`.
- `private-dir` receives mode `700`.

This is useful for private working files, although sensitive applications should also consider ownership, storage location, and other security controls.

---

## 6. Change the Current umask

To change the mask for the current shell:

```bash
umask 027
```

Verify it:

```bash
umask
```

The new mask generally affects files and directories created by processes launched from that shell, subject to the application's requested permissions and execution environment.

It does not change permissions on files that already exist.

---

## 7. umask vs. chmod

| Command | Purpose | Example |
|---|---|---|
| `umask` | Controls which permission bits are removed during creation | `umask 027` |
| `chmod` | Changes permissions on an existing file or directory | `chmod 640 example.txt` |

For example:

```bash
umask 027
touch app.log
```

This typically creates `app.log` with mode `640`, assuming the application requests the usual `666` file mode.

If the file already exists, changing `umask` does not update its permissions. Use `chmod` when you need to modify an existing file's mode.

---

## 8. DevOps Example

Suppose a deployment script creates log files on a shared Linux server.

You want the files to be readable and writable by the owner, readable by the authorized group, and inaccessible to others through the basic permission bits.

Set the mask before running the script:

```bash
umask 027
./deploy.sh
```

If the script creates a log file using the usual file-creation mode, the resulting mode will typically be `640`.

Verify the file:

```bash
ls -l application.log
```

Example output:

```text
-rw-r----- 1 appuser appgroup 2048 Oct 22 10:00 application.log
```

The exact outcome depends on how the script creates the file. An application can explicitly request more restrictive permissions, and other access-control mechanisms may also apply.

---

## 9. Important Considerations

### umask is not a complete security policy

A restrictive mask is useful, but it does not replace correct ownership, application configuration, or access-control lists.

### umask is inherited through the process environment

A shell's mask is generally inherited by processes it launches. Different sessions, services, and scheduled jobs can have different masks.

Do not assume that an interactive shell's `umask` is also the mask used by a system service.

### Making umask persistent

Setting `umask 027` in a terminal usually changes the mask only for that shell and its descendants.

For a persistent setting, the appropriate location depends on the environment. User shell startup files, login configuration, service manager settings, and application-specific settings may all behave differently.

Before changing production defaults, determine which processes need the setting and test the result.

## 10. Interview Questions

### Beginner

1. What is `umask` in Linux?
2. What is the difference between `umask` and `chmod`?
3. What are the usual starting modes for files and directories?
4. What permissions do a new file and directory typically receive with `umask 022`?
5. Does changing `umask` affect existing files?

### Intermediate

6. What are the typical file and directory modes for `umask 027`?
7. Why do regular files commonly start with mode `666` instead of `777`?
8. Why might a service use a different umask from your interactive shell?
9. How would you investigate unexpectedly permissive log-file permissions?
10. Why is umask useful when securing application-generated files?

---

## Conclusion

The `umask` command helps control the default permissions of newly created files and directories.

In DevOps, setting appropriate defaults can reduce accidental exposure of logs, configuration files, and deployment artifacts. Always verify the actual permissions produced by the application rather than assuming that a particular umask guarantees a specific result.

## Further Reading

- [GNU Coreutils: File mode creation mask](https://www.gnu.org/software/coreutils/manual/html_node/Mode-Structure.html)
- [Linux manual: umask(2)](https://man7.org/linux/man-pages/man2/umask.2.html)