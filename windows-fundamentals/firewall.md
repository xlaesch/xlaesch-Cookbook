A firewall allows or blocks incoming and outgoing network packets on a host. Rules define the conditions that determine whether packets can pass through the firewall.

# Firewall Console

Open **Windows + R**, then enter `wf.msc`.

# List Rules

```powershell
# list all firewall rules
netsh advfirewall firewall show rule name=all
```

# Inspect a Rule

```powershell
# display information about a specific firewall rule
netsh advfirewall firewall show rule name="<rule_name>"
```

# Delete a Rule

```powershell
# delete a specific firewall rule
netsh advfirewall firewall delete rule name="<rule_name>"
```
