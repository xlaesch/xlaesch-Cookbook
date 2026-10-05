---
tags: [enum, linux, nfs]
aliases: [NFS]
---
Network File System (NFS) is a similar system to SMB using a completely different protocol for Unix systems. As of, `NFSv4` the user must authenticate (older version authenticated the client computer), it is also ran on a single port `2049`. NFS protocol shifts authentication to the `ONC-RPC` protocol on port `111`, using `UID/GID` and group memberships.

# Export Configuration

When you have local access to an NFS server, inspect configured exports and reload changes.

```shell
# view configured NFS shares
cat /etc/exports

# add a directory
echo '$DIRECTORY  $HOST/24(sync,no_subtree_check)' >> /etc/exports
systemctl restart nfs-kernel-server
exportfs
```

Possible options for the hosts/subnets include:

| **Option**         | **Description**                                                                                                                                             |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`               | **(DANGEROUS)** Read and write permissions.                                                                                                                 |
| `ro`               | Read only permissions.                                                                                                                                      |
| `sync`             | Synchronous data transfer. (A bit slower)                                                                                                                   |
| `async`            | Asynchronous data transfer. (A bit faster)                                                                                                                  |
| `secure`           | Ports above 1024 will not be used.                                                                                                                          |
| `insecure`         | **(DANGEROUS)** Ports above 1024 will be used.                                                                                                              |
| `no_subtree_check` | This option disables the checking of subdirectory trees.                                                                                                    |
| `root_squash`      | **(DANGEROUS)** Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents `root` from accessing files on an NFS mount. |

# NFS Port Footprinting

Use Nmap against both RPC and NFS ports.

```shell
# footprinting at the nfs ports
sudo nmap $HOST -p111,2049 -sV -sC

# run all nfs scripts on nfs ports
sudo nmap --script nfs* $HOST -sV -p111,2049
```

# Show and Mount Shares

Mount discovered shares locally so permissions and file ownership can be reviewed.

```shell
# show available nfs shares
showmount -e $HOST

# mounting NFS share
mkdir target-NFS
sudo mount -t nfs $HOST:/ ./target-NFS/ -o nolock

# to list contents with UIDs and GUIDs
ls -n mnt/nfs/

# unmount share
sudo umount ./target-NFS
```

A common escalation strategy with NFS, would be to upload a shell to an NFS share that has the `SUID` of that users and then run the shell via the SSH user. (Assuming initial SSH connection).

## Related

- [[miscellaneous]]
- [[smb-attacks]]
- [[r-services|R-Services]]
- [[rsync]]

