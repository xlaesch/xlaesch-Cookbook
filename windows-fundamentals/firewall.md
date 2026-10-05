4Tool that allows blocking or allowing incoming network packets to the host machine and outgoing networks packets from the host machine. 

A firewall rule is a set of conditions used to determine whether a network packet is allowed to pass through the firewall.

To access the GUI "Windows + R" then `wf.msc`

```
netsh advfirewall firewall show rule name=all
```

List all firewall rules.

```
netsh advfirewall firewall show rule name=”TCP Port 4444 Block”
```

Displaying the information of the firewall rule.

```
netsh advfirewall firewall delete rule name="TCP Port 4444 Block"
```

Deleting a firewall rule.

