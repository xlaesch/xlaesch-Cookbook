---
tags: [enum, windows, smb, impacket]
aliases: [SMB]
---
Server Message Block (SMB) is a client-server protocol that regulates access to files and entire directories and other network resources like printers, etc. SMB uses TCP to establish a connection from both and govern the transport of data. SMB server is comprised of shares, which can be configured to accept different access rights defined by Access Control List (ACL).

Samba is the Unix-based implementation of SMB.

# Null Session Share Listing

Start with a null session because open shares quickly identify accessible data and potential credential material.

```shell
# listing server shares (-L) with a null session (-N)
smbclient -N -L //$HOST
```

# Connect to a Share

After identifying a share, connect directly and browse the contents.

```shell
# connecting to a share
smbclient //$HOST/$SHARE
```

# Local SMB Status

From the administrative POV, we can check connections.

```shell
# from the administrative POV, we can check connections
smbstatus
```

# Automated Share and RID Enumeration

Use these when manual null-session checks are slow or you need broader share, RID, and host detail.

```shell
# Brute forcing RIDs via Impacket script
impacket-samrdump $HOST

# share enumeration via smbmap
smbmap -H $HOST

# share enumeration via crackmapexec
crackmapexec smb $HOST --shares -u '' -p ''

# full enumeration script
enum4linux-ng $HOST -A
```

# RPCClient

Use `rpcclient` when null sessions are accepted and you want domain, share, and user information from RPC.

```shell
# rpcclient null session
rpcclient -U "" $HOST

# rpcclient server enumeration
srvinfo
enumdomains
querydominfo
netshareenumall
netsharegetinfo $SHARE

# rpcclient user enumeration
enumdomusers
queryuser $RID
```

# Default Shares

The shares below exist on every Windows host (`NETLOGON` and `SYSVOL` only on DCs). A `--shares` listing showing only defaults means the account is a plain domain user with no access to custom shares.

| **Share** | **Description** |
|---|---|
| `ADMIN$` | Remote Admin; maps to `C:\Windows`. Local admins only — psexec-style lateral movement drops a service binary here. |
| `C$` | Root of the `C:\` drive. Local admins only — full filesystem pillaging (SAM/SYSTEM hives, DPAPI) after escalation. |
| `IPC$` | Named-pipe endpoint for RPC, not a filesystem share. Required for authentication, `samr`/`lsarpc` enumeration, and the Service Control Manager channel psexec uses. |
| `NETLOGON` | DC-only logon share; clients fetch logon scripts from here. Mine for `.bat`/`.ps1` scripts with hardcoded credentials. |
| `SYSVOL` | DC-only, domain-wide replicated store for GPOs and scripts. First share to pillage on a DC: hunt `cpassword` values in GPP XML (`Groups.xml`, `ScheduledTasks.xml`) and decrypt with `gpp-decrypt`; check scripts for embedded creds. |

> READ on `IPC$`, `NETLOGON`, and `SYSVOL` is granted to any authenticated domain user by default — it signals nothing about privilege.

## Related

- [[smb-attacks]]
- [[password-spraying]]
- [[credential-enumeration]]
- [[snmp]]

