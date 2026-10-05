

### Threat actors 

Entity that has an impact on the safety of another entity

characteristics:
- internal / external
- resources / funding
- level of sophistication/capability

nation states
- external entity, government
- commonly referred to as Advanced Persistent Threat (APT)
- Highest sophistication

unskilled actors
- runs pre-made scripts without limited knowledge
- motivate by the hunt: disruption
- can be internal or external
- not very sophisitcated

hacktivist
- a hacker with a purpose motivated by philosophy
- often an external entity
- can be remarkably sophisticated

insider threat
- motivated by revenge or financial gain
- uses extensive resources against themselves
- medium level of sophistication with institutional knowledge

organized crime
- motivated by money
- very sophisticated
- crime that's organized
- lots of capital

shadow IT
- working the internal IT organization
- shadow IT is unencumbered by typical IT roadblocks

## 2.2
### Common Threat Vectors

a method used by the attack, attack vectors

message-based vectors
- most successful threat vector
- phishing attacks

image-based vectors
- some image formats can be execute code

file-based vectors
- more than executables
- for example an Adobe PDF can contain other objects

voice call vectors
- vishing, voice phishing
- spam over IP
- war dialing to find unpublished phone numbers

removable device vectors
-  get around the firewall
- the USB interface

vulnerable software vectors
- client-based with infected executable
- known (or unknown) vulnerabilities
- agentless as well (web-based applications)

unsupported systems vectors
- manufacturer no longer provides patching
- e.g. outdated operating systems
 
 unsecure network vectors
 - wireless
	 - outdates security protcols
 - wired with unsecure interfaces
 - bluetooth

open service ports
- most network-based services connect over a tcp or udp port
- every port is an opportunity for an attacker

default credentials

supply chain vectors
- tamper with underlying infrastructure
- managed service providers (MSP) can be attacked too
- e.g. counterfeit networking devices

### Phishing

social engineering with a touch of spoofing
business email compromise
- we trust email sources
- spoofed email addresses

typosquatting
- a type of URL hijacking

smishing
- SMS phishing

### Watering hole attack
attacker will poison a 3rd party service that you use and then attack through that (they poising the water)

need layered defense

## 2.3
### Memory Injections

Malware runs in memory
- memory forensics can find  the malicious code
- malware runs in its own process
- malware can also inject itself into another process
- add code into the memory of an existing process
	- allows for same rights and permissions

Dynamic-Link Library (DLL) injection
- attackers inject a path to a malicious DLL in the target process
- the target process will reference that DLL and load it into memory

### Buffer Overflows
overwriting a buffer of memory and spills over into other memory areas
to mitigate developers need to perform bounds checking, anyone writing into that memory address only writes 8 bytes and not anything else

### Race Condition

Time-of-check to Time-of-use attack (TOCTOU)
- check the system
- when do you use the results of your last check
- something might happen between the check and the use

### Malicious Updates
an attacker may embed malicious code into the update itself

mitigations
- backup
- trusted sources
- validate update file with digital signature

### Operating System Vulns

OS is a very big target
Windows we have patch tuesday
the patch may require testing before deployment

### SQL injection
Code injection is where attacker includes their own piece of code into an application
enabled because of bad programming
- the application should properly handle input and output.

Structure Query Language (SQL)
- most common relational database management system language
SQLi
- put your own requests into an existing application
- your application shouldn't allow this
- we can view everything in database

### Cross-site scripting (XSS)
originally called cross-site because information from one site could be shared with another

usually based around javascript attacks

persistent (stored) XSS stack
- an attack will post a message to a social network
- persistent because it's stored on the website
- every person visiting that page will have the JS run

### Hardware vulnerabilities

firmware is the operating system of the hardware device
- manufacturers are the only who can fix their hardware

end-of-line (EOL) notice mean manufacturers stops selling a product but they may continue supporting the product

end of service life (EOSL) same as above but without support

### Virtualization vulnerabilities

VMs can appear everywhere and the resources between VMs vary greatly

VMs is self-contained so it's hard to escape but it's possible and the attacker can get access to host OS

resource reuse
- hypervisor manages relationship between physical and virtual resources
- resources can be reused between VMs
	- data can inadvertently be shared between VMs where RAM is allocated and shared between VMs

### Cloud-specific vulnerabilities
Denial of Service (DoS)

Authentication bypass
- takes advantage of faulty authentication
Directory traversal (faulty config)
Remote Code Execution

### Supply Chain Vulnerabilities
Attackers can infect any step along the way
- raw materials, suppliers, etc.

you can't always control a service providers
- they can have internal access to services
- consider ongoing audits of security providers

hardware providers
- can we trust all our network devices?
- strict controls over policies and procedures
- ideally limit trust of hardware

software providers
- digital signature validation
- how secure are the updates?
- open source is not immune

### Misconfiguration Vulnerabilities

open permissions will leave hackers with an open door

unsecured admin accounts
- disable direct login to the root account
- protect accounts

insecure protocols
- some protocols aren't encrypted
- verify with a packet capture
- use encrypted versions

default settings
- every application and network has a default login

open ports and services
- managed with a firewall allow or deny based on port number
- firewall rulesets can be complex

### Mobile device security
jailbreaking/rooting to gain access to the operating system and install custom firmware

sideloading can be a trojan horse
- users install 3rd party apps

### Zero-day vulnerability

Attackers search for unknown vulnerabiltiies
the vendor has no idea the vulnerability exsits
- an attack without a patch or method of mitigation

## 2.4
### Malware overview

Viruses, Worms, Ransomware, etc.
Malware often works together with other pieces of malware
Your computer must run a program

Ransomware
- data is unavailable until you provide cash
- malware encrypt data files 
- the OS keeps working but nothing else

protecting against ransomware
- offline backup ideally so it's not targeted by the 

### Viruses and Worms

Malware that can reproduce itself
Most common malware

program viruses are part of applications
boot sectors viruses
script viruses, OS/browser engine
macro viruses

fileless virus is a stealthy attack that operates in memory without being installed in a file or application

worms are malware that self-replicates without needing you to do anything and uses the network as a transmission medium
- blocked by IDS/IPS and firewalls


### Spyware and Bloatware

spyware is malware that spies on you

bloatware is often included on the OS on a new computer/iphone that includes application you didn't expect. Included by the manufacturer.
- any applications can be exploited


### Other Malware Types

Keyloggers save all keystroke input and send to attackers. Keystrokes are in plaintext
- can also do clipboard logging, screen logging, etc.

Logic bomb waits for a particular event to occur and executes a bomb
- time or data
- user event

Rootkits is originally a Unix technique
- modifies core system files and is usually part of the kernel
- can be invisible to the OS

### Physical Attacks

Someone with physical access to a server has full control of it

Brute force in this context is pushing through obstruction.

RFID cloning is related to access badges and key fobs

Environmental attacks is attacking everything supporting the technology
- power monitoring
- HVAC (Heating, Ventilation, and Air Conditionig)
- Fire suppression

### Denial of Service

force a service to fail by overloading the service
can be used as a smokescreen

Distrbuted Denial of Service (DDoS)
- launch an army of computers to bring down a service by using all the bandwith or resources
- Through botnets (millions of computers at your command)

### DNS attacks

Dns poisoning
- modify the DNS server but can be very difficult
- more typical to modify the client host file (this file takes precedent over DNS queries)
- or man in the middle attack to redirect dns queries

domain hijacking
- get access to domain registration to redirect traffic

url hijacking / typosquatting
- use a similar domain to send to redirect to somewhere else

### Wireless Attacks

wireless deauthentication
- DoS to the network

802.11 management frames are responsible for how to access points, (dis)associate, etc.
- originally lacked any security
- fixed in the 802.11ac

Radio Frequency (RF) jamming
- DoS attack
- transmit interfering wireless signals
- the receiving device can't hear the good signal

for wireless jamming need to be somewhere close

### On-path attack
also know as Man-in-the-Middle (MitM) attack
- a device sits in between you and your destination and redirects your traffic

ARP poisoning/spoofing
- on the local IP subnet
- ARP protocol has no security so relatively easy

on-path browser attack
- malware on the same computer as victim acts as a proxy to redirect traffic
- known as man in the browser attack

### Replay Attack
Needs access to raw network data and uses this data to pretend be the victim

Pass the hash
- password hash is captured by attacker computer
- then sends his own authentication request

can be blocked by encryption and new salting for each authentication request

browser cookies and session IDs can be captured
- session IDs are often stored in cookies

session hijacking (sidejacking)
- using the victim's session ID to gain access

### Application Attacks
Cross-Site Request Forgery (CSRF)
- one-click attack
- takes advantage of the trust that a web application has for the user, forge the requests made by the web application for the attacker

Directory traversal
- read files from a web server that are outside the website's file directory

### Cryptographic attacks

Hash collision where the same hash value is returned for two different plaintexts

downgrade attack
- instead of using perfectly good encryption force the system to downgrade their security
- SSL stripping
	- combines on-path with a downgrade attack
	- strips the S away from HTTPS

### Indicators of compromise (IOC)
an event that indicates an intrusion

concurrent session usage is a type of IoC, multiple account logins from multiple locations

out-of-cycle logging patching and activity occurring at unexpected times

### Segmentation and Access control

physical, logical or virtual segmentation
= devices, VLANs, virtual networks

Access Control Lists (ACLs)
- allow or disallow traffic
- e.g. restrict access to network devices

application allow list / deny list
- on windows can be done via application hash, publisher certificates, path
- allow list
	- nothing runs unless approved
- deny list
	- nothing on bad list can be executed

### Mitigation Technniques

- patching
- encryption
	- full disk encryption (FDE)
- least privilege
	- rights and permissions should be bare minimum
- configuration enforcement
	- performing a posture assessment
- decommissioning
	- remove storage devices before throwing away information

### Hardening Techniques

- system hardening
	- updates
	- user accounts
- encryption
- Endpoint Detection and Response (EDR)
	- detecting a threat
	- beyond just signatures
	- behavioral analysis
	- can responds to threats
- host-based firewall
	- software-based firewall
- Host-based Intrusion Presentation System (HIPS)




