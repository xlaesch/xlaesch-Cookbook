AD Domain Services (AD DS) gives an organization ways to store directory data and make it available to standard users and admins on the network. Stores information such as usernames and passwords and manages the right needed for authorized users.

# AD Structure

Forests are "containers" of separate domains, users, computers, and other objects all under the same umbrella. AD is a hierarchical database that every user can access regardless of privilege. A basic user can enumerate:

| **Domain Computers** | **Domain Users** |
| ------------------------ | --------------------------- |
| Domain Group Information | Organizational Units (OUs) |
| Default Domain Policy | Functional Domain Levels |
| Password Policy | Group Policy Objects (GPOs) |
| Domain Trusts | Access Control Lists (ACLs) |

- Arranged in hierarchical tree structure, with a forest at the top containing one or more domains (which can also have subdomains themselves).
	- A domain is a structure within which objects (users, computers, groups) are accessible.
	- Domains have Organizational Units (OU)

```text
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
│               └── barbara.jones
├── CORP.INLANEFREIGHT.LOCAL
└── DEV.INLANEFREIGHT.LOCAL
```

- Different domains can have trust relationships (e.g. a company acquires another and needs to absorb the directory)

# Directory Terms

| **Term** | **Description** |
| --- | --- |
| Attributes | Every object is associated with an attribute to define the characteristics of that given object. These have an LDAP name associated for LDAP queries like `displayName` gives `Full Name` |
| Schema | Schema is the blueprint of an enterprise environment. |
| GUID | A global unique Identifier (GUID) is 128-bit value assigned when a domain user or group is created. It is unique across the enterprise. |
| Security principals | Security principles (in AD) are domain objects that can manage access to other resources within the domain. Managed by the Security Accounts Manager (SAM). These are users, groups, and computers that are granted permissions on resources. Each principle is represented by a SID. |
| SID | Security Identifier (SID) is a unique identifier for a security principal or security group. |
| DN | Distinguished Name (DN) describes the full path to an object in the AD (`cn=bjones, ou=IT, ou=employees, dc=inlanrfreight, dc=local`) |
| RDN | Relative Dstinguished Name (RDN) is a single component of a DN that identifies the object in question at the current level in the naming hierarchy. |
| `samAccountName` | samAccountName is the user's logon name |
| `userPrincipleName` | userPrincipleName is another way to identify the users in AD. Composed of prefix (user account name) and a suffix (domain name) `bjones@inlanefreight.local`. |
| FSMO | Flexible Single Master Operations (FSMO) roles give DCs the ability to continue authenticating and granting perms without interruptions. Help replication in AD to run smoothly. |
| Global Catalog | Global Catalog (GC) is a domain controller that stores all objects in an Active Directory forest. |
| RODC | Read-Only Domain Controller (RODC) has a read-only AD database. |
| Replication | Replication happens in AD when AD objects are updated and transferred from one DC to another. |
| SPN | Service Principle Name (SPN) uniquely identifies a service instance. Used by Kerberos auth to associate an instance of a service with a logon account. |
| GPO | Group Policy Object (GPO) are collections of policy settings. Each GPO has a GUID. "how systems and users should be configured" |
| ACL | Access Control List (ACL) is the ordered collection of access control entries (ACEs) that apply to a specific object. "who can access this object and how" |
| ACE | an Access Control Entry (ACE) in an ACL identifies a trustee (user account, group account, or logon session) and lists the access rights that are allowed, denied, or audited. The SID appears in ACEs. |
| DACL | Discretionary Access Control List (DACL) defines which security principles are granted or denied access to an object. If no DACL exists, everyone has access full access to the object. It's the part of the ACL that controls access. |
| SACL | System Access Control List (SACL) allows for admins to log access attempts that are made to secured objects. |
| FQDN | Fully Qualified Domain Name (FQDN) is the complete name for specific computer or host. `[host name].[domain name].[tld]` used to find object location in tree hierarchy or DNS. e.g. `DC01.INLANEFREIGHT.LOCAL` |
| Tombstone | Tombstone is a container object in AD that holds deleted AD objects. Remains for a set period of the Tombstone Lifetime. Works only if Recycle bin is not enabled. Most attributes are stripped. |
| AD Recycle Bin | AD Recycle Bin is where any deleted objects are preserved for a period of time. |
| SYSVOL | SYSVOL folder or shares stores copies of public files in the domain such as system policies, Group Policy settings, logon/logoff scripts etc. It is replicated to all DCs within the environment using File Replication Services (FRS) |
| AdminSDHolder | AdminSDHolder objects is used to manage ACLs for members of built-in groups in AD marked as privileged. Managed via SDProp process that runs on a schedule on the PDC Emulator Domain Controller that checks members of protected groups to ensure that the correct ACL is applied to them. If an attacker creates an ACL to grant a user rights over a member of the Domain Admins Groups, these rights will be removed by SDProp unless they modify other settings within the AD. |
| `dsHeuristics` | dsHeuristics attribute is a string value on the Directory Service object that defines forest-wide configuration settings. it can exclude built-in groups from the Protected Groups list. Protected Groups are protected via the AdminSDHolder object. If a group is excluded from the dsHeuristics then any changes will not be reverted by the SDProp |
| `adminCount` | adminCount attribute determines whether or not the SDProp process protects a user. |
| `sIDHistory` | sIDHistory holds any SIDs that an objects was assigned previously, |
| `NTDS.DIT` | NTDS.DIT is the heart of the AD. It is stored at `C:\Windows\NTDS` and is a database that stores AD data like user and group objects, group membership and **password hashes**. |
| MSBROWSE | MSBROWSE is an older networking protocol that maintains a list of shared printers and files. |
| RID | Relative Identifier (RID) is the last part of a SID unique to only the domain. AD assigns new RID when you create a new object. |

# Types of Objects

An object can be any resource present within the AD

| **Object** | **Description** |
| --- | --- |
| Users | Users are leaf objects so cannot contain any other objects within them. Has a SID and GUID; can have over 800 possible user attributes. |
| Contacts | Contacts (leaf objects) represent an external user and contains information attributes like name, email address. Do not contain security principals. |
| Computers | Computers are leaf objects, have their own security principals and SID and GUID |
| Shared folders | Shared Folders object points to a shared folder on the specific computer where the folder resides. Can be locked down so only specific users can access it. They do not have security principals but have a GUID. |
| Groups | Groups is a container object because it can contain other objects like users, computers and other groups (nested groups). A group is a security principal and has a SID and GUID. Used to assign permissions and access. Does not contain or manage GPOs |
| Organizational Units | Organizational Units (OUs) is a container that system admins can use to store their similar objects for ease of administration. Used for administrative structure. An example would be a top-level OU called Employees with child OUs like marketing, HR, finance, etc. |
| Built-in | Built-in is a container that holds default groups in AD domain. |
| Foreign Security Principal | Foreign Security Principal is an object created in AD to represent a security principal that belong to a trusted external forest. Used when an object from an external forest is added in the current domain. |

# FSMO Roles

| **Roles** | **Description** |
| --- | --- |
| `Schema Master` | This role manages the read/write copy of the AD schema, which defines all attributes that can apply to an object in AD. |
| `Domain Naming Master` | Manages domain names and ensures that two domains of the same name are not created in the same forest. |
| `Relative ID (RID) Master` | The RID Master assigns blocks of RIDs to other DCs within the domain that can be used for new objects. The RID Master helps ensure that multiple objects are not assigned the same SID. Domain object SIDs are the domain SID combined with the RID number assigned to the object to make the unique SID. |
| `PDC Emulator` | The host with this role would be the authoritative DC in the domain and respond to authentication requests, password changes, and manage Group Policy Objects (GPOs). The PDC Emulator also maintains time within the domain. |
| `Infrastructure Master` | This role translates GUIDs, SIDs, and DNs between domains. This role is used in organizations with multiple domains in a single forest. The Infrastructure Master helps them to communicate. If this role is not functioning properly, Access Control Lists (ACLs) will show SIDs instead of fully resolved names. |

# Trusts

Used to establish forest-forest or domain-domain authentication. A trust creates a link between the authentication systems of two domains.

| **Trust Type** | **Description** |
| --- | --- |
| `Parent-child` | Domains within the same forest. The child domain has a two-way transitive trust with the parent domain. |
| `Cross-link` | a trust between child domains to speed up authentication. |
| `External` | A non-transitive trust between two separate domains in separate forests which are not already joined by a forest trust. This type of trust utilizes SID filtering. |
| `Tree-root` | a two-way transitive trust between a forest root domain and a new tree root domain. They are created by design when you set up a new tree root domain within a forest. |
| `Forest` | a transitive trust between two forest root domains. |

# AD Federation Services

AD Federation Services (ADFS) provide SSO to systems and application for users on Windows Server operating systems. Uses claims-based Access Control Authorization model, identifies users by a set of claims related to their identity that are packaged into a security token by the identity provider.

# Group Managed Service Accounts

Group Managed Service Accounts (gMSA) is a secure way of running automated tasks, apps and services that mitigates Kerberoasting.
