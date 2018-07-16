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

### Lab 4.1: Customizing the Desktop

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

#### Logging In and Out
* **text terminal** will prompt for username and password
* **Secure Shell (SSH)** mentioned

#### Rebooting and Shutting Down
* **shutdown** is the preferred method; **init** then controls shutdown or reboot
* **halt** and **poweroff** commands issue a `shutdown -h` to halt the system, while **reboot** issues `shutdown -r`; both require superuser (root) access, as in:
  * `$ sudo shutdown -h 10:00 "Shutting down for scheduled maintenance."`

#### Locating Applications
* most executables should live in `/bin`, `/usr/bin`, `/sbin`, `/usr/sbin`, or `/opt`
* use **which** to find programs
  * example: `$ which python`
  > /usr/bin/python
* use **whereis** to search a broader range of directories; includes **source** and **man** files packaged with the program
  * example: `$ whereis python`
  > python: /usr/bin/python2.7 /usr/bin/python /usr/lib/python2.7 /usr/lib64/python2.7 /etc/python /usr/include/python2.7 /usr/share/man/man1/python.1.gz

#### Accessing Directories
* `$ echo $HOME` to see path of default directory
* `$ pwd` to see present working directory
* `$ cd ~` or `$ cd` to change to home directory
* `$ cd ..` to change to parent directory
* `$ cd -` to change to previous directory

#### Understanding Absolute and Relative Paths
* an **absolute pathname** always begins with `/`
* a **relative pathname** never starts with `/`
* `////usr//bin` is seen as `/usr/bin` by the system
* `.` == present directory
* `..` == parent directory
* `~` == home directory

#### Exploring the FileSystem
* `$ cd /` change to root directory
* `$ ls` list contents of present working directory
* `$ ls -a` list all files including **hidden** files (those whose name starts with .)
* `$ tree` display a tree view of filesystem
* `$ tree -d` show only directories and not file names

#### Hard File Links
* `$ ln file1 file2` creates a **hard link**
  * file1 is the original file
  * file2 is created
* `$ ls -li file1 file2` indicates that the **inode** number, a unique quantity for each file object, is the same for both, so really it's **one** file
  * `-i` flag for showing inode
  * the 2 in the results indicates there are two names associated with the file
* deleting one of the files, the **inode object** and other file name will remain
* when editing, **vi** and **gedit** will retain the link, but other editors might break it, resulting in creation of two objects

#### Soft (Symbolic) Links
* `$ ln -s file1 file3`
  * file3 has a different **inode** number
* symbolic links take no extra space on filesystem
* can point to objects on different filesystems or partitions; if source is not available or does not exist, link is called a **dangling** link

#### Navigating the Directory History
* `$ cd -` will go to last directory visited
* `$ pushd .` pushes starting directory to a list
* `$ popd .` move to directories on list in reverse order (most recent first)
* `$ dirs` to see the list

### Lab 7.2: Locating Applications
Find out the location of the ip network utility.  
My solution:
* `$ which ip`
> /usr/sbin/ip
* `$ whereis ip`
> ip: /usr/sbin/ip /usr/share/man/man7/ip.7.gz /usr/share/man/man8/ip.8.gz

### 7.3 Working with Files

#### Viewing Files

**Command** | **Usage**
----------- | ---------
cat         | used for viewing files that aren't long
tac         | used to look at a file backwards, starting with last line
less        | used to view larger files; pauses at each screen full of text; use / to search forward and ? to search backward; **more** is an older program that is similar
tail        | used to print last 10 lines of file by default; use -n 15 or -15 to look at last 15 lines, for instance
head        | opposite of tail

#### touch and mkdir
* **touch** is often used to set or update the access, change, and modify times of files; it resets a file's time stamp to match the current time
  * also used to create an empty file: `$ touch <filename>`
  * can set timestamp of file: `$ touch -t 06302000 <filename>` # June 30, 20:00
* **mkdir** is used to create directories
* **rmdir** is used to remove empty directories, use `$ rm -rf` for directories with data

#### Removing a File

**Command** | **Usage**
----------- | ---------
mv          | rename a file
rm          | remove a file
rm -f       | forcefully remove a file
rm -i       | interactively remove a file (good practice when using patterns)

#### Renaming or Removing a Directory

**Command** | **Usage**
----------- | ---------
mv          | rename a directory
rmdir       | remove an empty directory
rm -rf      | forcefully remove a directory recursively

#### Modifying the Command Line Prompt
* the **PS1** variable is the character string that is displayed as the prompt on the command line
* example: `student@quad32 $` could be set with `\u@\h \$`
`$ echo $PS1`
> \$
`$ PS1="\u@\h \$ "`
`coop@quad64 $ echo $PS1`
> \u@\h \$
`coop@quad64 $` 

### Lab 7.3: Creating, Moving, and Removing Files
*  Create an empty file named exercise.txt and move this file to the /tmp directory, using a relative pathname from your home directory. Then, delete this file using an absolute pathname.
* My solution:
  * `$ touch exercise.txt`
  * `$ mv exercise.txt ../../../../tmp/`
  * `$ rm /tmp/exercise.txt`


### 7.4: Searching for Files

#### Standard File Streams
Three standard **file streams** (or **descriptors**) are always open for use:  

**Name**        | **Symbolic Name** | **Value** | **Example**
--------        | ----------------- | --------- | -----------
standard input  | stdin             | 0         | keyboard
standard output | stdout            | 1         | terminal
standard error  | stderr            | 2         | log file

* **stdin** is your keyboard or supplied from output of a previous commmand via a **pipe**
* **stdout** is printed on the terminal or redirected into a file
* **stderr** is usually redirected to an error logging file
* Other open files will start at file descriptor 3 and increase from there

#### I/O Redirection
* assume we have a program called **do_something** that reads from **stdin** and writes to **stdout** and **stderr**
  * change its input source:
`$ do_something < input-file`
  * send its output to a file:
`$ do_something > output-file`
  * send its error messages to a file using the file descriptor:
`$ do_something 2> error-file`
  * put error messages to the same place as file descriptor 1:
`$ do_something > all-output-file 2>&1`
  * the previous in **bash**:
`$ do_something >& all-output-file`

#### Pipes
* a **pipeline**:
`$ command1 | command2 | command3`
  * _command2 and command3 do not have to wait for previous commands to complete_
  * this allows short and simple programs to be combined to produce complex results

#### locate
* **locate** searches a database constructed by **updatedb** (usually run once per day)
* example: `$ locate zip | grep bin`

#### Wildcards and Matching File Names

**Wildcard** | **Result**
------------ | ----------
?            | matches any single character
\*           | matches any string of characters
[set]        | matches any character in the set
[!set]       | matches any character not in the set

This is just regex  

#### Using find
* without args, `$ find` lists all files in current directory and all of its subdirectories7.4 - find
* common options:
  * `-name` for file name
  * `-iname` ignore case of file name
  * `-type` (`d` for directory, `l` for link, `f` for file)
* example use:
  * `$ find /usr -name gcc`
  * `$ find /usr -type d -name gcc`
  * `$ find /usr -type f -name gcc`

#### Using Advanced find Options
* `-exec` to run commands on matched files
* example: find and remove all files ending with .swp:
  * `$ find -name "*.swp" -exec rm {} ';'`7.4 - a
  * {} place holder for file names resulting from search
  * must end with ';' or '\;'
* `-ok` acts like exec but prompts for permission

#### Finding Files Based on Time and Size
* time, such as `$ find / -ctime 3`
  * `-ctime` for inode metadata (e.g., file ownership, permissions, etc.) last changed
  * `-atime` for last accessed or read
  * `-mtime` for last modified or written
  * the number **n** is for days, **+n** for larger than n, **-n** for less than n
  * for minutes, use `-cmin`, `-amin`, `-mmin`
* size, such as `$ find / -size 0`
  * default number is in 512-byte blocks
  * **c** for bytes, **k** for kilobytes, **M** for megabytes, **G** for gigabytes, etc.
  * can also use exact numbers as with time, **n**, **+n**, or **-n**

#### Try-It-Yourself: Using find
Tasks to be performed:
* earch for a file with name gcc in the /usr folder using find command.
* earch for a directory with filename gcc, in the /usr folder using find command.
* earch for files in the current directory which were modified today.
* Search for files with size 0 bytes.

### Lab 7.4: Finding Directories and Creating Symbolic Links
Find the init.d directory, starting from /, and then create a symbolic link from within your home directory to this directory.  
(Note that this SysVinit directory is no longer used much in systemd-based systems, but is kept for backwards compatibility reasons.)  
My solution:  
`$ find / -name init.d -type d 2>&1 | grep -v 'Permission denied'` (I could also use **sudo** to not see the Permission denied errors)  
`$ cd ~`  
`$ ln -s /etc/rc.d/init.d .`  

### 7.5: Installing Software
See LFS201 Chapters 5-7 for more details  

#### Package Managers: Two Levels
Both package management systems provide two tool levels:  
* a low-level tool (such as dpkg or rpm) takes care of the details of unpacking individual packages, running scripts, and getting the software installed correctly
* a high-level tool (such as apt-get, yum, or zypper) works with groups of packages, downloads packages from the vendor, and figures out dependencies.  

### Lab 7.5: Installing and Removing Software Packages
Using the upper level package management system appropriate for your Linux distribution, do the following:
* Install the dump package on your system.
* Remove the dump package from your system.
NOTE: If dump is already installed (you will be told so when you try to install), then do things in opposite order, i.e., remove and then install.
* My solution:
* `sudo yum install dump`
* `sudo yum remove dump`

### 7.6: Summary
Key concepts covered:
* Virtual terminals (VT) in Linux are consoles, or command line terminals that use the connected monitor and keyboard.
* Different Linux distributions start and stop the graphical desktop in different ways.
* A terminal emulator program on the graphical desktop works by emulating a terminal within a window on the desktop.
* The Linux system allows you to either log in via text terminal or remotely via the console.
* When typing your password, nothing is printed to the terminal, not even a generic symbol to indicate that you typed.
* The preferred method to shut down or reboot the system is to use the **shutdown** command.
* There are two types of **pathnames**: absolute and relative.
  * An absolute pathname begins with the root directory and follows the tree, branch by branch, until it reaches the desired directory or file.
  * A relative pathname starts from the present working directory.
  * Using **hard** and **soft** (**symbolic**) links is extremely useful in Linux.
* **cd** remembers where you were last, and lets you get back there with `cd -`.
* **locate** performs a database search to find all file names that match a given pattern.
* **find** locates files recursively from a given directory or set of directories.
* **find** is able to run commands on the files that it lists, when used with the `-exec` option.
* **touch** is used to set the access, change, and edit times of files, as well as to create empty files.
* The **Advanced Packaging Tool** (**apt**) package management system is used to manage installed software on Debian-based systems.
* You can use the **Yellowdog Updater Modified** (**yum**) open source command-line package-management utility for **RPM**-compatible Linux operating systems.
* The **zypper** package management system is based on RPM and used for openSUSE.

Chapter 8: Finding Linux Documentation
-----
### 8.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
* Use different sources of documentation.
* Use the **man pages**.
* Access the GNU **info** system.
* Use the **help** command and `--help` option.
* Use other documentation sources.

### 8.1: Documentation Sources

#### Introduction to Linux Documenation Sources
Important Linux documentation sources include:
* The **man pages** (short for manual pages)
* GNU **Info**
* The **help** command and `--help` option
* Other Documentation Sources, e.g. https://www.gentoo.org/doc/en/ or https://help.ubuntu.com/community/CommunityHelpWiki

### 8.2: The man pages

#### The man pages
Some Linux distributions require every installed program to have a corresponding **man** page, which explains the depth of coverage.  
**man** pages are often converted to:
* web pages (See http://man7.org/linux/man-pages/)
* published books
* graphical help
* other formats

#### man
* with no options, you see only the dedicated page about the topic
* with `-f`, view all pages containing a string in their name
* with `-k`, view all pages that discuss a specfied subject (even if subject not in name)
* `$ man -f` same as typing **whatis**
* `$ man -k` same as typing **apropos**

#### Manual Chapters
* **man pages** are divided into nine numbered chapters
* `$ man 3 printf` # shows chapter 3 of the printf man page
* `$ man -a printf` # shows all manual pages with the name printf
> --Man-- next: printf(1p) [ view (return) | skip (Ctrl-D) | quit (Ctrl-C) ]  
> --Man-- next: printf(3) [ view (return) | skip (Ctrl-D) | quit (Ctrl-C) ]  
> --Man-- next: printf(3p) [ view (return) | skip (Ctrl-D) | quit (Ctrl-C) ]  

### Lab 8.2 (Lab 1): Working with man
1. Finding man pages
From the command line, bring up the man page for man itself. Scroll down to the EXAMPLES section.
  * My solution:
  * `$ man man`
2. Finding man Pages by Topic
What man pages are available that document file compression?
  * My solution:
  * `$ man -k compression`
> Compress::Raw::Bzip2 (3pm) - Low-Level Interface to bzip2 compression library  
> Compress::Raw::Zlib (3pm) - Low-Level Interface to zlib compression library  
> Compress::Zlib (3pm) - Interface to zlib compression library  
> SSL_COMP_add_compression_method (3ssl) - handle SSL/TLS integrated compression methods  
> SSL_COMP_free_compression_methods (3ssl) - handle SSL/TLS integrated compression methods  
>  zlib (3)             - compression/decompression library  

3. man Pages by Section
From the command line, bring up the man page for the printf library function. In which manual page section are library functions found?
  * My solution:
  * `$ man printf` # Section 1 - this is the command line utility
  * Correct solution:
  * `$ man 3 printf` # Section 3

Here are the sections from the man man page:
1. Executable programs or shell commands
2. System calls (functions provided by the kernel)
3. Library calls (functions within program libraries)
4. Special files (usually found in /dev)
5. File formats and conventions eg /etc/passwd
6. Games
7. Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
8. System administration commands (usually only for root)
9. Kernel routines [Non standard]

### 8.3: GNU Info

#### GNU Info System
* alternative to **man**
* topics connected by links
* accessible via command line, graphical help utility, printed, or viewed online

#### Command Line Info Browser
* typing info with no arguments in a terminal window displays an index of available topics
* you can view help for a particular topic by typing `info <topic name>`

#### info Page Structure
* topic which you view in the info page is called a **node**
* nodes might contain menus and linked subtopics, or **items**, are like hyperlinks and identified by an asterisk (**\***) at the beginning of the item name
* named items outside a menu are identified with double-colons(**::**) at the end of the item name
* items can refer to other nodes within the file or to other files
Helpful keystrokes:
Key   | Function
----- | --------
n     | go to next node
p     | go to previous node
u     | move one node up in the index
q     | quit
h     | help
Enter | select a menu item

### Lab 8.3 (Lab 2): Working with info
From the command line, bring up the info page for cpio. Bring up the tutorial.
* My solution:
* `$ info cpio`, then select "Tutorial" from the menu

### 8.4: The --help Option and Help Command

#### Introduction to the --help Option
* short description accessed with the **--help** or **-h** flags

#### The help Command
* type `$ help` to see a synopsis of built-in commands
* type `$ help <command>`
* for these built-in commands, help performs the same basic function as the **-h** and **--help** arguments perform for stand-alone programs

### Lab 8.4 (Lab 3): Working with Command Line help
List the available options for the mkdir command, in more than one way.  
* My solution:
* `$ help mkdir`
> no help topics match 'mkdir'
* `$ man -k mkdir`
> gvfs-mkdir (1)       - Create directories
> mkdir (1)            - make directories
> mkdir (1p)           - make directories
> mkdir (2)            - create a directory
> mkdir (3p)           - make a directory
> mkdirat (2)          - create a directory relative to a directory file descriptor
* `$ man -f mkdir`
> mkdir (1)            - make directories
> mkdir (1p)           - make directories
> mkdir (3p)           - make a directory
> mkdir (2)            - create a directory
* `$ man mkdir`
* `$ info mkdir`
* Another answer:
* `$ mkdir --help`

### 8.5: Other Documentation Sources

#### Other Documentation Sources
* desktop help system
  * GNOME: within graphical terminal, type `$ gnome-help` or `$ yelp`
  * KDE: `$ khelpcenter`
* package documentation
  * found in `/usr/share/doc`
* online resources
  * LinuxCommand.org: http://linuxcommand.org/tlcl.php
  * Ubuntu: https://help.ubuntu.com/
  * CentOS: https://www.centos.org/docs/ points user to https://access.redhat.com/documentation/en-us/
  * OpenSUSE: http://en.opensuse.org/Portal:Documentation
  * GENTOO: http://www.gentoo.org/doc/en

### Lab 8.5 (Lab 4): Working with Graphical Help Systems
Find the graphical help system on your desktop, and try to locate within it the **man** pages for **printf**. This may be difficult, so do not waste too much time before looking at the suggestions below. 
* My solution:
  * I couldn't find any man pages in the gnome help
  * I used the tip in the lab and ran `$ yelp man:printf`
  * in location bar, hit **CTRL-l** and then type **man:printf**, and it works fine (bug?)
  * do the same for info pages as well

### 8.6: Summary
Key concepts covered:
* The main sources of Linux documentation are the **man pages**, **GNU Info**, the **help** options and command, and a rich variety of online documentation sources. 
* The **man** utility searches, formats, and displays **man pages**.
* The **man pages** provide in-depth documentation about programs and other topics about the system, including configuration files, system calls, library routines, and the kernel.
* The **GNU Info System** was created by the **GNU** project as its standard documentation. It is robust and is accessible via command line, web, and graphical tools using info.
* Short descriptions for commands are usually displayed with the `-h` or `--help` argument.
* You can type **help** at the command line to display a synopsis of built-in commands.
* There are many other help resources both on your system and on the Internet.

Chapter 9: Processes
-----
### 9.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
* Describe what a process is and distinguish between types of processes.
* Enumerate process attributes.
* Manage processes using **ps** and **top**.
* Understand the use of load averages and other process metrics.
* Manipulate processes by putting them in **background** and restoring them to **foreground**.
* Use **at**, **cron**, and **sleep** to schedule processes in the future or pause them.

### 9.1: Introduction to Processes and Process Attributes

#### What Is a Process?
* **process**: an instance of one or more related **tasks** (**threads**) executing on the computer
* not the same as  **program** or a **command**; a single program can start several processes simultaneously
* some are related to others, some are independent
* the OS, especially the kernel, allocate resources to processes

#### Process Types
* a terminal window is a process; allows users to run programs
* programs can also be run in the **background**, **detached** from the shell

Process Type          | Description | Example
------------          | ----------- | -------
Interactive Processes | Need to be started by a user, either at a command line or through a graphical interface such as an icon or a menu selection. | **bash, firefox, top**
Batch Processes       | Automatic processes which are scheduled from and then disconnected from the terminal. These tasks are queued and work on a **FIFO** (First In, First Out) basis. | **updatedb**
Daemons               | Server processes that run continuously. Many are launched during system startup and then wait for a user or system request indicating that their service is required. | **httpd, xinetd, sshd**
Threads               | Lightweight processes. These are **tasks** that run under the umbrella of a main process, sharing memory and other resources, but are scheduled and run by the system on an individual basis. An individual thread can end without terminating the whole process and a process can create new threads at any time. Many non-trivial programs are multi-threaded. | **firefox, gnome-terminal-server**
Kernel Threads        | Kernel tasks that users neither start nor terminate and have little control over. These may perform actions like moving a thread from one CPU to another, or making sure input/output operations to disk are completed. | **kthreadd, migration, ksoftirqd**

#### Process Scheduling and States
* the **scheduler** (a kernel function) moves processes on and off the CPU
* processes are in a **running** state when (1) executing instructions on CPU or (2) waiting for a **time slice**
* running processes are in a **run queue**
* processes are in a **sleep** state when they are waiting for something to happen (typically I/O to become available (9.2)) before they can resume
* sleeping processes are in a **wait** queue
* **zombie** state when child process completes but parent hasn't asked about it's state; shows in system's list of processes

#### Process and Thread IDs
* the **process ID** (**PID**) is used to track process state, CPU usage, memory use, where resources are located in memory, and other characteristics
* PIDs are assigned in ascending order; PID 1 is the **init** process

ID Type                  | Description
-------                  | -----------
Process ID (PID)         | Unique Process ID number
Parent Process ID (PPID) | Process (Parent) that started this process. If the parent dies, the PPID will refer to an adoptive parent; on recent kernels, this is **kthreadd** which has PPID=2.
Thread ID (TID)          | Thread ID number. This is the same as the PID for single-threaded processes. For a multi-threaded process, each thread shares the same PID, but has a unique TID.

#### Terminating a Process
`$ kill -SIGKILL <pid>`  
`$ kill -9 <pid>`  

#### User and Group IDs

User IDs                                                     | User Group IDs
--------                                                     | --------------
Real User ID (**RUID**): identifies user who started process | Real Group ID (**RGID**): identifies group that started process
Effective User ID (**EUID): determines access rights of the user | Effective Group ID (**EGID**): determines access rights of the group

#### More about Priorities
* the **priority** of a process determines how much CPU time it gets
* priority can be changed via the **nice value**, or **niceness** for the process
* one can also assign **real-time priority** to time-sensitive tasks; this is just a very high priority
* **hard real time** is conceptually different; involves making sure a job is complete within a well-defined time window

### 9.2: Process Metrics and Process Control

#### Load Averages
* **load average** is the average of the **load number**; takes into account processes that are:
  * actively running on CPU
  * runnable but waiting for a CPU to become available
  * sleeping
* ? what's the load number?
* unlike UNIX-like operating systems, Linux includes sleeping processes; it only includes **uninterruptible** sleepers, those which cannot be awakened easily
* load average can be viewed by running **w**, **top**, or **uptime**


#### Interpreting Load Averages
The load average is displayed using three different sets of numbers.  
Assuming our system is a single-CPU system, the three load average numbers are interpreted as follows:  
* 0.45: For the last minute the system has been 45% utilized on average.
* 0.17: For the last 5 minutes utilization has been 17%.
* 0.12: For the last 15 minutes utilization has been 12%.
  
If we saw a value of 1.00 in the second position, that would imply that the single-CPU system was 100% utilized, on average, over the past 5 minutes; this is good if we want to fully use a system. A value over 1.00 for a single-CPU system implies that the system was over-utilized: there were more processes needing CPU than CPU was available.  
  
If we had more than one CPU, say a quad-CPU system, we would divide the load average numbers by the number of CPUs. In this case, for example, seeing a 1 minute load average of 4.00 implies that the system as a whole was 100% (4.00/4) utilized during the last minute.  
  
Short-term increases are usually not a problem. A high peak you see is likely a burst of activity, not a new level. For example, at start up, many processes start and then activity settles down. If a high peak is seen in the 5 and 15 minute load averages, it may be cause for concern.  

#### Background and Foreground Processes
Linux supports **background** and **foreground** job processing. (A job in this context is just a command launched from a terminal window.) **Foreground** jobs run directly from the shell, and when one foreground job is running, other jobs need to wait for shell access (at least in that terminal window if using the GUI) until it is completed. This is fine when jobs complete quickly. But this can have an adverse effect if the current job is going to take a long time (even several hours) to complete.  
  
In such cases, you can run the job in the **background** and free the shell for other tasks. The background job will be executed at lower priority, which, in turn, will allow smooth execution of the interactive tasks, and you can type other commands in the terminal window while the background job is running. By default, all jobs are executed in the foreground. You can put a job in the background by suffixing & to the command, for example: `$ updatedb &`  
  
You can either use **CTRL-Z** to suspend a foreground job or **CTRL-C** to terminate a foreground job and can always use the **bg** and **fg** commands to run a process in the background and foreground, respectively.  

#### Managing Jobs
The **jobs** utility displays all jobs running in background. The display shows the job ID, state, and command name, as shown here.

`jobs -l` provides the same information as `jobs`, including the PID of the background jobs.

The background jobs are connected to the terminal window, so, if you log off, the jobs utility will not show the ones started from that window.

### Lab 9.2a (Lab 1): Getting Uptime and Load Averages
1. Ascertain how long your system has been up.  
* My solution:
* `$ uptime`  
>  14:28:34 up 1 day,  1:19,  2 users,  load average: 0.91, 0.82, 0.45  
2. Display its load averages.  
* My solution:
* `$ 2`  
>  14:28:45 up 1 day,  1:19,  2 users,  load average: 0.92, 0.83, 0.45  
> USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT  
> student  :0       :0               Sat16   ?xdm?  12:39   0.78s /usr/libexec/gnome-session-binary --session gnome-classic  
> student  pts/0    :0               Sat21    5.00s  0.13s  0.00s w  
* Another option:
* `$ top | head`  
> top - 14:30:10 up 1 day,  1:21,  2 users,  load average: 1.26, 0.96, 0.53  
> Tasks: 201 total,   1 running, 200 sleeping,   0 stopped,   0 zombie  
> %Cpu(s): 15.6 us, 14.1 sy,  0.0 ni, 70.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st  
> KiB Mem :  3863556 total,   807604 free,  1040748 used,  2015204 buff/cache  
> KiB Swap:   975868 total,   974580 free,     1288 used.  2454772 avail Mem   
>   
>    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND  
>    746 polkitd   20   0  649988  18112   5420 S  31.2  0.5   5:30.02 polkitd  
>    782 root      20   0  396324   4096   3184 S  12.5  0.1   1:02.49 accounts-daemon  
>    785 dbus      20   0   61892   4076   1856 S   6.2  0.1   1:19.45 dbus-daemon  

### Lab 9.2b (Lab 2): Background and Foreground Jobs
We are going to launch a graphical program from a terminal window, so that one can no longer type in the window. **gedit** is an easy choice, but you can substitute any other program that does this.  
  
The Solution file contains a step-by-step procedure for putting jobs in background, bringing them back to foreground, etc. Please repeat the steps, substituting the program you are using if it is not **gedit**.  
  
* My solution:  
> 629  gedit file1  
> 631  jobs -l  
> 632  bg  
> 633  jobs -l  
> 634  fg  
> 635  jobs -l  
> 640  kill -9 72049  
  
### 9.3: Listing Processes: ps and top
  
#### The ps Commmand (System V Style)
**ps** provides information about currently running processes keyed by **PID**. If you want a repetitive update of this status, you can use **top** or other commonly installed variants, such as **htop** or **atop**, from the command line, or invoke your distribution's graphical system monitor application.  
  
**ps** has many options for specifying exactly which tasks to examine, what information to display about them, and precisely what output format should be used.  
  
Without options, **ps** will display all processes running under the current shell. You can use the `-u` option to display information of processes for a specified username. The command `ps -ef` displays all the processes in the system in full detail. The command `ps -eLf` goes one step further and displays one line of information for every **thread** (remember, a process can contain multiple threads).  
  
#### The ps Command (BSD Style)
**ps** has another style of option specification, which stems from the **BSD** variety of UNIX, where options are specified without preceding dashes. For example, the command `ps aux` displays all processes of all users. The command `ps axo` allows you to specify which attributes you want to view.  
  
The screenshot shows a sample output of **ps** with the `aux` and `axo` qualifiers.  
  
#### The Process Tree
**pstree** displays the processes running on the system in the form of a **tree diagram** showing the relationship between a process and its parent process and any other processes that it created. Repeated entries of a process are not displayed, and threads are displayed in curly braces.

#### top
* **top** gives constant real-time updates (every two seconds by default)
* First Line of the top Output:
  * uptime
  * how many users are logged on
  * load average
* Second Line of the top Output:
  * total number of processes
  * the number of running, sleeping, stopped, and zombie processes
* Third Line of the top Output:
  * how the CPU time is being divided between the users (**us**) and the kernel (**sy**) by displaying the percentage of CPU time used for each
  * percentage of user jobs running at a lower priority (niceness - **ni**)
  * idle mode (**id**) should be low if the load average is high, and vice versa
  * percentage of jobs waiting (**wa**) for I/O is listed
  * interrupts include the percentage of hardware (**hi**) vs. software interrupts (**si**)
  * steal time (**st**) is generally used with virtual machines, which has some of its idle CPU time taken for other uses

#### Fourth and Fifth Lines of the top Output


#### Process List of the top Output


#### Interactive Keys with top


### 9.4: Starting Processes in the Future


### 9.5: Summary


Chapter 10: File Operations
-----
### 10.0: Introduction/ Learning Objectives


Chapter 11: Text Editors
-----
### 11.0: Introduction/ Learning Objectives


Chapter 12: User Environment
-----
### 12.0: Introduction/ Learning Objectives


Chapter 13: Manipulating Text
-----
### 13.0: Introduction/ Learning Objectives


Chapter 14: Network Operations
-----
### 14.0: Introduction/ Learning Objectives


Chapter 15: Bash Shell Scripting I
-----
### 15.0: Introduction/ Learning Objectives


Chapter 16: Bash Shell Scripting II
-----
### 16.0: Introduction/ Learning Objectives


Chapter 17: Printing
-----
### 17.0: Introduction/ Learning Objectives


Chapter 18: Local Security Principles
-----
### 18.0: Introduction/ Learning Objectives


Final Exam
-----
### 

Linux filesystem and paths
=====

Linux paths
-----
`////usr//bin` (7.2)  
`.` (7.2)  
`..` (7.2)  
`~` (7.2)  


Linux commands
-----
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
`$ sudo shutdown -h 10:00 "Shutting down for scheduled maintenance."` (7.2)  
`$ halt` (7.2)  
`$ poweroff` (7.2)  
`$ shutdown -r` (7.2)  
`$ cd /` (7.2)  
`$ ls` (7.2)  
`$ ls -a` (7.2)  
`$ tree` (7.2)  
`$ tree -d` (7.2)  
`$ ln file1 file2` (7.2)  
`$ ls -li file1 file2` (7.2)  
`$ ln -s file1 file3` (7.2)  
`$ cd -` (7.2)  
`$ pushd .` (7.2)  
`$ popd .` (7.2)  
`$ dirs` (7.2)  
`$ cat` (7.3)  
`$ tac` (7.3)  
`$ less` (7.3)  
`$ tail` (7.3)  
`$ tail -n 15` (7.3)  
`$ tail -15` (7.3)  
`$ head` (7.3)  
`$ touch <filename>` (7.3)  
`$ touch -t 06302000 <filename>` (7.3)  
`$ mkdir <dirname>` (7.3)  
`$ rmdir <dirname>` (7.3)  
`$ rm -rf` (7.3)  
`$ mv <original> <new>` (7.3)  
`$ rm` (7.3)  
`$ rm -f` (7.3)  
`$ rm -i` (7.3)  
`$ PS1="\u@\h \$ "` (7.3)  
`$ do_something < input-file` (7.4)  
`$ do_something > output-file` (7.4)  
`$ do_something 2> error-file` (7.4)  
`$ do_something > all-output-file 2>&1` (7.4)  
`$ do_something >& all-output-file` (7.4)  
`$ locate zip | grep bin` (7.4)  
`$ cp <original_file> <new_file>` (7.4)  
`$ find` with `-name`, `-iname`, `-type` (`d` for directory, `l` for link, `f` for file), `-exec` (7.4)  
`$ find /usr -name gcc` (7.4)  
`$ find /usr -type d -name gcc` (7.4)  
`$ find /usr -type f -name gcc` (7.4)  
`$ find -name "*.swp" -exec rm {} ';'` or `-ok` instead (7.4)  
`$ find / -ctime 3` or `-atime`, `-mtime` with **n**, **+n**, or **-n**; `-cmin`, `-amin`, `-mmin` (7.4)  
`$ find / -size 0` with **c**, **k**, **M**, **G**, etc. or with **n**, **+n**, or **-n** (7.4)  
`$ find / -name init.d -type d 2>&1 | grep -v 'Permission denied'` (7.4)  
`$ man -f` same as typing **whatis** (8.2)  
`$ man -k` same as typing **apropos** (8.2)  
`$ man 3 printf` (8.2)  
`$ man -a printf` (8.2)  
`$ info cpio` (8.3)  
`$ help` (8.4)  
`$ help <command>` (8.4)  
`$ mkdir --help` (8.4)  
`$ gnome-help` or `$ yelp` (8.5)  
`$ khelpcenter` (8.5)  
`$ yelp man:printf` (8.5)  
`$ kill -SIGKILL <pid>` (9.1)  
`$ kill -9 <pid>` (9.1)  
`$ w` (9.2)  
`$ top` (9.2)  
`$ uptime` (9.2)  
`$ updatedb &` (9.2)  
`$ fg <job>` (9.2)  
`$ bg <job>` (9.2)  
`$ jobs` (9.2)  
`$ jobs -l` (9.2)  
`$ top | head` (9.2)  
`$ ps` (9.3)  
`$ ps -u <username>` (9.3)  
`$ ps -ef` (9.3)  
`$ ps -eLf` (9.3)  
`$ ps aux` (9.3)  
`$ ps axo <attributes>` (9.3)  
`$ pstree` (9.3)  
`$ ` ()  
`$ ` ()  

`$ ` ()  