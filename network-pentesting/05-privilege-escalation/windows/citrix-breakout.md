---
tags: [privesc, windows, citrix]
aliases: [Citrix Breakout]
---
Virtualization platform that offers remote access solutions. Desktop restrictions are put in place to secure the organizations but a threat actor can still "breakout". The typical flow for these actions
- Gain access to a Dialog Box
- Exploit the dialog box to gain command execution.
- Escalate privileges.

# Bypassing Path Restrictions

Group policy may be implemented to restrict users from browsing directories in the C:\ drive using File Explorer. We can use the Windows dialog box as a means to bypass the restrictions. Once the dialog box is obtained we must find a folder path containing native executables that offer interactive access (cmd.exe).

A lot of Citrix applicatiosn are equipped with functionalities that enable them to interact with files on the OS. We can use MS Paint to open the dialog box. With MS Paint

Enter the UNC path `\\127.0.0.1\c$\users\pmorgan` with file-type set to All Files. We gain access to the desired directory.

# Accessing SMB Shares

With restrictions set, File Explorer does not allow direct access to SMB shares  on the attacker machine, or the Ubuntu server hosting the Citrix environment. We can again circumvent this with a UNC path.

```shell
# serve an SMB server
smbserver.py -smb2support share $(pwd)
```

Access the UNC path as `\\10.13.38.95\share`. Usually direct file copying is not viable, but, we can right-click on the executable and subsequently launch them. We will usually serve some sort of binary that opens the cmd.exe

```c
#include <stdlib.h>
int main() {
  system("C:\\Windows\\System32\\cmd.exe");
}
```

# Alternate Explorer

If the File Explorer is blocked we can use the Q-Dir or Explorer++ as a workaround. 

# Alternate Regitry Editors

If the default Registry Editor is blocked by group policy, alternative editors can be employed. Simpleregedit, Uberregedit, and SmallRegistryEditor are examples of such tools.

# Modifying existing shortcut file

We can access folder paths by modifying the existing Windows shortcuts and setting a desired executable's path.

1. Right-click the desired shortuct.
2. Select Properties.
3. Modify path to the intended folder.
4. Execute the shortcut.

# Script Execution

When script extensions such as .bat, .vbs, or .ps are configured to automatically execute their interpreters. We can thus serve an interactive console to bypass restrictions. 

1. Create new text file.
2. Open in notepad
3. Add commands.

## Related

- [[other|Other Privesc]]
- [[user-interaction]]
- [[windows-old]]
- [[groups|Windows Groups]]

