---
icon: linux
---

# Linux

## Enumeration

### Commands on local machine

| Command    | Information/Options                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hostname` | target system role                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `uname -a` | kernel used by system                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `ps`       | <p></p><ul><li><code>-A</code> all running process</li><li><code>axjf</code> view process tree</li><li><code>aux</code> show processes for all users and display user that launched process and that are not attached to a terminal</li></ul>                                                                                                                                                                                                                                                           |
| `env`      | show environment variables                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `sudo -l`  | all commands user can run using sudo                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `ls -la`   | show hidden files                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `id`       | user privilege level                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `history`  | show earlier commands                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `ifconfig` | information about the network interfaces of system                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `netstat`  | <p></p><ul><li><code>-a</code> all listening ports and established connection</li><li><code>-at</code> or <code>-au</code> list TCP or UDP protocols</li><li><code>-l</code> list ports in listening mode</li><li><code>-s</code> list network usage stats by protocol</li><li><code>-tp</code> list connections with the service name and PID info</li><li><code>-i</code> interface statistics</li><li><code>-n</code> do not resolve names</li></ul><ul><li><code>-o</code> display timers</li></ul> |
| `find`     | search target system                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

### Useful File Locations

<table data-header-hidden><thead><tr><th width="374">Location</th><th>Description</th></tr></thead><tbody><tr><td><code>/etc/issue</code></td><td>contains a message or system identification to be printed before the login prompt.</td></tr><tr><td><code>/etc/profile</code></td><td>controls system-wide default variables, such as Export variables, File creation mask (umask), Terminal types, Mail messages to indicate when new mail has arrived</td></tr><tr><td><code>/proc/version</code></td><td>specifies the version of the Linux kernel</td></tr><tr><td><code>etc/passwd</code></td><td>has all registered users that have access to a system</td></tr><tr><td><code>/etc/shadow</code></td><td>contains information about the system's users' passwords</td></tr><tr><td><code>/root/.bash_history</code></td><td>contains the history commands for <code>root</code> user</td></tr><tr><td><code>/var/log/dmessage</code></td><td>contains global system messages, including the messages that are logged during system startup</td></tr><tr><td><code>/var/mail/root</code></td><td>all emails for <code>root</code> user</td></tr><tr><td><code>/root/.ssh/id_rsa</code></td><td>Private SSH keys for a root or any known valid user on the server</td></tr><tr><td><code>/var/log/apache2/access.log</code></td><td>the accessed requests for <code>Apache</code> web server</td></tr><tr><td><code>C:\\boot.ini</code></td><td>contains the boot options for computers with BIOS firmware</td></tr></tbody></table>

### Automated Tooling

* [**LinPeas**](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
* [**LinEnum**](https://github.com/rebootuser/LinEnum)&#x20;
* [**LES (Linux Exploit Suggester)**](https://github.com/mzet-/linux-exploit-suggester)
* [**Linux Smart Enumeration**](https://github.com/diego-treitos/linux-smart-enumeration)&#x20;
* [**Linux Priv Checker**](https://github.com/linted/linuxprivchecker)

## Kernel Exploits

The kernel on linux manages communication between components such as memory and applications. Doing so requires privileges so exploiting the kernel can lead to root privileges. Typical methodology is:

1. Identify kernel version
2. Search and find exploit code
3. Run Exploit

{% hint style="info" %}
Failed kernel exploits lead to a system crash.
{% endhint %}

## Sudo

Sudo commands allow user to run commands with root privileges. Administrators might give regular users some flexibility to run certain commands with root privileges.&#x20;

`sudo -l` to view these commands. [GTFOBins ](https://gtfobins.github.io)is a useful resource to view how to exploit which commands are available.

If `env_keep` setting appears in the sudoers configuration, we can use the `LD_PRELOAD` option to load shared malicious libraries before others when a program runs. e.g. `sudo LD_PRELOAD=/path/to/shell.so /usr/bin/nmap`

## SUID/SGID

Set-User Identification (SUID) and Set-Group Identification (SGID) allows you to run executables or commands with the permissions of the file's owner, not the user who executes it. Once again, [GTFOBins ](https://gtfobins.github.io/)can show you which binaries can be exploitable.

`find / -type f -perm -04000 -ls 2>/dev/null`  to view which files allow you to do so.

## Capabilities

A Linux feature that helps manage privileges at a more granular level, allowing certain users to use certain tools but not others (far more specific than sudo).

`getcap -r / 2>/dev/null` to view these privileges.

## Cron Jobs

A Linux feature that runs scripts and binaries at specific times, by default they run with the privilege of their owners not the current users.&#x20;

`/etc/crontab` is where the jobs are located.

## PATH

PATH in linux is an environment variable that tells the OS where to search for executables for any command not built into the shell.&#x20;

{% hint style="info" %}
The goal is to get an administrator to run a payload as root instead of the intended command.
{% endhint %}

Certain conditions need to be met to exploit PATH:

* What folders are under `$PATH`
* Does the user have write privileges to any of these folders
  * `find / -writable 2>/dev/null` to find writable folders
* Can the user modify `$PATH`&#x20;
  * `export PATH=/tmp:$PATH` add `/tmp` to the beginning of PATH telling it to look their first&#x20;

## NFS:

When a user from a client machine accesses a shared NFS directory, their user ID is maintained. If `no_root_squash` is disabled, and they log on to the NFS directory as root, it will "squashed" and the server will treat them as a low-privilege user. If `no_root_squash` is enabled the client can create files on the server that are owned by the server's root user, including programs with the SUID bit set.

1. `/etc/exports` is where the configuration is kept
2. `showmount -e <IP>` to enumerate mountable shares
3. Mount `no_root_squash` shares, create a directory and then `mount -o rw IP:/PATH NEW_DIRECTORY`&#x20;
4. Compile a executable and set the SUID bit.
