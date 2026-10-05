Windows users and groups have different privileges and duties. Administrator accounts have full access to system resources and settings and can manage other users.

# Identify the Current User

```powershell
# identify the account accessing the system
whoami
```

> Output uses `domain\username`; a standalone host shows the hostname in place of the domain.

# Local Users

Open **Start → Computer Management → Local Users and Groups → Users** to manage users. Default users:

![[Pasted image 20261002115609.png]]

Users should have only the authority they need, with a limited number of administrator accounts.

# Groups

Groups simplify management of users and resources. Local groups manage resources on one computer; domain groups manage resources across computers on a network.

Default groups on Windows Server:

![[Pasted image 20261002115920.png]]

# User Management

These functions are also available through **Windows + R → `lusrmgr.msc`**.

```powershell
# list users on the system
net user

# display details for a specific user
net user <user>

# view password settings and logon restrictions
net accounts
```

# Group Management

```powershell
# list local groups
net localgroup

# view users in a group
net localgroup <group>
```
