---
icon: windows
---

# Principles of Windows

## Useful Commands

### Powershell

`powershell -ExecutionPolicy Bypass` to remove execution restrictions on scripts

`Get-Module -ListAvailable ModuleName` confirm if a module is available

`Import-Module ModuleName` if module is available, import it

`Measure-Object` count the amount of objects returned by a file or command (should be piped)

### Cmd

`where /r C:\\ filename` recursive search in the C:\\

`icacls` to view file permissions

## Key Concepts

### Security Account Manager (SAM)

A Microsoft Windows database that contains local account information such as usernames and passwords. The SAM database stores these details in an encrypted format to make them harder to be retrieved. Moreover, it can not be read and accessed by any users while the Windows operating system is running.

### Local Security Authority Subsystem Service (LSASS)

A WIndows process that handles the operating system security policy and enforces it on a system. It verifies logged in accounts and ensures passwords, hashes, and Kerberos tickets. The process is ran as `SYSTEM` .

### Windows Credential Manager

A Windows feature that stores logon-sensitive information for websites, applications, and networks. Contains usernames, passwords, and internet address.

## Users

#### User Accounts

* `Administrator` : full control over the computer to install software, change settings, and manage other user accounts.
* `Standard User` : run applications, browser internet and manage own files and settings but cannot perform actions that other users or the security of the system.

#### Built-In Accounts / NT AUTHORITY

* `SYSTEM/LocalSystem`: perform internal tasks, full access and higher privileges than Administrator
* `Local Service`: minimum privileges. Performs anonymous connections over the network.
* `Network Service`: minimum privileges. Uses computer credentials to authenticate the network.

{% hint style="info" %}
Network in this case means network resources (like file shares or databases) that have granted permissions to that specific computer account or anonymous/unauthenticated users (rare in secure environments).
{% endhint %}

## Active Directory

In Windows Active Directory environments, a **domain** is a logical grouping of computers and users. It centralizes identity and access management. Each user in the domain has a login in the form of: `DOMAIN\username`&#x20;

**Relative Identifier:** used to assign unique identifiers to user and group objects. Some standard RIDs are 500 (Administrator), 501 (Guest), 512 (Domain Administrator), 513 (Domain User), 514 (Domain Guest). Typical user accounts are in the 1000+

### Lightweight Directory Access Protocol (LDAP)

A lightweight protocol for managing directory services that is used in Microsoft AD.

### NTLM Authentication

A legacy authentication protocol used by Windows network.

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

1. The client sends an authentication request to the server they want to access.
2. The server generates a random number and sends it as a challenge to the client.
3. The client combines his NTLM password hash with the challenge (and other known data) to generate a response to the challenge and sends it back to the server for verification.
4. The server forwards both the challenge and the response to the Domain Controller for verification.
5. The domain controller uses the challenge to recalculate the response and compares it to the initial response sent by the client. If they both match, the client is authenticated; otherwise, access is denied. The authentication result is sent back to the server.
6. The server forwards the authentication result to the client.

### Kerberos Authentication

A current authentication protocol used by Windows network.

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

2. the ticket granting ticket (TGT) is encrypted using the krbtgt accounts' password hash(⇒domain secret hash, no one has it except domain), it also contains a copy of the session key so the key distribution center (KDC) doesn’t need to store session key, it can recover it from the TGT
3. Ticket Granting Service (TGS) allow connection to the specific service for which they were created (⇒ included in TGS request, called the Service Principal Name SPN)
   1. it’s encrypted using the service owner hash (use or machine account under which the service runs)
   2. TGS is then decrypted with the same service owner hash (configured password) and validate session key

{% hint style="info" %}
Examples of Kerberos-enabled services include databases, printers, LDAP, E-mail servers. etc
{% endhint %}
