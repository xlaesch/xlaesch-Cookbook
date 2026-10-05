---
tags: [enum, linux, imap]
aliases: [IMAP/POP3]
---
Internet Message Access Protocol (IMAP) allows access to emails from a mail server. IMAP is a network protocol for the online managements of emails on a remote server in a filesystem-like way. POP3 on the other hand provides listing, retrieving, and deleting emails as functions at the email server.

Clients will usually access these structures and create local copies. Clients establish connection via port **143**, and communicates in ASCII format. IMAP also allows creating personal folders and structures in the mailbox. By default, IMAP is unencrypted but encrypted variants exist on port **143 or 993**.

Most companies use 3rd party email providers, but if they use their own mail servers, they may have misconfigured their service.

# POP3 and IMAP Script Scan

Use this to identify exposed POP3/IMAP services and default script output across cleartext and TLS ports.

```shell
# scan POP3/IMAP
sudo nmap $HOST -sV -p110,143,993,995 -sC
```

# Authenticated IMAPS Access

Use curl when you have credentials and need to confirm mailbox access.

```shell
curl -k 'imaps://$HOST' --user user:p4ssw0rd -\
```

# TLS Connections

Use `openssl` to inspect encrypted IMAP or POP3 services.

```shell
#TLS encrypted connection to IMAP or POP3
openssl s_client -connect $HOST:pop3s
openssl s_client -connect $HOST:imaps
```

# Misconfiguration Clues

| **Setting**               | **Description**                                                                           |
| ------------------------- | ----------------------------------------------------------------------------------------- |
| `auth_debug`              | Enables all authentication debug logging.                                                 |
| `auth_debug_passwords`    | This setting adjusts log verbosity, the submitted passwords, and the scheme gets logged.  |
| `auth_verbose`            | Logs unsuccessful authentication attempts and their reasons.                              |
| `auth_verbose_passwords`  | Passwords used for authentication are logged and can also be truncated.                   |
| `auth_anonymous_username` | This specifies the username to be used when logging in with the ANONYMOUS SASL mechanism. |

## Related

- [[email-attacks]]
- [[smtp]]
- [[smb]]
- [[ldap]]

