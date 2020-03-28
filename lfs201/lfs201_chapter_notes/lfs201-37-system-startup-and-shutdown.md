Chapter 37: System Startup and Shutdown
---------------------------------------

[37.1: System Startup and Shutdown](#371-system-startup-and-shutdown)  
[37.2: Chapter 37 Introduction](#372-chapter-37-introduction)  
[37.3: Learning Objectives](#373-learning-objectives)  
[37.4: Boot Sequence](#374-boot-sequence)  
[37.5: BIOS](#375-bios)  
[37.6: Boot Loaders](#376-boot-loaders)  
[37.7: Configuration Files in /etc](#377-configuration-files-in-etc)  
[37.8: /etc/sysconfig](#378-etcsysconfig)  
[37.9: /etc/default](#379-etcdefault)  
[37.10: Shutting Down and Rebooting](#3710-shutting-down-and-rebooting)  
[37.11: Knowledge Check 37.1](#3711-knowledge-check-371)  
[Lab 37.1: Shutdown vs. Halt vs. Reboot](#lab-371-shutdown-vs-halt-vs-reboot)  
[Paths and Commands](#paths-and-commands)  
  
### 37.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the boot process.
* Identify several types of boot loaders.
* Describe what the BIOS does.
* Identify the relevant configuration files.
* Describe how the system shuts down and reboots.
  
### 37.4: Boot Sequence
----
* basic steps in the boot sequence are:  
    1. the BIOS/UEFI locates and executes the boot program, or boot loader
    2. the boot loader loads the kernel
    3. the kernel starts the **init** process (`pid=1`)
    4. **init** manages system initialization, using systemd or the older Upstart and SysVinit startup scripts
* when power is applied to the computer, the computer can only perform the operations the **BIOS** (**B**asic **I**nput **O**utput **S**ystem) orders it to do
* first, the BIOS runs the **POST** (**P**ower **O**n **S**elf **T**est), which checks the memory and hardware and then searches a specific location or device for a boot program
    * typically, the boot program is found in the device's **MBR** (**M**aster **B**oot **R**ecord)
    * control of the computer is then transferred to this boot program (usually **GRUB**)
* the boot program then loads the kernel into the memory and executes it
    * on x86 platforms (and many others), the kernel first has to decompress itself in place
    * it then performs hardware checks, gains access to important peripheral hardware, and eventually runs the **init** process
    * this first process continues the system startup, managing either systemd or Upstart, or running appropriate init scripts if SysVinit is being used
* newer computers are moving to **UEFI**, a replacement for BIOS, which performs many of the same functions
  
### 37.5: BIOS
----
* on the x86 architecture, the BIOS contains all the code required to gain initial access to the keyboard, display screen, disk drives, serial communications, and a number of miscellaneous functions
* once the full system is running, most of these devices will have enhanced capabilities when complete and specialized device drivers can be loaded and take over
* the BIOS is typically placed in a ROM chip that comes with the computer (it is often called a ROM BIOS)
    * this ensures that the BIOS will always be available and will not be damaged by disk failures
    * this also makes it possible for a computer to boot itself
* during the boot process, the BIOS loads the boot loader from the MBR
  
### 37.6: Boot Loaders
----
* different boot loaders used in Linux:
    * **GRUB**
    * **efibootmgr**
    * **LILO**
    * **Das U-Boot**
* most use **GRUB** (**GR**and **U**nified **B**oot **L**oader)
* **GRUB**:
    * can boot multiple operating systems
    * has both a graphical and a text-based interface allowing ease of use over a serial cable
    * has a powerful command line interface for interactive configuration
    * has network-based diskless booting
* **efibootmgr** is not actually a boot loader, but is a boot manager, used in conjunction with GRUB on multi-boot EFI systems
* the Linux Loader (**LILO**) is old and obsolete
* **Das U-Boot** is the most popular loader for embedded Linux systems; there are some other boot loaders, including **bareboot**
  
### 37.7: Configuration Files in /etc
----
* generally, system-wide configuration files are placed in `/etc` and its subdirectories, while user-specific ones are often placed in their individual home directories
    * however, default configuration information might be stored in `/usr/lib/systemd`, but can be overridden by files in `/etc/systemd`
    * it's distribution-specific for historical reasons
    * Red Hat-derived systems make extensive use of `/etc/sysconfig`
    * Debian-based systems have used `/etc/default`
    * RHEL 7 and SUSE use both
  
### 37.8: /etc/sysconfig
----
* `/etc/sysconfig` files here are used when starting, stopping, configuring or querying system services on RHEL 7
* `/etc/sysconfig/selinux` reads and sets the **selinux** configuration at system startup on RHEL 7
  
### 37.9: /etc/default
----
* `/etc/default` used by Ubuntu in a similar way as `/etc/sysconfig` on RHEL 7
    * the files provide extra options when starting as service
    * they typically contain code to set environment variables
    * RHEL 7 also has this directory as its use is becoming more common
* `/etc/default/useradd` sets the defaults that are used when new user accounts are being created
  
### 37.10: Shutting Down and Rebooting
----
* **shutdown** is used to bring the system down in a secure fashion, notifying all users that the system is going down and then stopping it in a graceful and non-destructive way
* after it is shut down, the system is either halted or rebooted
* examples:
    * `$ shutdown [OPTIONS...] [TIME] [WALL...]` generic syntax
    * `$ sudo shutdown -h +1 "Power Failure imminent"`
    * `$ sudo shutdown -h now` **-h** equivalent to **--poweroff**
    * `$ sudo shutdown -r now` reboots the machine
    * `$ sudo shutdown now` power off the system (but not Ubuntu, which goes to single user mode instead)
* legacy commands: **reboot**, **halt**, **poweroff**
  
### 37.11: Knowledge Check 37.1
----
Organize the basic steps of the boot sequence in the correct order.  
1. The boot loader is executed.
2. The Linux kernel and the **initrd** or **initramfs** image are loaded into memory and the kernel executes.
3. The **init** process starts.
4. Additional kernel modules (including device drivers) are loaded, and system services are started.
  
### Lab 37.1: Shutdown vs. Halt Vs. Reboot
----
1. Reboot the system using shutdown.
    `$ sudo shutdown -r now`
2. Power off the system using shutdown.
    `$ sudo shutdown -h now`
3. Power the system back up.

### Paths and Commands
----
  
#### Paths  
  
Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
startup/shutdown | `/usr/lib/systemd` | contains default configuration information | LFS201 37.7
startup/shutdown | `/etc/systemd` | contains default configuration information | LFS201 37.7
startup/shutdown | `/etc/sysconfig` | files used when starting, stopping, configuring or querying system services on RHEL 7 | LFS201 37.8
startup/shutdown | `/etc/sysconfig/selinux` | reads and sets the **selinux** configuration at system startup on RHEL 7 | LFS201 37.8
startup/shutdown | `/etc/default` | used by Ubuntu in a similar way as `/etc/sysconfig` on RHEL 7 | LFS201 37.9
startup/shutdown | `/etc/default/useradd` | sets the defaults that are used when new user accounts are being created | LFS201 37.9
  
#### Commands  
  
Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
startup/shutdown | `$ shutdown [OPTIONS...] [TIME] [WALL...]` | generic syntax | LFS201 37.10
startup/shutdown | `$ sudo shutdown -h +1 "Power Failure imminent"` | sends message to logged-in users(?) | LFS201 37.10
startup/shutdown | `$ sudo shutdown -h now` | **-h** equivalent to **--poweroff** | LFS201 37.10
startup/shutdown | `$ sudo shutdown -r now` | reboots the machine | LFS201 37.10
startup/shutdown | `$ sudo shutdown now` | power off the system (but not Ubuntu, which goes to single user mode instead) | LFS201 37.10
startup/shutdown | `$ reboot` | legacy command | LFS201 37.10
startup/shutdown | `$ halt` | legacy command | LFS201 37.10
startup/shutdown | `$ poweroff` | legacy command | LFS201 37.10
  