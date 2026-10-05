---
tags: [enum, linux]
aliases: [R-Services]
---
R-services are a suite of services hosted to enable remote access or issue commands between Unix hosts over TCP/IP. Transmits information in plaintext, and have mostly been phased out by SSH. Span across ports 512, 513, and 514. Most commonly found in commercial operating systems.

Access control is weak. Relies on trusted information sent from the remote client to the host machines usually via Pluggable Authentication Modules (PAM) for user authentication.

| **Command** | **Service Daemon** | **Port** | **Transport Protocol** | **Description**                                                                                                                                                                                                                                                            |
| ----------- | ------------------ | -------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rcp`       | `rshd`             | 514      | TCP                    | Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the `cp` command on Linux but provides `no warning to the user for overwriting existing files on a system`.        |
| `rsh`       | `rshd`             | 514      | TCP                    | Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files for validation.                                                                                                                 |
| `rexec`     | `rexecd`           | 512      | TCP                    | Enables a user to run shell commands on a remote machine. Requires authentication through the use of a `username` and `password` through an unencrypted network socket. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files. |
| `rlogin`    | `rlogind`          | 513      | TCP                    | Enables a user to log in to a remote host over the network. It works similarly to `telnet` but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.                                     |

# Service Scan

Scan the R-services ports together.

```shell
# Scanning
sudo nmap -sV -p 512,513,514 10.0.17.2
```

# Remote Login

Use `rlogin` when trusted-host access is allowed.

```shell
# logging in
rlogin 10.0.17.2 -l htb-student
```

# User Listing

Use `rwho` to list users visible through R-services.

```shell
# list users
rwho
```

## Related

- [[rdp]]
- [[rdp-attacks]]
- [[network-services]]
- [[rsync]]

