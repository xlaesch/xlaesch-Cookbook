---
tags: [privesc, windows]
aliases: [Virtual Machines]
---
# Virtual Machine Disks

Virtual Machine disk images can be helpful troves of information. The common file types are `.vhd`, `.vhdx`, and `.vmdk` files.  We need to mount them either on our local Linux or Windows attack boxes.

```shell
# linux mount vmdk
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk

# mount vhd/vhdx
guestmount --add WEBSRV10.vhdx  --ro /mnt/vhdx/ -m /dev/sda1
```

On Windows we can choose `Mount` from right-click menu or use the Disk management utility to mount a `.vhd` or `.vhdx` file. 

For .vmdk files, we can right-click and choose map virtual disk from the menu. We can also use VMWare Workstation to map the disk onto our system.

## Related

- [[citrix-breakout]]
- [[linux-old]]
- [[windows-old]]
- [[groups|Windows Groups]]

