# Linux Hard Links vs. Symbolic Links

## Introduction

Linux supports hard links and symbolic links (symlinks). Both allow a file to be accessed through more than one name or path, but they work differently internally.

To understand the difference, remember two concepts:

- **Inode:** Represents a filesystem object and stores its metadata.
- **Directory entry:** Maps a filename to an inode number.

A hard link creates another directory entry for the same inode. A symbolic link is a separate filesystem object whose contents identify a target path.

These concepts are important when managing Linux filesystems, troubleshooting broken paths, and designing deployment structures.

---

## 1. What Is a Hard Link?

A hard link is another filename that refers to the same inode as an existing file.

Create a practice file:

```bash
mkdir links-demo
cd links-demo

echo "Learning Linux links" > original.txt
```

Create a hard link:

```bash
ln original.txt hardlink.txt
```

Compare inode numbers:

```bash
ls -li original.txt hardlink.txt
```

Both filenames should display the same inode number.

### How it works

```text
original.txt ───┐
                ├──> Same inode ───> File data
hardlink.txt ───┘
```

The two filenames refer to the same underlying file. Editing the content through one name changes the content seen through the other.

### Important characteristics

- Hard links share the same inode.
- Removing one name does not remove the underlying data while another hard link or open reference still requires it.
- Hard links normally cannot cross filesystem boundaries.
- Creating hard links to directories is generally restricted.
- A hard link does not depend on the continued existence of one special "original" filename.

---

## 2. What Is a Symbolic Link?

A symbolic link, also called a symlink, is a separate filesystem object that stores a target pathname.

Create one:

```bash
ln -s original.txt symlink.txt
```

Inspect it:

```bash
ls -li original.txt symlink.txt
readlink symlink.txt
```

The symbolic link has its own inode, different from the target file's inode.

### How it works

```text
symlink.txt ───> Target path: original.txt ───> File data
```

When software follows the link, Linux resolves the target pathname. The target can be a file or directory and can be located on another filesystem.

### Important characteristics

- A symbolic link has its own inode.
- It stores a target pathname.
- It can point to a file or directory.
- It can cross filesystem boundaries.
- It can become broken if the target is moved, renamed, or deleted.
- Relative target paths are interpreted relative to the directory containing the symbolic link.

---

## 3. What Happens When the Original Filename Is Deleted?

This is one of the most important differences.

### Hard link

If `original.txt` is removed but `hardlink.txt` remains, the content is still accessible through `hardlink.txt`.

### Symbolic link

If `original.txt` is removed, `symlink.txt` still exists as a link object, but it may no longer resolve to a valid target. This is called a broken or dangling symbolic link.

Try it in your practice directory:

```bash
rm original.txt

cat hardlink.txt
cat symlink.txt
```

The hard link should still display the original content. The symbolic link should report that its target cannot be found.

This is a useful demonstration of why hard links and symbolic links behave differently.

---

## 4. Hard Link vs. Symbolic Link

| Feature | Hard link | Symbolic link |
|---|---|---|
| Inode | Shares the target file's inode | Has its own inode |
| Relationship | Another name for the same filesystem object | Points to a target pathname |
| Original filename removed | Other hard links continue to work | Link may become broken |
| Cross-filesystem support | Normally no | Yes |
| Directory links | Generally restricted | Supported |
| Target moved or renamed | Other hard links still refer to the same inode | May break if the stored target path no longer resolves |
| Common uses | Multiple names for the same file | Shortcuts, release paths, shared directory paths |

---

## 5. Useful Commands

Create a hard link:

```bash
ln original.txt hardlink.txt
```

Create a symbolic link:

```bash
ln -s original.txt symlink.txt
```

Display inode numbers and link information:

```bash
ls -li
```

Display the target pathname of a symbolic link:

```bash
readlink symlink.txt
```

Inspect metadata:

```bash
stat original.txt
stat -L symlink.txt
```

The `-L` option makes `stat` follow a symbolic link and report the target's metadata when the target exists. Without it, `stat` normally reports the link itself.

Check whether the symbolic link resolves to an existing file or directory:

```bash
test -e symlink.txt && echo "Target exists" || echo "Target missing"
```

---

## 6. DevOps Example: Application Release Symlink

Many deployment designs use versioned release directories and a symbolic link that points to the active release.

Example layout:

```text
/opt/myapp/
├── releases/
│   ├── v1/
│   └── v2/
└── current -> releases/v2
```

The application or service configuration can use a stable path such as `/opt/myapp/current`, while the symlink identifies the active release.

Inspect it:

```bash
ls -l /opt/myapp/current
readlink /opt/myapp/current
```

A deployment process may update `current` when promoting a release. For reliable deployments, the process should also consider atomic link updates, application reloads, health checks, permissions, and rollback procedures.

If an application fails to find its configuration after a deployment, check the symbolic link and its target before assuming the file itself is missing.

---

## 7. Interview Questions

### Q1. What is a hard link?

A hard link is another directory entry referring to the same inode as an existing filesystem object.

### Q2. What is a symbolic link?

A symbolic link is a separate filesystem object that stores a target pathname.

### Q3. What is the main difference between hard links and symbolic links?

Hard links share the same inode. Symbolic links have their own inode and point to a target path.

### Q4. What happens when the original filename is deleted?

Other hard links remain usable as long as the underlying object remains available. A symbolic link may become broken if its target path no longer resolves.

### Q5. Can a symbolic link point to a directory?

Yes. Symbolic links can point to files or directories.

### Q6. Can a hard link cross filesystem boundaries?

Normally, no. A hard link refers to an inode within its filesystem, while a symbolic link can store a path to another filesystem.

### Q7. Where might a DevOps engineer use symbolic links?

A deployment may use a stable path such as `/opt/myapp/current` to point to the active versioned release directory.

---

## Key Takeaways

- Hard links provide multiple names for the same inode.
- Symbolic links are separate objects that store target paths.
- Removing one hard-linked name does not necessarily remove the file data.
- Removing or moving a symlink's target can leave the link broken.
- `ls -li`, `readlink`, and `stat` are useful for troubleshooting.
- Symbolic links are common in application layouts and release-based deployments.

Understanding links helps prevent filesystem mistakes and makes Linux deployment troubleshooting more predictable.