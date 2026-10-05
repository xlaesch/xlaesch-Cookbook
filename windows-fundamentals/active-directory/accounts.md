### User and Machine Accounts

- Users are created on both local and AD environments.
- When logged in, the system verifies password and creates an access token.
	- The token describes security content of a process and includes user's security identity.
	- Whenever a user interacts with a process the token is presented.

Local accounts are stored on a particular system. They are considered security principals but can only manage access to and secure resources on a standalone host. 
1. `Administrator` has SID `S-1-5-domain-500` it has almost full control over every resource on the system. 
2. `Guest` disabled by default. Allows users without an account to log in temporarily on the account with limited access rights.
3. `SYSTEM` or `NT AUTHORITY\SYSTEM` used by the OS to perform internal functions. Profile does not exist for this account but it has permissions over almost everything. Highest level of permissions on the system.
4. `Network Service` predefine local used by Service Control Manager (SCM) for running Windodws services. It will present credentials to remote services for whichever service runs with this context.
5. `Local Service` another SCM managed local account. Has minimal privileges and presents anonymous credentials to the network.

Domain Users are granted access to domain resources such as file, servers. printers, intranet hosts, etc. 
	- `KRBTGT` is a specific domain user that is built-in to AD and is responsible for KDC. Target for many attacks.

User naming attributes:
1. `userPrincipalName` primary logon name for the user
2. `ObjectGUID` Unique ID for the user. Never changes even if user is deleted.
3. `SAMAccountName` logon account name that supports previous clients and servers.
4. `ObjectSID` the SID of the user. Identifies a user and its group
5. `sIDHistory` previous SIDs of the user.
