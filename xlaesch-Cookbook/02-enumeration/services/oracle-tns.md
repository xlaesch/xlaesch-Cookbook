---
tags: [enum, linux, acl]
aliases: [Oracle TNS]
---
Oracle Transparent Network Substrate (TNS) server is a communication protocol that allows for communication between Oracle databases and application over networks. TNS can use multiple networking protocols.

Default configuration depends heavily on the version and edition installed on the system. By default the connections are accepted on the TCP/1521 port. Listeners by default only accept connections from authorized hosts and have some basic authentication. Oracle TNS is heavily connected into the Oracle ecosystem.

Databases or services has a unique `tnsnames.ora` file that contains the information necessary to connect to the service.

`listener.ora` is a server-side configuration file that defines the listener process's properties and parameters, responsible for receiving incoming client requests and forwards them to the oracle database instance.

Oracle RDBMS, an SID is a unique name that identifies a unique database instance.

# TNS Port Scan

Start by confirming the listener on TCP/1521.

```shell
# scan the Oralce TNS port
sudo nmap -p1521 -sV 10.129.204.235 --open
```

# SID Brute Force

Brute force SIDs before connecting because the SID identifies the database instance.

```shell
# SID bruteforcing
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

# ODAT Enumeration

Use ODAT when you want broad Oracle database checks from one tool.

```shell
# enumerate ALL information
./odat.py all -s 10.129.204.235
c
```

# Database Connection

Connect once you have credentials and SID/service name.

```shell
# connect to the Oracle database
sqlplus scott/tiger@10.129.204.235/XE

# Database enumeration
sqlplus scott/tiger@10.129.204.235/XE as sysdba
```

# File Upload

If the servers runs a web server, and we know the root directory of it. We can upload a web shell.

```shell
# Upload files
./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
```

## Related

- [[firewall-and-ids-evasion]]
- [[smb-attacks]]
- [[sql-attacks]]
- [[rdp]]

