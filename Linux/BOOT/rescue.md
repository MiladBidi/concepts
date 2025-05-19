CVE: Common Vulnerabilities and Exposures \
Rescue Mode \
```bash
/etc/default/grub
```

To find the root partition, the kernel needs a set of modules (drivers) located in the initramfs file (a compressed file that is loaded at boot time).\
These modules tell the kernel how to communicate with the hardware (such as disks or a RAID controller).\

The kernel alone is incomplete: \
The Linux kernel is a large program, but many of its features (such as support for specific hardware, filesystems, or protocols) are stored in modules (separate files). \
At boot time, the kernel needs to load these modules in order to be able to recognize disks, the root filesystem, or even the network.
