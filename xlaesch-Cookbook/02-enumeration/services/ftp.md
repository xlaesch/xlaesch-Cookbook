---
tags: [enum, ftp]
aliases: [FTP]
---
The File Transfer Protocol (FTP) runs on the application. In an FTP connection, two channels are opened, the client and server establish a control channel through **TCP port 21** then both participants establish the data channel via **TCP port 20**.

Two variants exist:
* `active` where the client established the connection. Often blocked by firewall blocking all incoming connections to server.
* `passive` where the server announces a port through which the client can establish the data channel.

# Anonymous Login

Anonymous access is the fastest check because it may expose downloadable files or writable directories.

```shell
# anonymous login
ftp $HOST
```

# Session Inspection

Use FTP client status, debug, and trace output when the server behavior or transfer mode matters.

```shell
# status
ftp> status

# detailed output(debug and packet trace)
ftp> debug
ftp> trace
```

# Download Files

Use `get` for one file, or mirror anonymous FTP content when anonymous access is allowed.

```shell
# download file
ftp> get $FILE

# download all available files
wget -m --no-passive ftp://anonymous:anonymous@$HOST
```

# Upload Files

Writable FTP directories are worth testing because they may enable web shells, staged payloads, or data transfer.

```shell
# upload a file
ftp> put $FILE
```

# TLS Check

If FTP is running FTP TLS/SSL, use `openssl` to inspect the encrypted service.

```shell
# if FTP is running FTP TLS/SSL
openssl s_client -connect $HOST:21 -starttls ftp
```

# vsFTPd Configuration Clues

`vsFTPd` is the most common distribution of FTP. The config `/etc/vsftpd.conf` file reveals the settings. As well as `/etc/ftpusers` revealing the denied users.

| **Setting**                                                   | **Description**                                                                                          |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `listen=NO`                                                   | Run from inetd or as a standalone daemon?                                                                |
| `listen_ipv6=YES`                                             | Listen on IPv6 ?                                                                                         |
| `anonymous_enable=NO`                                         | **(DANGEROUS)** Enable Anonymous access?                                                                 |
| `local_enable=YES`                                            | Allow local users to login?                                                                              |
| `dirmessage_enable=YES`                                       | Display active directory messages when users go into certain directories?                                |
| `use_localtime=YES`                                           | Use local time?                                                                                          |
| `xferlog_enable=YES`                                          | Activate logging of uploads/downloads?                                                                   |
| `connect_from_port_20=YES`                                    | Connect from port 20?                                                                                    |
| `secure_chroot_dir=/var/run/vsftpd/empty`                     | Name of an empty directory                                                                               |
| `pam_service_name=vsftpd`                                     | This string is the name of the PAM service vsftpd will use.                                              |
| `rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`          | The last three options specify the location of the RSA certificate to use for SSL encrypted connections. |
| `rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key` |                                                                                                          |
| `ssl_enable=NO`                                               |                                                                                                          |
| (optional) `anon_upload_enable=YES`                           | Allowing anonymous to upload files?                                                                      |
| (optional) `anon_mkdir_write_enable=YES`                      | Allowing anonymous to create new directories?                                                            |
| (optional) `no_anon_password=YES`                             | Do not ask anonymous for password?                                                                       |
| (optional) `anon_root=/home/username/ftp`                     | Directory for anonymous.                                                                                 |
| (optional) `write_enable=YES`                                 | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE?                       |

## Related

- [[ftp-attacks]]
- [[rdp]]
- [[miscellaneous]]
- [[06-post-exploitation/credential-access/credential-hunting|Credential Hunting]]

