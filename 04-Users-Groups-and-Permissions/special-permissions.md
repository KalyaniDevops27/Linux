# Linux Special Permissions: setuid, setgid, and Sticky Bit

## 1. Introduction

Linux provides three special permission bits in addition to the normal read (`r`), write (`w`), and execute (`x`) permissions:

- **setuid (Set User ID)**
- **setgid (Set Group ID)**
- **Sticky bit**

These permissions modify how certain executable files and directories behave. They are particularly important when multiple users or applications share resources.

From a DevOps perspective, understanding these bits helps with troubleshooting access issues, securing shared directories, and reviewing privileged programs.

---

## 2. setuid — Set User ID

### What is setuid?

When set on an executable file, setuid can cause the process to run with the file owner's effective user ID instead of the ID of the user who launched it. The actual behavior depends on operating-system and filesystem restrictions.

A familiar example on many Linux distributions is `/usr/bin/passwd`, which may use controlled privileges to update password information.

### How to identify setuid

```bash
ls -l /usr/bin/passwd
```

On systems where setuid is enabled, the owner's execute position may display `s`, such as:

```text
-rwsr-xr-x
```

The lowercase `s` indicates that setuid is set and the owner-execute bit is enabled. An uppercase `S` indicates setuid is set but the owner-execute bit is not enabled.

### Numeric value

The numeric value of setuid is `4`.

### Setting and inspecting setuid

The syntax is:

```bash
chmod u+s filename
```

Inspect an existing file with:

```bash
stat filename
```

Do not apply setuid to arbitrary executables or scripts. It can introduce privilege-escalation vulnerabilities, and Linux may ignore setuid behavior in some circumstances.

---

## 3. setgid — Set Group ID

### What is setgid?

setgid has different effects depending on whether it is applied to an executable file or a directory.

- **Executable file:** A process may run with the file's group as its effective group ID.
- **Directory:** Newly created files and subdirectories normally inherit the directory's group.

The directory behavior is especially useful when a team needs to collaborate on shared files.

### DevOps example

Imagine that developers and deployment automation both need access to a shared application directory. The directory belongs to a common group named `devops`.

```bash
sudo chgrp devops /srv/app
sudo chmod 2770 /srv/app
```

The leading `2` enables setgid on the directory. The `770` portion grants read, write, and execute permissions to the owner and group, but none to others.

These commands assume that the `devops` group exists and that the intended owner and group access have been reviewed.

### How to identify setgid

```bash
ls -ld /srv/app
```

A permission string may contain `s` in the group-execute position, for example:

```text
drwxrws---
```

The lowercase `s` indicates that setgid and group execute are enabled.

### Numeric value

The numeric value of setgid is `2`.

### Setting setgid

```bash
chmod g+s directory_name
```

---

## 4. Sticky bit

### What is the sticky bit?

On a shared, writable directory, the sticky bit generally restricts users from deleting or renaming files owned by other users. The directory owner and privileged users have additional authority.

It does not prevent users from reading every file or automatically make the directory private.

### Real-world example: `/tmp`

Linux systems commonly use `/tmp` for temporary files created by different users and applications.

Inspect it with:

```bash
ls -ld /tmp
```

A typical permission string is:

```text
drwxrwxrwt
```

The final `t` indicates the sticky bit.

### Numeric value

The numeric value of the sticky bit is `1`.

### Setting the sticky bit

On a test directory:

```bash
mkdir shared-temp
chmod 1777 shared-temp
ls -ld shared-temp
```

The leading `1` enables the sticky bit, while `777` grants ordinary read, write, and execute permissions to everyone. The sticky bit limits file deletion and renaming in the shared directory.

Only use this configuration when it matches the intended security requirements.

---

## 5. Numeric reference

| Special permission | Numeric value | Symbolic form |
|---|---:|---|
| setuid | 4 | `u+s` |
| setgid | 2 | `g+s` |
| Sticky bit | 1 | `+t` |

The special-permission digit is placed before the ordinary three permission digits.

Examples:

- `4755`: setuid with ordinary permissions `755`
- `2750`: setgid with ordinary permissions `750`
- `1777`: sticky bit with ordinary permissions `777`

---

## 6. Inspect permissions and metadata

Use these commands to understand existing permissions:

```bash
ls -l filename
ls -ld directory_name
stat filename
stat directory_name
```

For a broader search, you can inspect files with special permission bits:

```bash
find /usr /bin /sbin -type f -perm /6000 -print 2>/dev/null
```

This searches common system directories for files with setuid or setgid bits. Results depend on your distribution and filesystem layout. Review findings rather than changing them automatically.

---

## 7. DevOps use cases

### Shared deployment directories

setgid can help ensure that new files in a team directory inherit the expected group.

### Temporary directories

The sticky bit helps protect users' files in shared writable locations such as `/tmp`.

### Security reviews

DevOps engineers can audit setuid and setgid executables because unexpected privileged files may create security risks.

### Troubleshooting access

`ls -l` and `stat` help identify special permission bits when ordinary permission checks do not explain a program's behavior.

---

## 8. Common mistakes

- Setting setuid on an arbitrary application without understanding its security implications.
- Assuming setgid on a directory grants write permission automatically.
- Assuming the sticky bit prevents all access to files.
- Using recursive permission changes without checking their impact.
- Confusing special permission bits with normal owner, group, and other permissions.

---

## 9. Interview questions

**Q1. What are the three special permission bits in Linux?**

setuid, setgid, and the sticky bit.

**Q2. What does setuid do on an executable?**

It can cause the process to use the executable owner's effective user ID, subject to system restrictions.

**Q3. What is the benefit of setgid on a directory?**

It normally makes new files and subdirectories inherit the directory's group, supporting shared team access.

**Q4. Why is the sticky bit used on `/tmp`?**

It helps prevent users from deleting or renaming other users' files in a shared writable directory.

**Q5. What are the numeric values of these bits?**

setuid is `4`, setgid is `2`, and the sticky bit is `1`.

**Q6. How can you inspect special permissions?**

Use `ls -l`, `ls -ld`, and `stat`.

**Q7. Why should setuid files be reviewed carefully?**

They can grant programs elevated effective privileges, so a vulnerable or unexpected executable may create a security risk.

---

## 10. Key takeaway

Special permission bits change how Linux handles executable privileges and shared directories. Understand their behavior, inspect existing configurations, and grant only the privileges required.

Useful references:

- `man chmod`
- `man inode`
- `man find`