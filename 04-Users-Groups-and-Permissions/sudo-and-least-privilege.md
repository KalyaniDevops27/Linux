# Linux `sudo` and the Principle of Least Privilege

## 1. Introduction

Linux separates ordinary user activities from administrative operations. This helps protect the operating system, applications, and sensitive files from unauthorized changes.

As a DevOps engineer, I use `sudo` when an authorized task requires elevated privileges, such as installing a package, managing a system service, or modifying a protected configuration file.

The goal is not to use administrator privileges for everything. It is to use them only when necessary.

---

## 2. What Is `sudo`?

`sudo` stands for **superuser do**.

It allows an authorized user to execute a command with elevated privileges according to the system's sudo policy.

### Syntax

```bash
sudo command
```

### Example

```bash
sudo systemctl status nginx
```

This runs the command with elevated privileges. It assumes the system uses `systemd` and the account is authorized to run the command.

Depending on the system's configuration, `sudo` may ask for the user's password or apply another authentication policy.

**Important:** Not every Linux user can use `sudo`. Authorization is controlled by the system's sudo configuration and related access policies.

---

## 3. Why Do We Need `sudo`?

Some operations affect the entire system and therefore require administrative access.

Examples include:

### Install software

On Ubuntu or Debian:

```bash
sudo apt update
sudo apt install nginx
```

### Manage a service

```bash
sudo systemctl restart nginx
```

### Edit a protected configuration file

For example, to edit an Nginx configuration file:

```bash
sudoedit /etc/nginx/nginx.conf
```

The exact package manager, service name, and configuration path depend on the Linux distribution and software installation.

### Read a protected log

```bash
sudo less /var/log/auth.log
```

This file is common on Ubuntu and Debian systems, but other distributions may store authentication logs elsewhere.

Use elevated access only when the task requires it and you are authorized to perform it.

---

## 4. Check Your Sudo Permissions

Use:

```bash
sudo -l
```

This displays the commands the current user is permitted to run through `sudo`, subject to the configured policy and authentication requirements.

It is useful when troubleshooting a message such as:

```text
user is not in the sudoers file
```

That message indicates the user is not authorized under the applicable sudo policy. The appropriate fix is to have an authorized administrator review the access configuration—not to attempt to bypass it.

---

## 5. Run a Command as Another User

The `-u` option lets an authorized user run a command as a specified user.

For example:

```bash
sudo -u appuser whoami
```

If permitted, the command runs as `appuser`, and `whoami` prints the effective username.

### DevOps use case

An application may run under a dedicated service account. Testing a command as that account can help investigate file access, environment, or permission problems.

This is often preferable to giving the application account full administrator privileges.

---

## 6. Root User vs. Sudo

The `root` account is the traditional Linux superuser. It has extensive privileges over the system.

`sudo` allows an authorized user to execute specific commands with elevated privileges without needing to operate as root all the time.

| Aspect | Root account | `sudo` |
|---|---|---|
| Identity | Superuser account | Runs an authorized command with elevated privileges or as another permitted user |
| Scope | Broad administrative access | Can be restricted by policy |
| Accountability | Depends on logging and access setup | Can record which user invoked a command |
| Recommended practice | Restrict direct access where practical | Use for authorized administrative tasks |

### Example

Instead of switching to a root shell for routine work:

```bash
sudo systemctl restart nginx
```

This makes the privileged action explicit.

The actual security benefit depends on how sudo privileges are configured and how commands are used.

---

## 7. What Is the Principle of Least Privilege?

The principle of least privilege means that each user, process, and application should receive only the permissions needed to perform its assigned work.

### Example in a DevOps environment

Imagine an application has these requirements:

- Read its configuration.
- Write logs to an assigned directory.
- Listen on its configured network port.
- Access only the data it needs.

The application generally should not need unrestricted root access to perform these tasks.

A safer setup uses a dedicated service account, carefully configured file permissions, and narrowly scoped administrative operations.

### Why it matters

Least privilege helps reduce:

- Accidental system changes.
- Unauthorized access to sensitive files.
- The impact of compromised accounts or applications.
- Unnecessary exposure of administrator privileges.

---

## 8. Practical DevOps Scenario

Suppose Nginx is installed on a Linux server, and you need to check its status.

First, inspect the service:

```bash
systemctl status nginx
```

If the operation is permitted without elevated privileges, this may work directly. Otherwise, use:

```bash
sudo systemctl status nginx
```

If a restart is required and authorized:

```bash
sudo systemctl restart nginx
```

Then verify the service:

```bash
systemctl status nginx
```

Use `sudo` only where required. Do not assume that every command needs elevated access.

---

## 9. Common Mistakes

### Using `sudo` for every command

Routine commands such as `pwd`, `ls`, and `whoami` usually do not require administrator privileges.

Running everything through `sudo` increases the chance of accidental changes and can create files owned by root when you intended to work as your normal user.

### Using broad sudo permissions unnecessarily

Granting unrestricted administrative access to every team member defeats the principle of least privilege.

Where practical, authorize only the administrative commands and actions required by the role.

### Running unfamiliar commands with sudo

A command with elevated privileges can affect system-wide files and services. Understand what a command does and verify its target before running it.

### Treating `sudo` as a fix for every permission error

An application permission error may be caused by incorrect ownership, directory permissions, a service configuration issue, or another security restriction.

Investigate the underlying cause rather than simply rerunning everything as root.

---

## 10. Useful Commands

| Command | Purpose |
|---|---|
| `sudo command` | Run an authorized command with elevated privileges |
| `sudo -l` | Inspect the current user's sudo permissions |
| `sudo -u appuser command` | Run an authorized command as another user |
| `sudoedit file` | Edit a protected file using the configured sudo editing mechanism |
| `whoami` | Display the current effective username |
| `id` | Display user and group identities |

---

## 11. Interview Questions

### Beginner

1. What is `sudo` in Linux?
2. What is the difference between root and a normal user?
3. How do you check which commands you can run through `sudo`?
4. Why do some Linux commands require elevated privileges?
5. What is the principle of least privilege?

### Intermediate

6. Why is it safer to use `sudo` for individual commands rather than routinely working as root?
7. What would you investigate if a user receives a sudo authorization error?
8. Why should application services use dedicated accounts when practical?
9. How can excessive sudo privileges increase security risk?
10. How would you investigate an application permission error without immediately running the application as root?

---

## Conclusion

`sudo` is an important tool for administering Linux systems safely. It allows authorized users to perform privileged tasks without requiring them to operate as root for all their work.

In DevOps, combine carefully managed sudo access with dedicated service accounts, appropriate ownership, and restrictive permissions.

The goal is to keep systems manageable while limiting the impact of mistakes and compromised accounts.

## Further Reading

- [Sudo project documentation](https://www.sudo.ws/docs/)
- [Ubuntu Server documentation: User management](https://documentation.ubuntu.com/server/how-to/security/user-management/)