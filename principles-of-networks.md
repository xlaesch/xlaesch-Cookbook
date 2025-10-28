---
icon: wifi
---

# Principles of Networks

## Firewalls

Security measure against unauthorized connection attempts from external networks. Monitors network traffic between firewall and incoming data connections and decides how to handle the connection based on the rules that have been set.
## IDS/IPS

Intrusion Detection System (IDS) scans for potential attacks, analyzes them, and reports any detected attacks; usually based on pattern matching and signatures. Intrusion Prevention System (IPS) compliments takes specific measures if a potential attack should have been detected.
### Key Concepts

**Subnetworks:**

* Network segment: group of devices connected using a shared medium (e.g. ethernet switch, Wi-Fi access point)
* Subnetwork: equivalent of one or more network segments connected together using the same router.
  * depends on [CIDR notation](https://www.shellhacks.com/cidr-notation-explained-examples/)

Firewall: hardware or software that permit packets to pass through or blocks them. Basics firewalls relies of rules and examines IP header and transport layer header, more complex examines data within

IDS: intrusion detection system, inspects network packets for specifics behaviors or content signatures

**Socket:** One endpoint of a two-way communication link between two programs running on a network. A software interface that allows application send and receive data. Combination of the an IP address and a port number, there must be a socket on both the client and the server.

**SOCKS proxy:** a general-purpose proxy server that relays any kind of traffic (TCP and sometimes UDP) between a client and a destination server, without interpreting the traffic.

#### OSI, TCP/IP Chart

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Port forwarding is when the router is configured to forward incoming traffic on a specific port (on its WAN/public IP) to a specific device and port on the LAN.

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### Protocols

#### Server Message Block (SMB, port 445)

Protocol that provides shared access to files, printers, and other network resources via the Kerberos and NTLM authentication protocol.

#### Telnet (port 43)

Application Layer protocol used to connect to a virtual terminal, the communication itself is not encrypted.

#### HTTP (port 80)

Clear text protocol, that can be communicated with a telenet or netcat client to act as a web browser.

#### File Transfer Protocol (FTP, port 21)

Transfer of files between systems efficient. Sent in clear text and can be accessed with an `ftp` client. Uses TCP connect.

#### Email Delivery

Email Delivery is composed of several components:

* Mail Submission Agent (MSA) responsible for receiving message and checks for errors. Forwards message to the MTA.
* Mail Transfer Agent (MTA) responsible for sending the message to the recipient. Route the e-mail throought the internet.
* Mail Delivery Agent (MDA), responsible for placing the e-mail into the user's mailbox.
* Mail User Agent (MUA) responsible for loading the client to connect to the MSA and sending message or receiving messages.

All this information needs to be propagated via protocol.

* SMTP (port 25) is responsible for the MTA portion.&#x20;
* POP3 (port 110) is responsible for MDA portion.
* IMAP (port 143) is the more sophisticated version of POP3 allowing message states to be synchronized across multiple devices.

#### Transport Layer Security (TLS)

<figure><img src=".gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

1. Client hello ⇒ tells server with cryptography algos it knows
2. ServerHello ⇒ tells client which algo it choose among other things. provides cert if server auth is required
3. ClientKeyExchange ⇒ contains additional info required to generate master key, switches to encrypted format
4. switches to encryption using master key provided

public certificates (signed by authorities) are used to ensure that who we are communicating with is actually truly them. the root store of our browser or OS contains a list of highly trusted CAs and certs, in simple terms⇒compares hashes

How Certificates are used in SSL/TLS:

* You type `https://example.com`.
* The server sends its **SSL/TLS certificate**.
* Your browser checks:
  * Is it issued by a **trusted CA**? (Browsers come with a preinstalled list.)
  * Is it **still valid** (not expired or revoked)?
  * Does the **domain match** the one in the certificate?
* If all checks pass:
  * The browser uses the **public key in the certificate** to begin the handshake (key exchange, etc.)
  * You now have a **secure encrypted connection.**

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

#### Secure Shell (SSH, port 22)

* authenticates with username and password + private and public key
* connect with `ssh username@MACHINE_IP`
* everything sent over encrypted channel
* for the first time, need to confirm fingerprint of SSH server’s public key
  * no 3rd party to check if public key is valid (certificate), so we need to check if its valid
* can copy over files over SSH with `SCP` (secure copy protocol)
  * `scp username@MACHINE_IP:filepath path_to_save`&#x20;

#### Domain Name System (DNS, port 53)

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* Local DNS Server: The server your computer asks first. It does the recursive lookup for you and caches the answers.
* Root Server: The starting point. It directs queries to the correct Top-Level Domain (TLD) server.
* TLD Server: Manages a specific extension (like `.com` or `.org`) and points to the domain's authoritative server.
* Authoritative Server: The final source of truth that holds the actual DNS records for a specific domain.

**Virtual Hosts**

DNS doesn't know about virtual hosts. Its only job is to point multiple domain names to a single IP address.

When your browser connects to that IP, it sends a Host header specifying which website it wants (e.g., Host: site-a.com). The web server reads this header to determine which site's content to serve.

