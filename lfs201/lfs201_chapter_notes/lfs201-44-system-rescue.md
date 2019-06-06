Chapter 44: System Rescue
-------------------------

[44.3: Learning Objectives](#443-learning-objectives)  
[44.4: Rescue Media and Troubleshooting](#444-rescue-media-and-troubleshooting)  
[44.5: Common Utilities on Rescue/Recovery Media](#445-common-utilities-on-rescuerecovery-media)  
[44.6: Using Rescue/Recovery Media](#446-using-rescuerecovery-media)  
[44.7: Emergency Boot Media](#447-emergency-boot-media)  
[44.8: Using Rescue Media](#448-using-rescue-media)  
[44.9: Rescue USB Key](#449-rescue-usb-key)  
[44.10: Emergency Mode](#4410-emergency-mode)  
[44.11: Single User Mode](#4411-single-user-mode)  
[Lab 44.1: Preparing to Use Rescue/Recover Media](#lab-441-preparing-to-use-rescuerecover-media)  
[Lab 44.2: Recovering from a Corrupted GRUB Configuration](#lab-442-recovering-from-a-corrupted-grub-configuration)  
[Lab 44.3: Recovering from Password Failure](#lab-443-recovering-from-password-failure)  
[Lab 44.4: Recovering from Partition Table Corruption](#lab-444-recovering-from-partition-table-corruption)  
[Lab 44.5: Recovering Using the Install Image](#lab-445-recovering-using-the-install-image) 
[Paths and Commands](#paths-and-commands)  
  
### 44.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain what forms system rescue media come in and how they are made available or can be prepared.
* Know how to enter **emergency mode** and what can be done there.
* Know how to enter **single user mode**, what can be done there, and how it differs from emergency mode.
  
### 44.4: Rescue Media and Troubleshooting
----
* when booting from external media, there will be an option like **Rescue Installed System**
* the rescue image generally contains a limited but powerful set of utilities useful for fixing problems on a system:
    * disk Maintenance utilities
    * networking utilities
    * miscellaneous utilities
    * logging files
  
### 44.5: Common Utilities on Rescue/Recovery Media
----
* useful programs include:
    * disk utilities for creating partitions, managing RAID devices, managing logical volume, and creating filesystems, such as **fdisk**, **mdadm**, **pvcreate**, **vgcreate**, **lvcreate**, **mkfs**, and many others
    * networking utilities for network debugging and network connectivity, such as **ifconfig**, **route**, **traceroute**, **mtr**, **host**, **ftp**, **scp**, **ssh**
    * numerous other commands are also available, such as **bash**, **chroot**, **ps**, **kill**, **vi**, **dd**, **tar**, **cpio**, **gzip**, **rpm**, **mkdir**, **ls**, **cp**, **mv**, and **rm** to name a few
  
### 44.6: Using Rescue/Recovery Media
----
* the rescue image will ask whether or not to mount your filesystems (if it can)
    * if so, they are usually mounted at `/mnt/sysimage`
    * `$ sudo chroot /mnt/sysimage` change into the `/mnt/sysimage` environment
    * you may install software packages from inside the **chroot**-ed environment
    * `$ sudo rpm -ivh --force --root=/mnt/sysimage /mnt/source/Packages/vsftpd-2*.rpm` install packages outside a **chroot**-ed environmnet on an **rpm**-based system by using the **--root** option
* for a network-based rescue, you may be asked to mount `/mnt/source`
  
### 44.7: Emergency Boot Media
----
* most Linux distributions permit the install media (CD, DVD, USB) and/or Live media to serve a double purpose as a rescue disk
* Live media (in any format) provide a complete and bootable operating system which runs in memory, rather than loading from disk
    * useful for experiencing and evaluating an operating system and/or Linux distribution without actually installing it, or making any changes to the existing operating system on the computer
    * Live removable media are unique in that they can run on a computer lacking secondary storage, such as a hard disk drive, or with a corrupted hard disk drive or file system, allowing users to rescue data
  
### 44.8: Using Rescue Media
----
* the rescue/recovery mode can be accessed from an option on the boot menu when the system starts from the removable media. In many cases, you may have to type **rescue** on a line like: **boot: Linux rescue**
* expect some questions, many distribution-specific
  
### 44.9: Rescue USB Key
----
* many distributions provide a **boot.iso** image file for download (the name may differ)
* `$ dd if=boot.iso of=/dev/sdX` use **dd** to place boot image on a USB key drive assuming your system recognizes the removable drive as `/dev/sdX`
* assuming your system has the capability of booting from USB media and the BIOS is configured for it, you can then boot from this USB drive
    * the install tree will not be present on the USB drive; therefore, this method requires a network-based install tree if one is needed
* helpful utilities such as **livecd-tools** and **liveusb-creator** allow specification of either a local drive or the internet as the location for obtaining an install image, and then do all the hard work of constructing a bootable image and burning it on the removable drive
    * this is extremely convenient and works for virtually all Linux distributions
  
### 44.10: Emergency Mode
----
* in **emergency mode** you are booted into the most minimal environment possible
    * the root filesystem is mounted read-only
    * no **init** scripts are run
    * almost nothing is set up
* the main advantage of emergency mode over single-user modeis that if **init** is corrupted or not working, you can still mount filesystems to recover data that might be lost during a re-installation
* to enter emergency mode:
    * select an entry from the GRUB boot menu
    * then hit **e** for edit
    * then add the word **emergency** to the kernel command line before telling the system to boot
    * you will be asked for the root password before getting a shell prompt
* you may also enter emergency mode when a boot fails for a variety of reasons, including a corrupted filesystem
  
### 44.11: Single User Mode
----
* if your system boots, but does not allow you to log in when it has completed booting, try **single user mode**
* in single user mode:
    * **init** is started
    * services are not started
    * network is not activated
    * all possible filesystems are mounted
    * root access is granted without a password
    * Aa system maintenance command line shell is launched
* in this mode, your system boots to **runlevel** 1
* because single user mode automatically tries to mount your filesystem, you cannot use it when your root filesystem cannot be mounted successfully, or if the **init** configuration is corrupted
* to boot into single user mode, you use the same method as described for emergency mode with one exception, replace the keyword **emergency** with the keyword **single**
  
### Lab 44.1: Preparing to Use Rescue/Recover Media
----
"Do not do the following exercises unless you are sure you can boot your system off rescue/recovery media!"
  
### Lab 44.2: Recovering from a Corrupted GRUB Configuration
----

### Lab 44.3: Recovering from Password Failure
----

### Lab 44.4: Recovering from Partition Table Corruption
----

### Lab 44.5: Recovering Using the Install Image
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
rescue, system | `/mnt/sysimage` | location of mounted filesystems during system rescue | LFS201 44.6
rescue, system | `/mnt/source` | mount location for a network-based rescue | LFS201 44.6
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
rescue, system | `$ sudo chroot /mnt/sysimage` | change into the `/mnt/sysimage` environment | LFS201 44.6
rescue, system | `$ sudo rpm -ivh --force --root=/mnt/sysimage /mnt/source/Packages/vsftpd-2*.rpm` | install packages outside a **chroot**-ed environmnet on an **rpm**-based system by using the **--root** option | LFS201 44.6
rescue, system | `$ dd if=boot.iso of=/dev/sdX` | use **dd** to place boot image on a USB key drive | LFS201 44.9
rescue, system | **livecd-tools** | suite of tools for creating and manipulating live media | LFS201 44.9
rescue, system | **liveusb-creator** | creates live CD or DVD images (ISO images) from kickstart files | LFS201 44.9
  