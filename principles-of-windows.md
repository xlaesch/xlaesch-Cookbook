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

### NTDS Domain Controller

New Technologies Directory Services (NTDS) is a database containing all Active Directory data, including objects, attributes, credentials, etc. The NTDS.DTS data consists of three tables as follows:

* Schema table: it contains types of objects and their relationships.
* Link table: it contains the object's attributes and their values.
* Data type: It contains users and groups.

### Local Administrator Password Solution (LAPS)

Removed storing encrpyted passwords in the `SYSVOL` folder and now uses two attributes `ms-mcs-AdmPwd` that contains clear-text password of the local administrator and `ms-mcs-AdmPwdExpirationTime` that contains the expiration time to reset the password. LAPS uses `admpwd.dll` to change the local administrator password and update the value of `ms-mcs-AdmPwd`

### (Legacy after 2015) Group Policy Preferences (GPP)

Every Windows OS has a built-in Administrator account which can be accessed by password. When you have a lot of workstations with Windows you can change local administrator accounts using GPP. It allows you to create domain policies with embedded credentials using XML files that are created in the `SYSVOL` .

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

`SYSVOL` folder: creates a shared directory on an NTFS volume that all authenticated domains users can access with reading permissions

### Lightweight Directory Access Protocol (LDAP)

A lightweight protocol for managing directory services that is used in Microsoft AD.

### (Legacy) NTLM Authentication

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
