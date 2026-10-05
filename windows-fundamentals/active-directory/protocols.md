Kerberos, DNS, LDAP, and MSRPC support authentication, service discovery, directory access, and remote operations in Active Directory.

# Kerberos

Kerberos is the default authentication protocol for domain accounts and uses port `88`. It is a stateless, ticket-based protocol that avoids transmitting user passwords over the network; domain controllers contain the Key Distribution Center (KDC) that issues tickets.

1. A logon request creates an encrypted ticket; the KDC creates a Ticket Granting Ticket (TGT).
2. The TGT is presented to the domain controller, which creates a Ticket Granting Service (TGS).
3. The TGS is presented to the application.

![[Pasted image 20260118150559.png]]

# DNS

AD DS uses DNS to locate domain controllers and resolve the IP addresses of domain-joined clients. Service records (SRV) maintain information about services running on the network.

# LDAP

Lightweight Directory Access Protocol (LDAP) is the language applications use to communicate with directory servers: how systems speak to AD. AD uses LDAP in the same way that Apache uses HTTP.

| **Protocol** | **Port** |
| --- | --- |
| LDAP | `389` |
| LDAP over SSL (LDAPS) | `636` |

## Authentication Types

| **Type** | **Description** |
| --- | --- |
| Simple | Includes anonymous and unauthenticated authentication; a username and password create a BIND request to authenticate to the LDAP server. |
| SASL | Uses other authentication services, such as Kerberos, to bind to the LDAP server. LDAP sends a message to the authentication service, which creates a series of challenges. |

> LDAP authentication messages are sent in cleartext.

# MSRPC

Microsoft Remote Procedure Call (MSRPC) lets client-server applications execute a function on another system as if it were a local function call.

| **Interface** | **Description** |
| --- | --- |
| `lsarpc` | Calls the LSA system to manage domain security policies. |
| `netlogon` | Windows process that authenticates users and services in a domain environment. |
| `samr` | Manages the domain account database. Administrators use it to manage AD; attackers can use it to map the network. Restrict remote SAM queries to administrators through the registry. |
| `drsuapi` | Directory replication API for environments with multiple domain controllers. Attackers can use it to copy `NTDS.dit` and retrieve password hashes. |
