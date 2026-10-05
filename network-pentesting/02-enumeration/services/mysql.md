---
tags: [enum, linux, sql]
aliases: [MySQL]
---
MySQL is a SQL relational database system developed and supported by Oracle. Works with a client-server principle with one MySQL server and MySQL clients.

Clients retrieve and edit the data using queries to the database engine.

# Nmap Script Scan

Use MySQL NSE scripts to identify version and service details before logging in.

```shell
# nmap scan an SQL server
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

# Login Checks

Test for no-password access first, then known credentials.

```shell
# login to the server without password
mysql -u root -h 10.129.14.132

# with password
mysql -u root -pP4SSw0rd -h 10.129.14.128
```

# Database Navigation

Use these commands after login to identify databases, tables, columns, and target data.

```shell
mysql> show databases;
mysql> use <database>;
mysql> show tables;
mysql> show columns from <table>;
mysql> select * from <table>;
mysql> select * from <table> where <column> = "<string>";
```

# Misconfiguration Clues

MySQL is suited for dynamic websites, where efficient syntax and high response speed are essential. Some examples of misconfigured MySQL servers:

|**Settings**|**Description**|
|---|---|
|`user`|Sets which user the MySQL service will run as.|
|`password`|Sets the password for the MySQL user.|
|`admin_address`|The IP address on which to listen for TCP/IP connections on the administrative network interface.|
|`debug`|This variable indicates the current debugging settings|
|`sql_warnings`|This variable controls whether single-row INSERT statements produce an information string if warnings occur.|
|`secure_file_priv`|This variable is used to limit the effect of data import and export operations.|

## Related

- [[sql-attacks]]
- [[sql-basics]]
- [[mssql]]
- [[mysql-injection]]

