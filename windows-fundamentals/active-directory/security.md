![[Pasted image 20261002124521.png]]Active Directory is a critical component that offers organizations efficiency, security and ease of management.

# Default Accounts

Default Accounts are pre-created by the OS or applications that have a unique username and password (like "Administrator" and "Guest"). They come with basic security settings and frequently used passwords, therefore creating special and need-based accounts is better. These default accounts should be disabled or deleted.

# Authorized Groups

Domain Admins and Enterprise Admins are very powerful groups, so ordinary user accounts should not be in these authorized groups. 

# Audit Policy

Allows to monitor, control and record events on a network. Can be accessed with "Computer Configuration --> Policies --> Windows Settings --> Security Settings --> Advanced Audit Policy Configuration".

# Local Admin Password Solution (LAPS)

Method that enables regular and automatic management of passwords of local admin accounts. 

`Win + R` → `gpmc.msc` → edit the relevant GPO → Computer Configuration → Policies → Administrative Templates → System → LAPS

# Lockout Policy

Locks accounts and prevents access for a certain period of time as a result of incorrect password attempts. This mainly for slowing down brute force attempts. 

Computer Configuration --> Policies --> Windows Settings --> Security Settings --> Account Policies --> Account Lockout Policy

# Secure Admin Workstation (SAW)

Ensures that administrators operations are secured by locking down a computer only for privileged administrative work, by keeping credentials from every day software.

# Service Accounts

Service accounts are used to ensure the proper functioning of systems, applications and services. 
- A separate account should be used for each service or application.
- Minimum privileges needed.
- Passwords should be changed.

# Events

Users activiers table.

![[Pasted image 20261002124539.png]]

Groups Activities Table

![[Pasted image 20261002124548.png]]

# Local Admin Group Membership Control 

No standard user should be in the local admin group.
