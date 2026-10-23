# Linux `chmod` Command

## 1. Introduction

In Linux, every file and directory has permissions that control who can read, modify, or execute it.

As a DevOps engineer, I use `chmod` when configuring deployment scripts, securing application files, and troubleshooting permission errors.

For example, suppose a deployment script exists on a Linux server, but executing it produces a permission error. The file may not have execute permission for the current user. The `chmod` command can change its permission bits.

---

## 2. What Is `chmod`?

`chmod` stands for **change mode**.

It changes the permission bits of a file or directory.

The basic syntax is:

```bash
chmod [options] permissions filename
```

Example:

```bash
chmod 644 application.conf
```

This sets the file's permissions to `rw-r--r--`.

**Important:** `chmod` changes permissions, not file ownership. Use `chown` when ownership needs to change.

---

## 3. Understanding Linux Permissions

Linux permissions are divided into three categories:

| Symbol | Meaning | Applies to |
|---|---|---|
| `u` | User (owner) | File owner |
| `g` | Group | Owning group |
| `o` | Others | Everyone else |
| `a` | All | User, group, and others |

Each category can have these basic permissions:

| Permission | Symbol | Meaning for a file | Meaning for a directory |
|---|---|---|---|
| Read | `r` | Read file contents | List directory entries |
| Write | `w` | Modify file contents | Create, delete, or rename entries, subject to other checks |
| Execute | `x` | Execute the file, if it is executable | Traverse or access entries by path, subject to other checks |

Directory permissions have different practical effects from file permissions. For example, being able to list a directory and being able to access a known filename inside it are not exactly the same thing.

---

## 4. Check Existing Permissions

Use `ls -l` to inspect a file:

```bash
ls -l deploy.sh
```

Example output:

```text
-rw-r--r-- 1 devops developers 1200 Oct 22 10:00 deploy.sh
```

The first ten characters represent the file type and permission bits:

```text
-rw-r--r--
```

- The first character (`-`) identifies a regular file.
- `rw-` means the owner can read and write.
- `r--` means the group can read.
- `r--` means others can read.

The owner cannot execute this script yet because the execute bit is not set.

---

## 5. Symbolic Mode

Symbolic mode uses letters and operators to modify permissions.

The operators are:

- `+` adds permissions.
- `-` removes permissions.
- `=` sets permissions to exactly the specified values for the selected categories.

### Add execute permission for the owner

```bash
chmod u+x deploy.sh
```

The owner can now execute the file.

### Remove write permission from others

```bash
chmod o-w application.conf
```

This removes the write permission from the others category.

### Give the group read and write permissions

```bash
chmod g+rw shared.txt
```

### Set exact permissions for the owner

```bash
chmod u=rwx deploy.sh
```

This sets the owner's permissions to read, write, and execute. It does not, by itself, change the group or others permissions.

---

## 6. Numeric Mode

Numeric mode represents permissions using numbers.

| Permission | Number |
|---|---:|
| Read (`r`) | 4 |
| Write (`w`) | 2 |
| Execute (`x`) | 1 |
| No permission (`---`) | 0 |

Add the values to calculate each category's permission.

For example:

- `7` = 4 + 2 + 1 = `rwx`
- `6` = 4 + 2 = `rw-`
- `5` = 4 + 1 = `r-x`
- `4` = `r--`
- `0` = `---`

A three-digit permission has one digit each for owner, group, and others.

### Common examples

**`chmod 644 application.conf`**

```text
Owner:  rw-
Group:  r--
Others: r--
```

Useful for ordinary configuration or text files that should be readable but not writable by everyone.

**`chmod 600 secrets.env`**

```text
Owner:  rw-
Group:  ---
Others: ---
```

Only the owner has read and write permission. This can be appropriate for sensitive files, provided the owner and other access controls are configured correctly.

**`chmod 755 deploy.sh`**

```text
Owner:  rwx
Group:  r-x
Others: r-x
```

The owner can modify and execute the script. Group members and others can read and execute it.

**`chmod 700 private-directory`**

```text
Owner:  rwx
Group:  ---
Others: ---
```

The owner can access the directory; group and others have no permissions through these basic permission bits.

**Note:** Numeric modes with four digits can also specify special permission bits. Those are covered in `special-permissions.md`.

---

## 7. Recursive Permissions

The `-R` option applies permission changes recursively to a directory tree.

Example:

```bash
chmod -R 755 scripts/
```

This applies `755` to all files and directories under `scripts/`, including the directory itself.

**Be careful:** applying one mode recursively is often inappropriate for mixed files and directories. It may make private files readable or mark ordinary files as executable.

For example, avoid blindly running `chmod -R 777` on application directories. It grants everyone read, write, and execute permissions, potentially exposing data or allowing unwanted modifications.

Choose permissions based on the access each file and directory actually requires.

---

## 8. DevOps Example

Suppose your repository contains a deployment script:

```bash
ls -l deploy.sh
```

Output:

```text
-rw-r--r-- 1 devops developers 1200 Oct 22 10:00 deploy.sh
```

Trying to execute it directly may fail because the file lacks execute permission for the user.

Add execute permission for the owner:

```bash
chmod u+x deploy.sh
```

Verify the result:

```bash
ls -l deploy.sh
```

You should now see an execute bit for the owner, such as:

```text
-rwxr--r-- 1 devops developers 1200 Oct 22 10:00 deploy.sh
```

The script's contents and ownership have not changed.

To run it, use:

```bash
./deploy.sh
```

Execution can still fail for other reasons, such as an invalid interpreter, missing dependencies, or directory access restrictions.

---

## 9. Troubleshooting Permission Errors

When a deployment script or application file cannot be accessed, inspect the permissions first:

```bash
ls -l deploy.sh
```

For directory access problems, inspect the directory permissions too:

```bash
ls -ld /path/to/application
```

If necessary, check ownership and the current user's groups:

```bash
id
```

Then determine which permission change is actually required. Avoid using `chmod 777` as a default fix.

---

## 10. Interview Questions

### Beginner

1. What does `chmod` stand for?
2. What is the difference between `chmod` and `chown`?
3. What do `r`, `w`, and `x` mean?
4. What does `chmod 755 deploy.sh` do?
5. What is the difference between symbolic and numeric modes?

### Intermediate

6. How do you add execute permission only for the file owner?
7. What does `chmod 600 secrets.env` mean?
8. What is the risk of using `chmod -R 777`?
9. How do file permissions differ from directory permissions?
10. If a script has execute permission but still fails to run, what else would you investigate?

---

## Conclusion

`chmod` is one of the fundamental tools for managing Linux access.

In DevOps, the goal is not simply to make a file accessible. It is to give the right users the permissions they need while avoiding unnecessary access.