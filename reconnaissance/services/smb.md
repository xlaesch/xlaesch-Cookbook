# Theory

Server Message Block (SMB) is a client-server protocol that regulates access to files and entire directories and other network resources like printers, etc. SMB uses TCP to establish a connection from both and govern the transport of data. SMB server is comprised of shares, which can be configured to accept different access rights defined by Access Control List (ACL).

Samba is the Unix-based implementation of SMB. 

---
# Practice

```shell
# listing server shares (-L) with a null session (-N)
smbclient -N -L //$HOST

# connecting to a share
smbclient //$HOST/$SHARE

# from the administrative POV, we can check connections
smbstatus
```
### Useful Scripts
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
### RPCClient
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
