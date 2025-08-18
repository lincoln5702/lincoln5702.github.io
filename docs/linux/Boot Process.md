# Linux Boot Process

To truly understand the Linux boot process, we first need to be familiar with the hardware components that make up a PC. Let’s start by exploring the structure of a hard disk drive (HDD).

#### Hard Disk Drive
In the past, the terms *disk* and *drive* referred to separate components. The *disk* is where data is stored, while the *drive* is the mechanism that moves the read/write head to the correct sector.

![](attachments/linux_one.png)

The arm of the drive contains the read/write head, which writes data in blocks onto specific sectors. The circular components are called *platters*, which spin at high speeds. Each platter has its own read/write arm. To better understand how data is stored, we need to examine *tracks* and *sectors*.

The concentric rings on a platter are called *tracks*. Each track is divided into smaller units known as *sectors*, where the actual data resides. Data is stored in blocks of 512 bytes. On MBR-based systems, the first sector of track 0 stores the **Master Boot Record (MBR)**. Newer systems use the more advanced **GUID Partition Table (GPT)**. In this section, we’ll focus on MBR to explain the boot process, and later discuss GPT.

![](attachments/Linux_two.png)

### Reading and Writing Information to the Disk
Writing data to a disk is a time-consuming process because the disk arm must physically move to the appropriate track and sector. There are three important time factors involved:

- **Seek time** – the time it takes for the disk head to reach the correct track.  
- **Transfer time** – the time it takes to transfer a block of data.  
- **Rotational latency** – the delay caused by waiting for the platter to rotate to the correct position.  

### How Linux Handles the Disk
Linux categorizes devices into two main types: **character devices** and **block devices**.

- *Character devices* handle data one character at a time (e.g., mouse, keyboard, terminal).  
- *Block devices* store and transfer data in blocks (e.g., hard disk drives).  

All device files are stored in the `/dev` directory. Each disk partition is also represented as a separate file under `/dev`. Every device requires a **device driver**, which communicates with the Linux kernel through system calls such as `open()`, `close()`, `read()`, `write()`, `ioctl()`, and `mmap()`. Each device is uniquely identified by **major** and **minor** numbers, which the kernel uses to locate the correct driver.

### Disk Partitioning
A disk partition is a logical division of a hard disk where filesystems, swap areas, and data regions reside. MBR and GPT define how many partitions can be created and how they are structured.

### File System
A **file system** is an organized collection of files and directories. Examples include Microsoft’s FAT and NTFS, and Linux journaling file systems such as Btrfs and XFS.

![](attachments/Linux_three.png)

A disk is divided into multiple partitions, and each partition can hold its own filesystem. A filesystem typically consists of:

- **Boot block** – contains boot code (present on all partitions, even if they aren’t bootable).  
- **Superblock** – stores metadata, including the size of the inode table, logical blocks, and data blocks.  
- **Inode table** – a critical structure that maintains metadata about files and directories. For example, when you double-click a directory in a GUI, the system uses the inode entry to check permissions and locate files.  
- **Data blocks** – where the actual data is stored.  

### Putting It All Together: The Boot Process
The Linux boot process begins the moment you press the power button:

1. **BIOS/UEFI Initialization** – The BIOS (stored in ROM) runs the **Power-On Self Test (POST)**, checking hardware and I/O connections.  
2. **Loading the MBR** – BIOS loads the MBR from the disk. The MBR contains:  
   - *Disk signature* – identifies the bootable disk (if multiple disks exist).  
   - *Partition table* – describes how partitions are laid out.  
   - *Master boot code* – responsible for loading the bootloader into memory.  
3. **Bootloader (GRUB2)** – The master boot code loads **GRUB2**, the bootloader, into memory.  
4. **Kernel Loading** – GRUB2 loads the compressed Linux kernel image (`vmlinuz`) from the `/boot` directory into RAM.  
5. **System Initialization (systemd)** – Once the kernel is running, it starts **systemd** (process ID 1), the parent of all other processes. Systemd initializes the system by setting the hostname, starting services, configuring the network, and more.  
   - Older systems used **SysVinit**, but modern Linux distributions rely on **systemd**.  
   - Previous systems used *runlevels* (e.g., single-user, multi-user, graphical). Today, systemd replaces them with **targets**:  

   - Runlevel 0 → `poweroff.target`  
   - Runlevel 3 → `multi-user.target`  
   - Runlevel 5 → `graphical.target`  

6. **Login Screen** – After initialization, the system presents the login screen, ready for user access.  

![](attachments/linux_four.png)
