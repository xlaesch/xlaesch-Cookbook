---
tags: [enum, linux, ssh]
aliases: [SSH]
---
Secure Shell (SSH) allows two computers to establish an encrypted and direct connection on port 22. Can be configured to allows connections from specific clients. SSH-2 is the commonly used version, that blocks the MitM attacks that SSH-1 was vulnerable to.

Has 6 authentication mechanisms:
- Password authentication
- Public-key authentication
- Host-based authentication
- Keyboard authentication
- Challenge-response authentication
- GSSAPI authentication

When the SSH server and client authenticate themselves to each other. The server sends its public host key to the client, which the client uses to verify the server's identity. A 3rd party can interpose themselves between the two participants only during the initial contact and intercept that. A host key cannot be imitated because it is unique public-private key pair. The attacker cannot forge the private key's signature without access to it.

After authentication, the client must prove to the server that it has access authorization. The SSH server has the hash value of the password set for the desired user so the user has to enter the password to log on to another server.

The private key is a created individually for the users own computer and secured with a passphrase. It is stored on our own computer and never shared.

Public keys are also stored on the server. The server creates a "cryptographic problem" with the client's public key and sends it to the client, and the client decrypts the problem with the private key.

# SSH Audit

Use `ssh-audit` to check client-side and server side configs.

```shell
# Checks client-side and server side configs
./ssh-audit.py 10.129.14.132
```

# Verbose Login

Use verbose SSH output when testing authentication methods or troubleshooting access.

```shell
# change auth method
ssh -v cry0l1t3@10.129.14.132
```

# Dangerous SSH Settings

The sshd_config has the configuration settings for the server. Some dangerous setting scan be:

|**Setting**|**Description**|
|---|---|
|`PasswordAuthentication yes`|Allows password-based authentication.|
|`PermitEmptyPasswords yes`|Allows the use of empty passwords.|
|`PermitRootLogin yes`|Allows to log in as the root user.|
|`Protocol 1`|Uses an outdated version of encryption.|
|`X11Forwarding yes`|Allows X11 forwarding for GUI applications.|
|`AllowTcpForwarding yes`|Allows forwarding of TCP ports.|
|`PermitTunnel`|Allows tunneling.|
|`DebianBanner yes`|Displays a specific banner when logging in.|

# Private Key Handling

Recovered private keys often fail to load on first use. Two checks resolve the common errors.

SSH refuses to use a private key that is readable by group/other:

```shell
# fix "Permissions 0644 for 'id_rsa' are too open"
chmod 600 id_rsa
```

Keys that were copy-pasted (e.g. out of a web UI or notes) frequently gain leading whitespace or Windows carriage returns, which break parsing with `error in libcrypto: unsupported`:

```shell
# strip leading whitespace and trailing CR
sed -i 's/^[[:space:]]*//; s/\r$//' id_rsa
chmod 600 id_rsa
```

Verify OpenSSH can parse the key before trying to use it:

```shell
# prints the public key on success, errors on failure
ssh-keygen -y -f id_rsa
```

An encrypted OpenSSH-format key (header `-----BEGIN OPENSSH PRIVATE KEY-----`, typically `aes256-ctr` + `bcrypt`) requires a passphrase separate from the Linux account password. Crack it offline:

```shell
ssh2john id_rsa > id_rsa.hash
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.hash
```

## Related

- [[network-services]]
- [[lateral-movement-and-pivoting]]
- [[password-spraying]]
- [[double-hop]]

