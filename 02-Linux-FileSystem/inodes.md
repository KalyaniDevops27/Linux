# Linux Inodes

## Introduction

An inode (index node) is a data structure used by many Linux filesystems to store metadata about a filesystem object.

An inode contains information such as:

- File type
- Permissions
- Owner and group
- File size
- Timestamps
- References to the file's data blocks or other storage structures, depending on the filesystem

A filename is not stored as part of the inode itself. A directory entry associates a filename with an inode number.

Understanding inodes helps with Linux filesystem troubleshooting, hard links, disk usage, and file management.

---

## 1. How an Inode Works

Think of a filesystem as a library:

- The filename is the book's label.
- The inode is the record describing the book.
- The file data is the book's content.
- The directory maps the filename to its inode.

When a process opens a file, Linux resolves its pathname through directory entries to locate the corresponding filesystem object.

Conceptually:

```text
Filename
   |
   v
Directory entry
   |
   v
Inode / filesystem metadata
   |
   v
File data
```

This is a simplified model. The exact internal implementation depends on the filesystem.

---

## 2. Display an Inode Number

Create a practice file:

```bash
mkdir inode-demo
cd inode-demo

echo "Learning Linux inodes" > notes.txt
```

Display the inode number:

```bash
ls -i notes.txt
```

Example output:

```text
123456 notes.txt
```

The number `123456` is an example. Your system will display its own inode number.

Display more metadata:

```bash
stat notes.txt
```

List inode numbers for files in the current directory:

```bash
ls -li
```

**Important:** An inode number is meaningful within its filesystem. The same number may exist on another filesystem.

---

## 3. Filename vs. Inode

A filename is a name used to access an object through a directory. An inode represents the underlying filesystem object and its metadata.

Renaming a file on the same filesystem normally changes its directory entry without changing its inode.

For example:

```bash
mv notes.txt study-notes.txt
ls -li study-notes.txt
```

The inode number will normally remain the same after this simple rename.

---

## 4. Inodes and Hard Links

A hard link is another directory entry that points to the same inode.

Create one:

```bash
ln study-notes.txt notes-copy.txt
ls -li study-notes.txt notes-copy.txt
```

Both names should display the same inode number.

The two filenames refer to the same underlying file, so changes made through either name affect the same content.

### Important points

- Hard links to a file share the same inode.
- Removing one hard link removes that directory entry, not necessarily the file's data.
- The data can be reclaimed when no directory links or open references remain.
- Hard links normally cannot cross filesystem boundaries.
- Creating hard links to directories is generally restricted.

Check the link count:

```bash
stat study-notes.txt
```

The link count indicates how many hard links refer to that inode.

---

## 5. Inodes and Symbolic Links

A symbolic link stores a target pathname. It is a separate filesystem object with its own inode.

Create one:

```bash
ln -s study-notes.txt notes-shortcut.txt
ls -li study-notes.txt notes-shortcut.txt
```

The symbolic link and its target should have different inode numbers.

Inspect the link:

```bash
readlink notes-shortcut.txt
```

A symbolic link can point to a file or directory, including a target on another filesystem. If its target is renamed or deleted, the link may become broken.

---

## 6. What Is Inode Exhaustion?

Filesystems have a finite supply of inodes. If the filesystem runs out of available inodes, it may be unable to create additional files—even when some disk space remains free.

This can happen when an application creates a very large number of small files, such as:

- Temporary files
- Cache entries
- Application session files
- Small log or queue files

Check filesystem space:

```bash
df -h
```

Check inode usage:

```bash
df -i
```

Interpretation:

- `df -h` reports storage capacity and usage.
- `df -i` reports inode capacity and usage.

A filesystem with available storage but nearly 100% inode usage may be experiencing inode exhaustion.

---

## 7. DevOps Troubleshooting Example

Imagine a Linux server cannot create new temporary files.

First, inspect filesystem storage and inode usage:

```bash
df -h
df -i
```

If inode usage is at 100%, investigate directories that may contain excessive numbers of small files.

For example, inspect file counts within a known application cache directory:

```bash
find /var/cache/myapp -xdev -type f | wc -l
```

Replace `/var/cache/myapp` with the actual directory being investigated. This command counts regular files beneath that directory while staying on the same filesystem.

Before removing files, confirm what the application owns and whether the files are safe to delete. Avoid deleting system files or active application data without understanding their purpose.

---

## 8. Deleted Files That Still Consume Space

On Linux, removing a filename does not necessarily release its data immediately.

If a process still has the file open, the underlying data can remain allocated until the final open reference is closed.

This explains a common production issue: a large log file is deleted, but disk usage does not decrease.

Investigate carefully with tools such as:

```bash
sudo lsof +L1
```

This can list open files that have been unlinked but remain open. The `lsof` utility may need to be installed, and elevated permissions may be required for a complete view.

If an application is holding a deleted log open, use the application's documented log-reopen or restart procedure as appropriate. Do not terminate production processes without understanding the impact.

---

## 9. Interview Questions

### Q1. What is an inode in Linux?

An inode is a filesystem data structure that stores metadata about a filesystem object and information used to locate its data. Directory entries associate filenames with inode numbers.

### Q2. Is the filename stored inside the inode?

No. The filename is stored in a directory entry that maps the name to an inode.

### Q3. What is the difference between a hard link and a symbolic link?

Hard links refer to the same inode. Symbolic links are separate objects that store a target pathname.

### Q4. What happens to an inode when a file is renamed?

A normal rename on the same filesystem usually changes the directory entry while preserving the inode.

### Q5. Why can a server run out of inodes when disk space is available?

The filesystem may have exhausted its inode capacity, often because it contains a very large number of small files.

### Q6. How do you check inode usage?

Use:

```bash
df -i
```

### Q7. Why might deleting a log file not free disk space?

A process may still have the deleted file open. The data is generally released when no remaining links or open references require it.

--- 

## Key Takeaways

- An inode stores filesystem metadata; it is not the filename.
- Directory entries map filenames to inode numbers.
- Hard links share an inode; symbolic links have their own inode.
- `ls -i` displays inode numbers, `stat` shows metadata, and `df -i` checks inode usage.
- A filesystem can run out of inodes while storage space remains available.
- An unlinked file may continue consuming storage while a process holds it open.

Inodes are a core Linux filesystem concept and are especially useful when diagnosing disk-space issues, link behavior, and file-management problems on DevOps servers.
