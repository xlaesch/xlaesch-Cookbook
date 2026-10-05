
## 4.1
### Hardening targets
- no system is secure with default configurations

mobile device manager (MDM)
- manages mobile devices and harden
- centralize management
- set policies on apps, data, camera, etc.

### Securing Wireless and Mobile

Site surverys
- Determine existing wireless landscape
- Identify access poitns
- work around existing frequencies
- heat maps identify wireless signal strengths

Wireless survey tools
- show signal coverage
- interference

Bring your own device (BYOD)
- Employee owns the device
- needs to meet company's requirements
- Difficult to secure

### Securing Wireless Networks

WPA2 PSK problem
- brute-force problem
- Listen to the four-way handshake 
	- derive the PSK hash without the handshake
- Capture the hash
- Attacker can brute force the Pre-Shared Key (PSK)

WPA3 and GCMP
- Wifi Protected Access 3 (WPA3)
- GCMP block cipher mode is stronger than WPA2
- Changes the PSK authentication process
	- mutual authentication
	- creates a shared session key
- Simultaneous Authentication of Equals (SAE)
	- everyone uses a different session key, even with the same PSK

Pre-Shared Key (PSK)
- The shared Wi-Fi password
- everyone uses the same 256-bit kley

In corporate environments (WPA-3 Enterprise)
- we use 802.1X allows everyone to have separate credentials

AAA framework
- Identification is usually your username
- AAA triad

Remote Authentication Dial-In User Service (RADIUS)
- One of the more common AAA protocols 
- centralizes authentication for users
- RADIUS services are available for almost any operating system.

### Application Security

Secure coding concepts
- input validation
	- normalization, fix any data with improper input
	- test with fuzzing
- secure cookies
	- can have secure attribute set which only sends it over HTTPs
- Static Application Security Testing (SAST)
	- checks for buffer overflows, etc.
	- can't find everything (e.g insecure cryptography)
- Code signing
	- integrity and who wrote the code
	- uses a CA
- Sandboxing
	- applications cannot access unrelated resources
	- 

## 4.2

### Asset Management

Assginment
- Associate a person with an asset
	- useful for tracking
- Classification
	- Hardware (capital expenditure = can depreciate)
	- Software (operating expenditure)

Physical destruction that destroys drives
- 3rd parties are often used and provide a certificate of destruction

data retention
- backup your data

## 4.3

### Vulnerability scanning
Usually minimally invasive (e.g. port scan)
- should be tested from the outside and inside

Fuzzing
- looking for the application to act out of the ordinary given a random input
- fuzzing engines and framework are very time and processor resource heavvy

Package monitoring
- Some applications are distributed in a package
- Confirm legitimacy

### Threat intelligence

Research the threats and threacts actors

Open-source intelligence (OSINT)
- publicly available sources
- government data

3rd party intellignece
- someone else compiles threat info
- threat intelligence services (analytics, correlation)

Cyber Threat Alliance (CTA)
- information sharing organization
- members upload specifically formatted threat intelligence
- CTA scores each submission and validates
- other members can extract validated data

Dark web intelligence
- hacking groups and services

### Penetration Testing
Simulate an attack
Try to exploit the vulnerabilities

 Rules of Engagement
 - formal list of rules that defines purpose and scope
 - type of testing and schedule  (what hours, internal/external)

Exploiting vulnerabilities
- try to break into the system
- can cause Denial of Service

Process
1. Initial exploitation
2. Lateral movement
3. Persistence
4. The pivot 
	- Gain access to systems that would normally not be accessible, use a system as a proxy

### Analyzing Vulnerabilties

False positives
- vulnerability is identified but doesn't exist

Common Vulnerability Scoring System (CVSS) 
- enhanced feed sharing and automation

Common Vulnerabilities and Exposures (CVE)
- vulns can be cross-referenced online

Exposure Factors
- percentages that signifies loss of value or business activity if the vulnerability is exploited.

Environmental variables
- what type of env is associated with this vulnerability?

Risk tolerance
- amount of risk acceptable to an organization
	- it's impractical to remove all risk

### Vulnerability Remediation

Patching
- most common mitigation
- scheduled vulnerability patch notices
- unscheduled patches
	- e.g. zero day

Insurance
- Cybersecurity Insurance coverage
	- lost revenue
	- data recovery costs
	- money lost to phishing
	- privcacy lawsuit costs

Compensating controls (e.g. disable the problematic service, revoke access to the application)

### Security Monitoring
24/7
Monitory all entry points

Log aggregation
- SIEM (Security Information and Event Manager)
	- consolidate many logs to a central database (servers, firewalls)

### Security Tools

Security Content Automation Protocol (SCAP)
- many different tools can identify the same vulnerability
- allows tool to identify and acts on the same criteria via SCAP
- can automate the detection and removal of vulnerabilities

Benchmarks
- Apply security best-practices to everything

Agents can usually provide more details
- always monitoring

agentless runs without a formal install
- performs the check then dissapears

Data Loss Prevention (DLP)
- stop the data before the attackers get it
- more than one single appliance

**Simple Network Management Protocol (SNMP)**
- A protocol used to **monitor and manage** network devices (routers, switches, servers, printers, etc.).
- The device exposes management data through a structured “database” called a **Management Information Base (MIB)**
    - A **MIB** is basically a **catalog of variables** (metrics and settings) that SNMP can read (and sometimes write).

Netflow
- Gather traffic statistics from all traffic flows
- Has a probe and collector that watches network communication and the summary records

### Firewalls

Appliance that sits in-line network and filters
can run other services
- encrypt traffic
- VPN

most firewalls are layer 3 devices (routers)

### Web Filtering

Content filtering
- control traffic based on data within the content (e.g. URL filtering, ...)

can be agent based
can also proxy based that sits between the users and the external network


### OS security

Active Directory
- A database of everything on the network
- Manages authentication
- Centralized Access Control

Group Policy
- Manage the computers or users with group policies

Security-Enhanced Linux (SELinux)
- Security patches for the linux kernel
- Adds Mandatory Access Control (MAC)
- Limits application access

### Email Security

It's easy to spoof an email

Mail Gateway
- evaluates the source of inbound email messages
- blocks it at the gateway before it reaches the user
- usually in screened subnet (network segment that sits between an external firewall and internal firewall so nothing reaches the LAN)

Sender Policy Framework (SPF)
- which servers authorized send emails for a domain
- added to DNS TXT records

Domain Keys Identified Mail (DKIM)
- A mail server digitally signs all outgoing mail
- the public key is in the DKIM DNS TXT record
- the signature is validated by the receiving mail servers

Domain-Based Message Authentication, Reporting, and Conformance (DMARC)
- extension of SPF and DKIM
- The domain owner decides what receiving email servers should do with emails not validating using SPF and DKIM
- compliance reports are sent to the email admin

### Monitoring Data

File Integrity Monitoring (FIM)
- Some filed change some files should NEVER change
- we should monitor if important OS files change
- Windows - SFC (System File Checker)
- Linux - Tripwire

### Endpoint Security
Endpoint is the user's access

Edge vs access control
- Edge is where the inside of the netowork meets the outside
- access control limit access to data, inside or out

posture assessment
- via persistent agents
	- permanently installed on system
- dissolvable agents
	- no installation is required 
- agentless NAC
	- integrated with AD
	- checks made during login and logoff

If failed posture assessment, quarantine the network

Endpoint detection and response (EDR)
- extend signature functionality of antivirus to behavioral analysis, machine learning...
- response can be automated

Extended Detection and Response (XDR)
- evolution of EDR
- more than a single agent that interprets data from multiple sources/systems
- includes user behavior analytics

### Identity and Access Management (IAM)
- give right permissions to the right people and the right time
- access control
- authentication and authorization
- identity governance
- starts with provisioning user accounts and ends with de-provisioning
- identify proofing
	- verify the user is who they are

Single sign-on (SSO)
- provide credentials one time and gain access to all the resources
- usually limited by time

Lightweight Directory Access Protocol (LDAP)
- Protocol for reading and writing over an IP network
- used to query and update an X.500 directory (used in Windows AD, Apple OpenDirectory)

X.500 Directory Information Tree
- hierarchical structure
- container objects

Security Assertion Markup Language (SAML)
- open standard for authentication and authorization
- authenticate through a 3rd party to gain access
- no designed for mobile apps

Oauth
- Determines what resources a user will be able to access = authorization
- not an authentication protocol

Federation
- provide network access to others
- authenticate and authorize between the two organizations
- e.g. login with your facebook credentials

### Access Controls

policy enforcement of authorization

Least privilege
- rights and permissions should be set to the bare minimum

Mandatory Access Control (MAC)
- the operating system limits the operation on an object
- every object gets a label

Discretionary Access Control (DAC)
- as the owner, you control who has access
- used in most operating systems
- each individual users must create the appropriate access

Role-based access control (RBAC)
- administrators provide access based on the role of the user

Rule-based access control
- access is determine through system-enforced rules

Attribute-based access control (ABAC)
- access may be based on many different criteria
- combine and evaluate multiple parameters
	- resource information, IP address, time of day, ...

### Password Security

Make your password strong
- password age and expiration
- password complexity (length)
- password managers

Just-in-time permissions
- grand admin access for a limited time
	- principle of least privilege
- accounts are temporary

## 4.8
### Incident Response

NIST SP800-61
- incident response lifecycle

### Digital Forensics

Legal hold
- technique to preserve relevant information
- custodians are instructed to preserve data
- separate repository for the electronically stored information (ESI)

Chain of custody
- hashes and digital sigs to maintain integrity of data

1. Acquisition
2. Reporting (document how data was acquired)
3. Preservation (how the data is going to be stored)
4. E-discovery (work together with digital forensics to provide it for court data)



