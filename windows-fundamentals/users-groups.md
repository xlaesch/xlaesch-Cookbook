
Privileges and duties of users and group on Windows system differ. 

```cmd
whoami
```

Tells use which user account is accessing the system. With format `domain\username`. If the host is not included in the domain, the hostname will be shown instead of the domain.

Administrator accounts have full access to all system resources and settings, and can manage other user accounts.

We can manage users in Start --> Computer Management --> Local Users and Groups --> Users. Default users are:

![[Pasted image 20261002115609.png]]

Users should have as little authority as necessary, and there should be a limited amount of Administrator accounts. 
# Groups

Groups make it easier to manage users and system resources. There are local groups used to manage resources on a specific omputer and domain groups to manage across multiple computers on the network. These are the default groups on a Windows Server

![[Pasted image 20261002115920.png]]
# User Management

```shell
net user
```

Displays the username within the system.

```
net user LetsDefend
```

Displays details for a specific user.

```
net accounts
```

See the configurations related to password usage and logon restrictions.

```
net localgroup

# view users in a group
net localgroup Administrators
```

Change things for the groups on the system.

All these functions are available with "Windows + R" and `lusrmgr.msc`