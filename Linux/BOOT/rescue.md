CVE: Common Vulnerabilities and Exposures \
### Rescue Mode 

1. Download iso
```bash
SystemRescue Main Page:
https://www.system-rescue.org/Download/

download link:
https://sourceforge.net/projects/systemrescuecd/files/sysresccd-x86/12.00/systemrescue-12.00-amd64.iso/download
```

2. Mount this ISO as a CD/DVD Drive into VM
* in vmware workstation: go to settings of VM > CD/DVD > Browse ISO File

3. Turn off and Power on VM > Press ESC > Enter to BIOS Menu > Select Boot From CD/DVD
4. you must be here

![image](https://github.com/user-attachments/assets/63ccfd52-7fa5-401d-8380-15e888899cf8)

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
