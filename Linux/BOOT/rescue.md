CVE: Common Vulnerabilities and Exposures \
Rescue Mode \
```bash
/etc/default/grub
```

To find the root partition, the kernel needs a set of modules (drivers) located in the initramfs file (a compressed file that is loaded at boot time).\
These modules tell the kernel how to communicate with the hardware (such as disks or a RAID controller).
