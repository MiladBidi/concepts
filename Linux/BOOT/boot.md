1. Power On

2. BIOS
* POST - Power On Self Test
* Detect peripherals: hard drive, RAM ...
* Find the boot device
* Load the bootloader from the boot sector

3. BL
* Display boot menu
* Load kernel into memory
* Provide information needed by kernel (initrd/initramfs)
* Transfer control to kernel

4. Kernel
* Loads initial hardware drivers.
* Prepares virtual file system (VFS).
* Mounts the initramfs
* Checks and mounts the root filesystem (e.g. / on ext4 or xfs)
* Runs the first userspace process: systemd
