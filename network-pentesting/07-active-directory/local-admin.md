---
tags: [ad, windows, local-admin]
aliases: [Local Admin]
---
Once we gain a foothold in the domain, our goal shifts to advancing our position further by moving laterally or vertically to obtain access to other hosts. We must however, first have local admin rights on some of our hosts to do so.

# Remote Desktop

If we have control of a local admin user on a given machine, we will be able to access it via RDP. We may also find a user that does not have local admin rights but does have rights to RDP into one or more machines.

```powershell
# enumerate the remote desktop users group on a given host via PowerView
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"
```

If we gain access to an account, we can check group membership in Bloodhound via the `Node Info --> Execution Rights`.

There are also some queries such as `Find Workstations where Domain Users can RDP` or `Find Servers where Domain Users can RDP`.

# WinRM

We may find that either a specific user or an entire group has WinRM access to one or more hosts.

```powershell
# enumerating the remote management users group on a given host via powerview
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Management Users"
```

Similarly we can use a Cypher query in Bloodhound.

```cypher
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2
```

Once established we can establish our winRM session.

```powershell
$password = ConvertTo-SecureString "Klmcargo2" -AsPlainText -Force
$cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\forend", $password)
Enter-PSSession -ComputerName ACADEMY-EA-MS01 -Credential $cred
```

Similarly on Linux.
```shell
evil-winrm -i 10.129.201.234 -u forend
```

# SQL Server Admin

It is common to find user and service accounts setup with sysadmin privileges on a given SQL server instance. 

We can find this type of access via the SQLAdmin edge in Bloodhound.
```cypher
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:SQLAdmin*1..]->(c:Computer) RETURN p2
```

We can authenticate to the account found like so

```powershell
Import-Module .\PowerUpSQL.ps1
Get-SQLInstanceDomain

# authenticate against remote SQL server host and run queries
Get-SQLQuery -Verbose -Instance "172.16.5.150,1433" -username "inlanefreight\damundsen" -password "SQL1234!" -query 'Select @@version'
```

Similarly, from a Linux host.

```shell
mssqlclient.py INLANEFREIGHT/DAMUNDSEN@172.16.5.150 -windows-auth
```

## Related

- [[credential-enumeration]]
- [[groups|Windows Groups]]
- [[rdp-attacks]]
- [[other|Other Privesc]]

