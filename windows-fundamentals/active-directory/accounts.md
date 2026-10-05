Users exist in both local and Active Directory environments. Local accounts secure resources on a standalone host, while domain users access domain resources such as files, servers, printers, and intranet hosts.

# Authentication and Access Tokens

At logon, the system verifies the password and creates an access token. The token describes a process's security context, includes the user's security identity, and is presented when the user interacts with a process.

# Local Accounts

Local accounts are stored on a particular system and are considered security principals.

| **Account** | **Description** |
| --- | --- |
| `Administrator` | SID `S-1-5-domain-500`; almost full control over every resource on the system. |
| `Guest` | Disabled by default; allows temporary logon without an account, with limited access rights. |
| `SYSTEM` (`NT AUTHORITY\SYSTEM`) | Used by the OS for internal functions. Has no profile and has the highest permission level, with access to almost everything. |
| `Network Service` | Predefined local account used by the Service Control Manager (SCM) to run Windows services; presents credentials to remote services. |
| `Local Service` | SCM-managed local account with minimal privileges; presents anonymous network credentials. |

# Domain Accounts

`KRBTGT` is a built-in domain user responsible for the Key Distribution Center (KDC) and is a target for many attacks.

# User Naming Attributes

| **Attribute** | **Description** |
| --- | --- |
| `userPrincipalName` | Primary logon name. |
| `ObjectGUID` | Unique user ID; never changes, even if the user is deleted. |
| `SAMAccountName` | Logon account name supporting older clients and servers. |
| `ObjectSID` | User SID; identifies the user and its group. |
| `sIDHistory` | Previous user SIDs. |
