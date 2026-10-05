
NTFS file systems uses 3 types of file links.

# Hard Links

A directory entry that points to the same file on disk. File-system representation of a file by which more than one path references a single file in the same volume. Any changes made to a hard-linked file are instantly visible to application that access it through the links that reference it. 

Hard links can't reference directories, only files, and they can't reference files on different volumes.

# Junctions

Implemented through reparse points. Allows a directory to appear to exist at another location. Happens inside the file system so application don't know they are traversing a junction. Or soft link are storage objects that reference separate directories. A junction can link link directories located on different local volumes on the same computer. Otherwise, junctions operate identically to hard links. 

To create a junction you must have write access to the parent.

```powershell
# 
mklink /J C:\Windows\Tasks\Uploads\a07a66adb5e7c7190a377f987ad29bcb C:\xampp\htdocs
```
## Reparse Point

A reparse point is an NTFS filesystem object attribute. It is not a file type or a directory type.

Every file or directory in NTFS is represented by an **MFT (Master File Table) record**, which contains a set of typed attributes:
- `$STANDARD_INFORMATION`
- `$FILE_NAME`
- `$DATA`
- `$INDEX_ROOT` (for directories)
- etc.

It effectively tells Windows "Stop here and do something special instead of treating this like a normal file or folder."
# Symbolic Links

Stores a path to another file or directory.