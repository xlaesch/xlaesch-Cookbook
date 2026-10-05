NTFS supports three types of file links: hard links, junctions, and symbolic links.

# Hard Links

A hard link is a directory entry that points to the same file on disk. Multiple paths reference a single file on the same volume, and changes are visible through each link.

> Hard links reference files, not directories, and cannot reference files on different volumes.

# Junctions

Junctions use reparse points to make a directory appear at another location. Applications traverse them through the file system without knowing they are following a junction; they can reference directories on different local volumes on the same computer. Otherwise, junctions operate identically to hard links.

Write access to the parent directory is required.

```powershell
# create a junction; write access to the parent is required
cmd /c mklink /J "<junction_path>" "<target_directory>"
```

## Reparse Points

A reparse point is an NTFS file-system object attribute, not a file or directory type. It tells Windows to stop and handle the object specially rather than treating it as a normal file or folder.

Each file or directory has a Master File Table (MFT) record containing typed attributes, including:

```text
$STANDARD_INFORMATION
$FILE_NAME
$DATA
$INDEX_ROOT (for directories)
```

# Symbolic Links

A symbolic link stores a path to another file or directory.
