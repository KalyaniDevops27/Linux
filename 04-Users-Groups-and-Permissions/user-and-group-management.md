# Linux User and Group Management

## 1. Introduction

Linux is a multi-user operating system. Multiple people and applications can use the same server, but they should not all have the same level of access.

Linux manages this through **users, groups, user IDs (UIDs), and group IDs (GIDs)**.

As a DevOps engineer, user and group management is important when configuring application accounts, controlling server access, managing deployment permissions, and following the principle of least privilege.

---

## 2. What is a user?

A user represents a person or an account used by a service or application.

Examples:

- `devuser` — a developer account.
- `devopsuser` — an account used by a DevOps engineer.
- `appuser` — an account used to run an application.

Each Linux user has a UID, which the operating system uses to identify that account.

Check the current user:

```bash
whoami
```

Display the current user's UID, primary group, and supplementary groups:

```bash
id
```

Inspect a specific account:

```bash
id devuser
```

---

## 3. What is a group?

A group is a collection of users. Groups make it easier to manage permissions for multiple users who need similar access.

For example, a `developers` group could be allowed to access a shared application directory.

Check the groups associated with your current user:

```bash
groups
```

Check a specific user's groups:

```bash
groups devuser
```

Inspect a group's details:

```bash
getent group developers
```

---

## 4. Create a user

The `useradd` command creates a user account.

```bash
sudo useradd -m devuser
```

The `-m` option creates a home directory if needed.

Set the account's password:

```bash
sudo passwd devuser
```

On some distributions, `adduser` provides an interactive way to create an account:

```bash
sudo adduser devuser
```

The availability and behavior of `adduser` vary by Linux distribution. Do not run both examples for the same account; choose the appropriate method for your system.

Verify the account:

```bash
id devuser
getent passwd devuser
```

---

## 5. Modify a user

The `usermod` command changes existing user account properties.

### Add a user to a supplementary group

First, ensure the group exists:

```bash
sudo groupadd developers
```

Add `devuser` to that group:

```bash
sudo usermod -aG developers devuser
```

The options mean:

- `-a`: append to existing supplementary group memberships.
- `-G`: specify supplementary groups.

**Important:** Using `usermod -G developers devuser` without `-a` can replace the user's existing supplementary group list. Use `-aG` when you intend to add a group without removing existing memberships.

Verify the result:

```bash
id devuser
groups devuser
```

Existing login sessions may not immediately reflect new group memberships. Start a fresh login session when necessary.

---

## 6. Delete a user

Use `userdel` to remove a user account:

```bash
sudo userdel devuser
```

This normally removes the account but does not automatically remove its home directory.

To remove the account and its home directory:

```bash
sudo userdel -r devuser
```

Use `-r` carefully. It can remove the user's home directory and mail spool, potentially deleting data you need.

Before deleting a real account, check whether it owns application files, scheduled jobs, running processes, or other resources.

---

## 7. Create and manage groups

### Create a group

```bash
sudo groupadd developers
```

### Modify a group

The `groupmod` command can rename a group:

```bash
sudo groupmod -n engineering developers
```

This changes the group name from `developers` to `engineering`. Review existing files and services that depend on the group name before making such a change.

### Delete a group

```bash
sudo groupdel engineering
```

A group cannot normally be deleted while it is the primary group of an existing user. Check dependencies and memberships before removing a group.

### Verify group information

```bash
getent group engineering
```

---

## 8. Important Linux account files

Linux maintains account and group information in standard system databases. The files below are common local-file sources, though systems using LDAP or other identity services may retrieve information elsewhere.

### `/etc/passwd`

Contains account information such as usernames, UIDs, primary GIDs, home directories, and login shells.

Inspect a particular account:

```bash
getent passwd devuser
```

A local `/etc/passwd` entry commonly contains fields in this order:

```text
username:x:UID:GID:comment:home_directory:login_shell
```

The `x` typically indicates that the password hash is stored separately rather than in this file.

### `/etc/shadow`

Stores password hashes and password-aging information for local accounts on typical Linux systems. Access is restricted because this data is security-sensitive.

Do not publish or casually copy its contents.

### `/etc/group`

Contains local group definitions and supplementary group membership information.

Inspect a group:

```bash
getent group developers
```

A typical entry has this structure:

```text
group_name:x:GID:member1,member2
```

The primary group relationship is recorded in the user's account information and may not appear in the group's member list.

---

## 9. Primary and supplementary groups

A Linux user has a primary group, identified by the user's primary GID.

A user can also belong to supplementary groups that provide additional access.

Inspect the relationships with:

```bash
id devuser
```

For example, a developer might belong to a primary group and also be a member of `developers` and `docker`.

Group membership does not automatically guarantee access to every file. The file or directory permissions, ACLs, and application-specific security controls must also allow it.

---

## 10. DevOps example: shared application directory

Suppose an application directory should be accessible to an authorized deployment group.

Create the group and add the required user:

```bash
sudo groupadd appdeploy
sudo usermod -aG appdeploy devuser
```

Inspect the directory:

```bash
ls -ld /srv/app
```

If the directory's group should be `appdeploy`, an administrator can change its group ownership:

```bash
sudo chgrp appdeploy /srv/app
```

Permissions must also be configured to match the intended access. For example, group write access should only be granted when the group genuinely needs it.

After changing group membership, the user may need to start a new login session.

For production systems, review directory ownership, permissions, ACLs, service accounts, and deployment requirements before applying changes.

---

## 11. Security best practices

- Use individual accounts instead of sharing administrator credentials.
- Grant only the permissions each account needs.
- Prefer controlled `sudo` access over unrestricted root access.
- Use dedicated service accounts for applications when appropriate.
- Avoid granting broad access to sensitive directories.
- Review group membership periodically.
- Protect `/etc/shadow` and other sensitive account data.
- Check file ownership before deleting users.
- Use a test environment to learn account-management commands.

---

## 12. Common mistakes

- Using `usermod -G` without `-a` and accidentally replacing supplementary group memberships.
- Assuming a new group membership is visible immediately in every existing session.
- Deleting a user without checking the files and processes associated with that account.
- Assuming group membership alone grants file access.
- Confusing a user's UID with a group's GID.
- Assuming every Linux distribution configures `adduser` identically.

---

## 13. Interview questions

**Q1. What is the difference between a user and a group?**

A user identifies an account. A group collects users so access can be managed collectively.

**Q2. What are UID and GID?**

UID identifies a user, while GID identifies a group.

**Q3. How do you create a user with a home directory?**

```bash
sudo useradd -m devuser
```

**Q4. How do you add a user to a supplementary group without removing existing memberships?**

```bash
sudo usermod -aG developers devuser
```

**Q5. What is the purpose of `/etc/passwd`?**

It contains account information such as usernames, UIDs, primary GIDs, home directories, and login shells.

**Q6. What is stored in `/etc/shadow`?**

For typical local accounts, it stores password hashes and password-aging information.

**Q7. What is the difference between `userdel` and `userdel -r`?**

`userdel` removes the account. `userdel -r` also requests removal of the user's home directory and mail spool.

**Q8. How do you inspect a user's group membership?**

```bash
id devuser
groups devuser
```

**Q9. Why are groups useful in DevOps?**

They allow teams to share access to application files and operational resources without assigning permissions separately to every user.

---

## 14. Key takeaway

Linux user and group management is the foundation of access control on a multi-user server. Learn how to create accounts, manage memberships, inspect account information, and verify permissions before applying changes to production systems.

Useful references: `man useradd`, `man usermod`, `man userdel`, `man groupadd`, and `man passwd`.