LF101 Introduction to Linux Notes
=====

Chapter 3: Linux Basics and System Startup
-----

## Notes 3

### 3.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
* Identify Linux filesystems.
* Identify the differences between partitions and filesystems.
* Describe the boot process.
* Install Linux on a computer.

### 3.1: The Boot Process
the Linux **boot process** is the procedure for initializing the system; everything that happens from power on until the UI is fully operational
1. **BIOS** (**Basis Input/Output System**)
  * initializes hardware, including screen and keyboard, and tests main memory (**POST** or **POwer On Self Test**)
  * BIOS software stored on ROM chip on motherboard
  * after this, boot process controlled by OS
2. **MBR** (**Master Boot Record**)
  * after **POST** control passes from **BIOS** to the boot loader
  * boot loader usually stored on hard disk in the boot sector (for traditional **BIOS/MBR** systems; first sector of the hard disk, known as **MBR**; MBR is 512 bytes) or **EFI** partition (for more recent **(Unified) Extensible Firmware Interface** or **EFI/UEFI** systems)
  * up to this stage, machine does not access any mass storage media, so information on date, time, and most important peripherals are loaded from the **CMOS values**
  * Linux boot loaders:
  	* **GRUB** (**GR**and **U**nified **B**oot loader)
  	* **ISOLINUX** (for booting from removable media)
  	* **DAS U-Boot** (for booting on embedded devices/appliances)
  * boot loader responsible for loading the kernel image and the intial RAM disk or filesystem into memory
3. Boot Loader in Action
  * First stage:
    * MBR systems:
      * boot loader examines partition table and finds a bootable partition
      * then searches for second stage boot loader like **GRUB** and loads it into **RAM**
    * EFI/UEFI method:
    	* UEFI firmware reads its **Boot Manager** data to determine with UEFI application to launch and from where (from which disk and partition the EFI partition can be found)
    	* then launches the UEFI application like **GRUB**
    	* more complicated but more versatile than the older MBR methods
  * Second stage:
    * second stage boot loader resides under `/boot`
    * a splash screen is displayed, which allows us to choose which OS to boot
    * after selection, boot loader loads the kernel of the selected OS into RAM and passes control to it
  * kernel's first job is usually to uncompress itself
  * then kernel will check and analyze the system harware and initialize any hardware device drivers built into the kernel
4. Initial RAM Disk
  * the **initramfs** filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem
  * provides device drivers for mass storage controllers with a facility called **udev** (for **U**ser **Device**), which figures out devices present, **drivers** they need, and loading them
  * root filesystem found, checked for errors, and mounted
  * the **mount** program instructs OS that FS is ready for use and associates it with the **mount point**, a particular point in the overall heirarchy of the FS
  ? What is an example of a mount point?
  * if success, **initramfs** is cleared from RAM and the **init** program on the root FS (`/sbin/init`) is executed
  * **init** handles mounting and pivoting to final real root FS; needed special hardware drivers must be in **initramfs** image
5. Text-Mode Login
  * text-mode login prompts, which enable you to type your username and password, are started by **init** (will not see these with a GUI at first)
  * most distros start six text terminals and one graphics terminal starting with **F1** or **F2**
  	* access using **ALT** plus a **function** key
  	* within a graphical environment, switch to text console with **CTRL-ALT +** the appropriate function key (**F7** or **F1** lead to the GUI)
  ? What is a 'terminal' in this context?
  * **bash** (the **GNU Bourne Again Shell**) is the usual, default command shell

### 3.2: Kernel, init, and Services

#### The Linux Kernel
* the boot loader loads the kernel and a RAM-based file system (**initramfs**) into memory
* the kernel initializes and configures the computer's memory and all attached hardware (processors, I/O subsystems, storage devices, etc.) in addition to some user space applications

#### /sbin/init and Services
* the kernel runs the `/sbin/init` program, which becomes the origin of all other processors except those started byt he kernel itself
* **init** starts other processes to get the system running, keeps it running, and shuts it down cleanly
* old way of doing it: **System V UNIX** with **runlevels**

#### Startup Alternatives: Upstart and systemd
* problem: **SysVinit** viewed things as **serial** and did not take advantage of parallel processing offered by multiple processors or cores; shutdown and reboot treated as rare events; startup scripts difficult to maintain
* alternatives:
  * **Upstart**
    * developed by **Ubuntu** and included in 2006
    * adopted in **Fedora 9** in 2008 and **RHEL 6**
  * **systemd**
    * adopted by **Fedora** in 2011
    * adopted by **RHEL 7** and **SUSE**
    * replaced **Upstart** in **Ubuntu 16.04**
    * migration to **systemd** controversial, but almost universal adoption has made learning how to work on Linux systems simpler

#### systemd Features
* boot with **systemd** is faster than with init methods
* complicated startup shell scripts replaced with simpler configuration files
* **systemctl** is a **systemd** command; what it does (**fooservice** like **nfsd**):
  * starting, stopping, restarting a service: `$ sudo systemctl start/stop/restart fooservice`
  * enabling or disabling a system service from starting up at system boot: `$ sudo systemctl enable/disable fooservice`

### 3.3: Linux Filesystem Basics

#### Linux Filesystems
* filesystems store and organize arbitrary collections of data in a human-usable form
* types of filesystems supported by Linux:
  * conventional disk filesystems: **ext2, ext3, ext4, XFS, Btrfs, JFS, NTFS,** etc.
  * flash storage filesystems: **ubifs, JFFS2, YAFFS,** etc.
  * database filesystems
  * special purpose filesystems: **procfs, sysfs, tmpfs, debugfs,** etc.

#### Partitions and Filesystems
* a **partition** is a logical part of the disk
* a **filesystem** is a method of storing and finding files on a hard disk (usually in a partition)
* analogy: filesystem like a family tree showing descendants and relationships, while partitions like different families, each with their own tree

Table 3.3 Comparison between filesystems in Windows and Linux
feature                        | Windows     | Linux
-------                        | -------     | -----
Partition                      | Disk1       | /dev/sda1
Filesystem type                | NTFS/VFAT   | EXT3/EXT4/XFS/BTRFS...
Mounting parameters            | DriveLetter | MountPoint
Base folder where OS is stored | C:\         | /

#### The Filesystem Hierarchy Standard
* important files stored according to a standard layout called the **Filesystem Hierarchy Standard (FHS)**, maintained by The Linux Foundation
* Linux doesn't have drive letters like Windows
  * multiple drives or partitions are mounted as directories within the FS, like `/run/media/yourusername/disklabel`

#### More About the Filesystem Hierarchy Standard
* filesystem names are case-sensitive
* most distros distinguish between core utilities needed for system operation and other programs (those in `/usr`, which has a similar structure to `/`)
* viewing FSH in GUI: click 'Home' -> 'Computer' -> 'etc' -> 'avahi' and CTRL-L to see the path (all major distros use the same File Manager program, so this is basically the same across distros)

### 3.4: Linux Distribution Installation

#### Choosing a Linux Distribution / Questions to Ask When Choosing
*  What is the main function of the system (server or desktop)?
*  What types of packages are important to the organization? For example, web server, word processing, etc.
*  How much hard disk space is available? For example, when installing Linux on an embedded device, there will be space limitations.
*  How often are packages updated?
*  How long is the support cycle for each release? For example, LTS releases have long term support.
*  Do you need kernel customization from the vendor?
*  What hardware are you running the Linux distribution on? For example, X86, ARM, PPC, etc.
*  Do you need long-term stability or short-term experimental software?

#### Linux Installation: Planning
* partition design can be modified later, but easier to get it right the first time
* most installers provide reasonable default filesystem layouts

#### More About Planning in Linux Installation
* most installers provide options for adding categories of software
* examples of what is often included:
  * common applications (such as the Firefox web browser and LibreOffice office suite)
  * developer tools (like the vi and emacs text editors)
  * popular services, (such as the Apache web server tools or MySQL database)
  * a desktop environment
* all installers secure the system as part of the installation (like setting a password for **root**; Ubuntu starts with an initial user but no root access at first)

#### Linux Installation: Install Source
* can be installed from removable media or a small image which then downloads rest over the network
* config files can specify installation options
  * Fedora-based systems: **Kickstart** file
  * SUSE-based systems: **AutoYAST** profile
  * Debian-based systems: **preseed file**

#### Linux Installation: The Process
Nothing to see here

#### Linux Installation: The Warning
* Don't install on a currently-used hard disk without knowing what you're doing
* alternate options:
  * re-partitioning your hard disk to free up enough room to permit dual boot
  * using a host machine hypervisor program (such as VMWare's products or Oracle Virtual Box) to install a client Linux Virtual Machine
  * booting off of and using a Live CD or USB stick and not writing to the hard disk at all

### 3.5: Summary
* The boot process has multiple steps, starting with **BIOS**, which triggers the **boot loader** to start up the Linux kernel. From there, the **initramfs** filesystem is invoked, which triggers the **init** program to complete the startup process.

Chapter 4: Graphical Interface
-----
### 4.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
  * Manage graphical interface sessions.
  * Perform basic operations using the graphical interface.
  * Change the graphical desktop to suit your needs.

### 4.1: Graphical Desktop

#### Introduction
* **CLI**: Command Line Interface
* **GUI**: Graphical User Interface

#### X Window System
* loaded as final step in boot process
* **display manager** service keeps track of provided displays, loads the **X server**, handles graphical logins, starts desktop environment
* **X server** provides graphical services to application, sometimes called **X clients**
* old, so might be replaced by **Wayland**, already adopted in **Fedora 25**
* three components of the desktop environment:
  * session manager: starts and maintains the components of the graphical session
  * window manager: controls placement and movement of windows, window title-bars, and controls
  * utilities

#### GUI Startup
* **X** display manager:
  * starts at the end of the boot process
  * responsible for starting graphics system
  * logging in the user
  * starting the user's desktop environment
* default display manager for **GNOME** is called **gdm** (**lightdm** used on Ubuntu, **kdm** associated with KDE)

#### GNOME Desktop Environment
* GNOME bundled as default for RHEL, Fedora, CentOS, SUSE Linux Enterprise, and Debian
* KDE also a common desktop environment, often used in SUSE and openSUSE
* other alternatives: **Unity** (from Ubuntu, based on GNOME), **XFCE**, and **LXDE**

#### Graphical Desktop Background
#### Customizing the Desktop Background
#### Changing the Desktop Background in CentOS
#### Changing the Desktop Background in openSUSE
#### Changing the Desktop Background in Ubuntu
#### Try-It-Yourself: Changing the Desktop Background
#### gnome-tweak-tool
* exposes many more setting options than the default settings utility
* can also be used to add third-party extenstions
* use **Alt-F2** to enter a command

#### Changing the Theme
* can do this via settings or gnome-tweak-tool
* this link doesn't seem to work anymore: http://art.gnome.org/themes/

### Lab 1: Customizing the Desktop

### 4.2: Session Management
### 4.3: Basic Operations
### 4.4: Summary

Chapter 5: System Configuration from the Graphical Interface
-----
### 5.0: Introduction/ Learning Objectives
### 5.1: System, Display, Date and Time Settings
### 5.2: Network Manager
### 5.3: Installing and Updating Software
### 5.4: Summary

Chapter 6: Common Applications
-----
### 6.0: Introduction/ Learning Objectives
### 6.1: Internet Applications
### 6.2: Productivity and Development Applications
### 6.3: Multimedia Applications
### 6.4: Graphics Editors and Utilities
### 6.5: Summary

Chapter 7: Command Line Operations
-----
### 7.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
  * Use the command line to perform operations in Linux.
  * Search for files.
  * Create and manage files.
  * Install and update software.

### 7.1: Command Line Mode Options

#### Introduction to Command Line
"Graphical user interfaces make easy tasks easier, while command line interfaces make difficult tasks possible."

#### Some Basic Utilities
* **cat**
* **head**
* **tail**
* **man**

#### The Command Line
Most input lines entered at shell prompt have three basic elements:
* command
* options
* arguments (what the command operates on)

#### sudo
* **sudo** allows a user to run programs with privileges of another user, generally root (superuser)
steps for setting up and running sudo if not already set up and enabled:
1. use **su** and then enter password; prompt changes from `$` to `#`
2. create a config file, typically created in `/etc/sudoers.d`; file created like this: `# echo "student ALL=(ALL) ALL" > /etc/sudoers.d/student`  
I already have the file:
`[root@centos sudoers.d]# cat student` 
> student ALL=(ALL) NOPASSWD: ALL
3. change permissions: `# chmod 440 /etc/sudoers.d/student`
* it is possible (though very insecure) to configure sudo to not require a password or change the time window in which the password does not have to be repeated with every sudo command

#### Using a Text Terminal on the Graphical Desktop
**gnome-terminal** is the default **terminal emulator** on **GNOME** desktop environments; other terminal programs include:
* **xterm**
* **rxvt**
* **konsole**
* **terminator**

#### Launching Terminal Windows
in **CentOS 7**:
* click **Applications->Utilities->Terminal**
* right-click on desktop background and select **Open Terminal**
* hit **Alt-F2** and type **gnome-terminal** or **xterm**

#### Switching between the GUI and the Command Line
* during installation, one can often choose between desktop (with **X**) or server (usually without **X**)
* removing **X** from a production server can help maintain a lean system that can be easier to support and more secure

#### Virtual Terminals
**Virtual Terminals (VT)** are **console** sessions that use the entire display and keyboard outside of graphical environment; only one visible at a time even if others active; not the same as command line terminal on desktop  
One VT reserved for graphical environment (Ubuntu: VT 7; CentOS/RHEL and openSUSE: VT 1), six for text logins
To switch between the VTs:
* **CTRL-ALT-corresponding function key** for the VT if running **X**
* **ALT-corresponding function key** if not running **X**

#### Turning Off the Graphical Desktop
on newer **systemd**-based distros, the display manager is run as a service; it can be stopped with the **systemctl** utility or **telinit** on most distros:
* `$ sudo systemctl stop gdm`  (or `$ sudo telinit 3`)
* `$ sudo systemctl start gdm`  (or `$ sudo telinit 5`)
  (use **lightdm** instead of **gdm** on Ubuntu)

### Lab 7.1 Killing the Graphical User Interface
1. kill the current graphical desktop
I used `$ sudo systemctl stop gdm`
2. restart the GUI from the console
I used `$ sudo telinit 5`; GUI reinstated in VT 1; got message that last login was on tty2, so I guess that refers to the terminal

### 7.2: Basic Operations
### 7.3: Working with Files
### 7.4: Searching for Files
### 7.5: Installing Software
### 7.6: Summary




Linux commands
=====
`$ gnome-tweak-tool`
`$ cat [FILE]`
`$ head [FILE]`
`$ tail [FILE]`
`$ man _name_`
**pipe** |
`[student@centos ~]$ su
Password: 
[root@centos student]#`
`$ sudo systemctl stop gdm`  (or `$ sudo telinit 3`)
`$ sudo systemctl start gdm`  (or `$ sudo telinit 5`)
(use **lightdm** instead of **gdm** on Ubuntu)
