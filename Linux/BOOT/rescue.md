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

### The Linux Boot Process
#### Power-On: 
* A firmware (such as BIOS or UEFI) is executed.
* This firmware initializes the hardware and looks for a bootloader.

#### Bootloader Execution: 
* The bootloader (such as GRUB) is loaded from disk.
* This bootloader finds the `kernel` and `initramfs` and loads them into RAM.

#### Kernel Execution: 
* The kernel is executed from RAM, initializes the hardware
* opens the initramfs as a temporary filesystem in RAM.

#### Running initramfs: 
* The kernel runs an initial script (usually /init) from the initramfs that loads:
* the necessary modules, finds and mounts the root disk, and hands control to the main operating system.

#### Booting the operating system: 
* After mounting the root filesystem, the kernel runs the systemd  which starts services
