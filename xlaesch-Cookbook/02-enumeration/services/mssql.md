---
tags: [enum, linux, sql]
aliases: [MSSQL]
---
Microsoft SQL is a closed-source database management system. SQL Server Management Studio (SSMS) is a client side database management application. It can often hold saved credentials. Other clients exist as well but are not as popular.

SQL Service will run as `NT SERVICE\MSSQLSERVER` and authentication is defaulted to Windows Authentication which means the Windows OS is responsible for authentication.

# Nmap Script Scan

Use the MSSQL NSE scripts to check configuration, empty passwords, `xp_cmdshell`, tables, access, and hash dumping.

```shell
# MSSQL script scan
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```

# Metasploit Scanner

Metapsloit also has auxiliary scanner.

```shell
mssql_ping
```

# Remote Interaction

`mssqlclient.py` allows remote interaction with SQL service.

```shell
python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth
```

# Database Defaults and Risk Clues

Databases have this default structure:

|Default System Database|Description|
|---|---|
|`master`|Tracks all system information for an SQL server instance|
|`model`|Template database that acts as a structure for every new database created. Any setting changed in the model database will be reflected in any new database created after changes in the model database|
|`msdb`|The SQL Server Agent uses this database to schedule jobs & alerts|
|`tempdb`|Stores temporary objects|
|`resource`|Read-only database containing system objects included with SQL server|

Some common dangerous settings we see is:
- MSSQL clients not using encryption.
- self-signed certificates (can be spoofed)
- The use of [named pipes](https://docs.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
- Weak & default `sa` credentials. Admins may forget to disable this account.

## Related

- [[sql-attacks]]
- [[mysql]]
- [[sql-basics]]
- [[network-services]]

