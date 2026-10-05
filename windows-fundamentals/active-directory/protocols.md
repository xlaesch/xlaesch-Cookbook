### Kerberos, DNS, LDAP, MSRPC

- Kerberos is the default authentication protocol for domain accounts. 
	- Port 88
	- Stateless authentication protocol based on tickets instead of transmitting user passwords over the network. DCs have Key Distribution Center that issues tickets. 
	- A login request to a system creates a ticket with the encrypted ticket. The KDC creates Ticket Granting Ticket (TGT)
	- The TGT is presented to the DC and then a Ticket Granting Service is created. 
	- The TGS is presented to the application.
 ![[Pasted image 20260118150559.png]]

- AD DS uses DNS to allow clients to locate DCs and find to find the IP of other domain joined clients. AD maintains a databse of services running on the network via a service record (SRV). 

- AD uses Lightweight Directory Access Protocol (LDAP). 
	- LDAP Port 389
	- LDAP over SSL uses 636 (LDAPS)
	- LDAP Is the language applications use to communicate with other server that provide directory services.
		- "how systems speak to AD"
	- Similar to the way Apache and HTTP work. Apache is the web server that uses the HTTP protocol. AD is the directory server that uses the LDAP protocol.
- Two authentication types
	- Simple authentication: Includes anonymous authentication, unauthenticated. Username + password to create a BIND request to auth to the LDAP server.
	- SASL authentication: uses other authentication services like Kerberos to bind to the LDAP server. LDAP protocol used to send an LDAP message to the auth service which creates a series of challenges.
	- LDAP authentication messages are sent in cleartext.

- MSRPC is Microsoft's implementation of Remote Procedure Call (RPC), a communication technique used for client-server model-based applications. Essentially lets program execute a function on another system as if it were a local function call.
- AD uses four RPC interfaces
	- `lsarpc` calls the LSA system to perform management on domain security policies
	- `netlogon` windows process used to authenticate users and other services in domain environment
	- `samr` provides management functionality for the domain account database. IT admins use it manage the whole AD essentially. Attackers can use to visually map out the AD network. Orgs should force the registry to only allow admins to perform remote SAM queries.
	- `drsuapi`is the Microsoft API that implements directory replication for a multi-DC environment. Attackers can use it create a copy of NTDS.dit to retrieve password hashes.
