---
tags: [enum, linux, rsync]
aliases: [rsync]
---
Rsync is a tool for locally and remotely copying files. Uses an algorithm that minimizes that amount of data transmitted over the network when a version of the file already exists on the destination host. By default it uses port 873 and can be configured to use SSH for secure file transfers.

# Rsync Port Scan

Check whether rsync is exposed.

```shell
# Does rsync exist
sudo nmap -sV -p 873 127.0.0.1
```

# Probe Accessible Shares

Use netcat to probe the rsync service directly.

```shell
# probing for accessible shares
nc -nv 127.0.0.1 873
```

# List Open Share

Enumerate an open share without transferring files.

```shell
# enumerating an open share
rsync -av --list-only rsync://127.0.0.1/dev
```

## Related

- [[rdp]]
- [[r-services|R-Services]]
- [[06-post-exploitation/file-transfer/linux|Linux]]
- [[nfs]]

