Chapter 2: Linux Filesystem Tree Layout
---------------------------------------

[2.3: Learning Objectives](#23-learning-objectives)  
[2.4: One Big Filesystem](#24-one-big-filesystem)  
[2.5: Data Distinctions](#25-data-distinctions)  
[2.6: Filesystem Hierarchy Standard (FHS)](#26-filesystem-hierarchy-standard-fhs)  
[2.7: Main Directory Layout](#27-main-directory-layout)  
[2.8: The Root (/) Directory and Filesystems](#28-the-root--directory-and-filesystems)  
[2.9: /bin](#29-bin)  
[2.10: /boot](#210-boot)  
[2.11: Other Files and Directories in /boot](#211-other-files-and-directories-in-boot)  
[2.12: /dev](#212-dev)  
[2.13: /etc](#213-etc)  
[2.14: /home](#214-home)  
[2.15: /lib and /lib64](#215-lib-and-lib64)  
[2.16: /media](#216-media)  
[2.17: /mnt](#217-mnt)  
[2.18: /opt](#218-opt)  
[2.19: /proc](#219-proc)  
[2.20: /sys](#220-sys)  
[2.21: /root](#221-root)  
[2.22: /sbin](#222-sbin)  
[2.23: /srv](#223-srv)  
[2.24: /tmp](#224-tmp)  
[2.25: /usr](#225-usr)  
[2.26: /var](#226-var)  
[2.27: /run](#227-run)  
[2.28: Main Consumers of Storage on CentOS Demo](#228-main-consumers-of-storage-on-centos-demo)  
[Lab 2.1: Use du to calculate overall size of each of your system's top-level directories](#lab-21-use-du-to-calculate-overall-size-of-each-of-your-systems-top-level-directories)  
[Lab 2.2: Touring the /proc Filesystem](#lab-22-touring-the-proc-filesystem)  
[Paths and Commands](#paths-and-commands)  
  
### 2.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain why Linux requires the organization of one big filesystem tree, and what the major considerations are for how it is done.
* Know the role played by the Filesystem Hierarchy Standard.
* Describe what must be available at boot in the root (/) directory, and what can be available only once the system has started.
* Explain each of the main subdirectory trees in terms of purpose and contents.
  
### 2.4: One Big Filesystem
----
It's an inverted tree, with root, `/`, at the top of the tree
  
### 2.5: Data Distinctions
----
Two distinctions on what kind of information has to be read and written:
1. Shareable (between diff. hosts) vs. non-shareable (specific to a particular host)
2. Variable vs. static (require sys admin to change, like binaries, libraries, documentation)
  
### 2.6: Filesystem Hierarchy Standard (FHS)
----
  
### 2.7: Main Directory Layout
----
Does not violate **FHS** to have other directories, but to have components in directories other than those dicatated in the standard
  
**Main Directory Layout**
Directory | In FHS? | Purpose
--------- | ------- | -------
/         | Yes     | primary directory of entire FH
/bin      | Yes     | executables needed in **single user mode**
/boot     | Yes     | files needed for boot, like kernel, **initrd** or **initramfs**, bootloaders
/dev      | Yes     | **device nodes**; pseudo-filesystem
/etc      | Yes     | system-wide config files
/home     | Yes     | user **home directories**
/lib      | Yes     | libraries required by binaries in `/bin` and `/sbin`; Linux kernel modules
/lib64    | No      | 64-bit libraries required by binaries in `/bin`, `/sbin`
/media    | Yes     | mount points for removable media
/mnt      | Yes     | temporarily mounted filesystems
/opt      | Yes     | optional application software packages
/proc     | Yes     | virtual pseudo-filesystem w/ info about system and processes
/sys      | No      | virtual pseudo-filesystem w/ info about system and processes; similar to a **device tree**, part of **Unified Device Model**
/root     | Yes     | home directory for **root** user
/sbin     | Yes     | essential system binaries
/srv      | Yes     | site-specific data; seldom used
/tmp      | Yes     | temporary files; lost on reboot in many distributions
/usr      | Yes     | multi-user applications, utilities and data; theoretically read-only
/var      | Yes     | variable data that changes during system operation
/misc     | No      | some distros (see LFS201 2.7)
/run      | No      | some distros (see LFS201 2.7); pseudo-filesystem
/tftpboot | No      | some distros (see LFS201 2.7)
  
### 2.8: The Root (/) Directory and Filesystems
----
* It's special
* Must contain all essential files for booting and then mounting other file systems, config files, boot loader info, etc.; must be adequate to:
    1. boot the system
    2. restore system from backups on external media
    3. recover and/or repair the system
* No application or package should create new subdirectories of the root directory
  
### 2.9: /bin
----
* Contains executables needed before other filesystems have been mounted, for example when booting into single user or recovery mode
* May contain executables used indirectly by scripts
* May not include any subdirectories
* Required programs which must exist in `/bin/` include:
**cat, chgrp, chmod, chown, cp, date, dd, df, dmesg, echo, false, hostname, kill, ln, login, ls, mkdir, mknod, more, mount, mv, ps, pwd, rm, rmdir, sed, sh, stty, su, sync, true, umount, and uname** (**test** may be there as well, and optionally, it may include: **csh, ed, tar, cpio, gunzip, zcat, netstat and ping**)
* Less important command binaries go in `/usr/bin`; programs required only by non-root users
* Some recent distros are abandoning the separation of `/bin` and `/usr/bin`
  
### 2.10: /boot
----
Absolutely essential files for booting:
* `vmlinuz` (compressed Linus kernel)
* `initramfs` (**initial RAM Filesystem**, mounted before real root filesystem becomes available)
    * names can vary
    * `initrd` (**initial RAM disk**) instead of `initramfs` on some distros

 Stores saved master boot sectors, sector map files, other data not edited manually; used to be in `/`
  
### 2.11: Other Files and Directories in /boot
----
 Multiple kernel version available in `/boot`, and for each, there are four files (choice between kernels made by using **GRUB** at boot time); other two files, not required for boot or running:
 * `config` (config file used when compiling kernel; used for bookkeeping and reference when debugging)
 * `System.map` (kernel **symbol table**, useful for debugging; gives hex addresses of all kernel symbols)
 ? What's a kernel symbol?
  
### 2.12: /dev
----
* Contains **special device files**, or **device nodes**
* Such files represent **character** and **block I/O** devices
? like stderr, stdin, stdout? check the 101 course
* Network devices do not have device nodes and are represented by name, such as eth1 or wlan0
  
### 2.13: /etc
----
* Should not contain executable binaries
* Files which might be found:
**csh.login, exports, fstab, ftpusers, gateways, gettydefs, group, host.conf, hosts.allow, hosts.deny, hosts,equiv, hosts.lpd, inetd.conf, inittab, issue, ld.so.conf, motd, mtab, mtools.conf, networks, passwd, printcap, profile, protocols, resolv.conf, rpc, securetty, services, shells, syslog.conf**
* Red Hat adds subdirectories to `/etc`, like `/etc/sysconfig`, `/etc/skel` used to populate new home dirs, and `/etc/init.d` which contains start up and shut down scripts when using System V initialization
  
### 2.14: /home
----
In some systems, mounted automatically upon use with an **automount** facility
`$ ls -l $HOME/public_html`
`$ ls -l ~/public_html`
  
### 2.15: /lib and /lib64
----
* Contain libraries needed to execute binaries in `/bin` and `/sbin`
* kernel modules that are loadable extensions of the kernel: `/lib/modules/<kernel-version-number>`
* **PAM** (Pluggable Authentication Modules) in `/lib/security`
  
### 2.16: /media
----
Modern Linux systems mount such media dynamically upon insertion, and udev creates directories under `/media` and then mounts the removable filesystems there, with names that are set with udev rules specified in configuration files. Upon unmounting and removal, the directories used as mount points under `/media` will disappear.

If the media has more than one partition and filesystem, more than one entry will appear on `/media`. On many Linux distributions the file manager (such as Nautilus) will pop up when the media is mounted.

On some newer distributions (including SUSE and RHEL 7) removable media will pop up under `/run/media/[username]/....` We will discuss `/run` at the end of this section.
  
### 2.17: /mnt
----
* Provided so that a sys dmin can temporarily mount a FS when needed, like for network filesystems, including:
    * NFS
    * Samba
    * CIFS
    * AFS
* Used to be used for kinds of files now mounted under `/media` or `/run/media`
* Should not be used by installation programs
  
### 2.18: /opt
----
* For software packages that want all or most files in one place instead of scattered over the system
    * `/opt/application`, `/opt/application/bin`, `/opt/application/man`, etc.
    * **RPM**, **APT**, etc. make this unnecessary
* Used by application providers with proprietary software or who like to avoid complications of distribution variance, like `/opt/skype` and `/opt/google`, `/opt/google/chrome`, `/opt/google/earth`, and `/opt/google/talkplugin`
* `/opt/bin`, `/opt/doc`, `/opt/include`, `opt/info`, `/opt/lib`, and `opt/man` are reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them
  
### 2.19: /proc
----
* Pseudo-filesystem: exists only in memory, not on disk
* Empty on a non-running system like `/dev`
* Each active process has its own subdir w/ info about state of process, resources it is using, and its history
* Entries in `/proc` often termed **virtual files**; interesting properties:
    * listed as zero bytes but can contain much information
    * time and date settings are current, an indication they are constantly changing
    * in fact, information in these files obtained only when viewed; they are not being constantly or periodically updated
* Important pseudo-files, including `/proc/interrupts`, `/proc/meminfo`, `/proc/mounts`, and `/proc/partitions`, provide an up-to-the-moment glimpse of the system's hardware
* Others, like `/proc/filesystems` and the `/proc/sys/` directory, provide system configuration information and interfaces
* Files containing info about a similar topic are grouped into virtual directories, like `/proc/scsi/` for information about all physical **SCSI** devices; similar for **process** directories
  
### 2.20: /sys
----
* mount point for **sysfs pseudo-filesystem**
* empty on a non-running system like `/proc` and `/dev`
* gathers system information, modifies behavior while running
* most pseudo files contain only one line or value
  
### 2.21: /root
----
* root account should only be used for actions which require superuser privileges; otherwise, use another account
  
### 2.22: /sbin
----
* contains binaries essential for booting, restoring, recovering, and/or repairing in addition to those binaries in the `/bin` directory
* should be included: **fdisk, fsck, getty, halt, ifconfig, init, mkfs, mkswap, reboot, route, swapon, swapoff, update**
* some recent distros are abandoning the separation of `/sbin` and `/usr/sbin`
  
### 2.23: /srv
----
* helps users find location of data files for a particular service and serves as a location where read-only data, writable data, and scripts (like **cgi** scripts) can be reasonably placed
* no concensus on naming subdirectories; sometimes it's by protocol, such as **ftp, rsync, www**
* some swear by its use, others ignore it
* confusion about what is best to go here as opposed to `/var`
  
### 2.24: /tmp
----
* used for temporary files, accessible by any user or application
    * **cron** jobs might remove files older than 10 days (RHEL 6 policy)
    * some distros remove with every reboot (Ubuntu)
    * some use a virtual filesystem, using `/tmp` as a mount point for a **ram disk** using the **tmpfs** filesystem, so information lost on reboot (Fedora); in this system, files occupy memory rather than disk, so beware of large files
    * this policy can be canceled on systems (such as Fedora), using **systemd**: `$ sudo systemctl mask tmp.mount` followed by a system reboot
  
### 2.25: /usr
----
* the `/usr` directory can be thought of as a **secondary hierarchy**
* used for files not needed for booting and need not reside in the same partition as the root directory
* can be shared among hosts of the same system architecture across a network
* software packages should not create subdirectories directly under `/usr` but symlinks are okay
* typically read-only data and binaries not needed in single user mode
* contains `/usr/local` where local binaries may be stored
* **man** pages in `/usr/share/man`
  
### 2.26: /var
----
* contains variable (or volatile) data files that change frequently during system operation, including:
    * log files
    * spool (?) directories and files for printing, mail queues, etc.
    * administrative data files
    * transient and temporary files, such as cache contents
* cannot be mounted read-only, obviously
* for security reasons, good to mount as a separate filesystem
* `/var/log` contains most log files
* `/var/spool` where local files for processes such as mail, printing, and cron jobs are stored while awaiting action
  
### 2.27: /run
----
* purpose is to store transient files: those that contain runtime information, which may need to be written early in system startup, and which do not need to be preserved when rebooting
* implemented as an empty mount point, with a **tmpts** ram disk (like `/dev/shm`) mounted there at runtime; thus, `/run` is a pseudo-filesystem existing only in memory
* some locations, such as `/var/run` and `/var/lock`, will now just be symbolic links to directories under `/run`; some distros might point to location under `/run`
  
### 2.28: Main Consumers of Storage on CentOS Demo
----
  
### Lab 2.1: Use du to calculate overall size of each of your system's top-level directories
----
-c total
-h human readable
-s summarize (otherwise every file on the system!
-x skip directories on different file systems
-X exclude

Mine: `$ sudo du -cshx --exclude=/proc *`
Solution: `$ sudo du --max-depth=1 -hx /`
    > pseudo-filesystems not followed: /dev /proc /run /sys
    > symbolic links to counterparts under /usr not followed: /bin /sbin /lib /lib64
  
### Lab 2.2: Touring the /proc Filesystem
----
1. As root, cd into /proc and do a directory listing.  Notice many of the directory names are numbers; each corresponds to a running process and the name is the process ID. An important subdirectory we will discuss later is /proc/sys, under which many system parameters can be examined or modified.
* `ls -aF`
2. View the following files:
    * /proc/cpuinfo: contains information on each of my 4 cores
    * /proc/meminfo: total, free, available, active, inactive, etc. memory
    * /proc/mounts: I don't know what I'm looking at here
    * /proc/swaps: shows the /dev/sda2 partition
    * /proc/version: linux version 3.10.0-862.3.2.el7.x86_64, gcc version, Red Hat version (no mention of CentOS 7)
    * /proc/partitions: lists partitions (sda, sda1, sda2, sr0)
    * /proc/interrupts: seems like interrupts by type and CPU
Note that this information is not being constantly updated; it is obtained only when one wants to look at it.
3. Take a peek at any random process directory (if it is not a process you own some of the information might be limited unless you use sudo). Example: `$ ls -F 4435` Take a look at some of the fields in here such as: cmdline, cwd, environ, mem, and status
* I checked a couple (23 and 4615), and status had data, and cwd looks like the `/` filesystem; everything else was empty
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem | `/` | primary directory of entire FH | LFS201 2.7
filesystem | `/bin` | executables needed in **single user mode** | LFS201 2.7
filesystem | `/boot` | files needed for boot, like kernel, **initrd** or **initramfs**, bootloaders | LFS201 2.7
filesystem | `/dev` | **device nodes**; pseudo-filesystem | LFS201 2.7
filesystem | `/etc` | system-wide config files | LFS201 2.7
filesystem | `/home` | user **home directories** | LFS201 2.7
filesystem | `/lib` | libraries required by binaries in `/bin` and `/sbin`; Linux kernel modules | LFS201 2.7
filesystem | `/lib64` | 64-bit libraries required by binaries in `/bin`, `/sbin` | LFS201 2.7
filesystem | `/media` | mount points for removable media | LFS201 2.7
filesystem | `/mnt` | temporarily mounted filesystems | LFS201 2.7
filesystem | `/opt` | optional application software packages | LFS201 2.7
filesystem | `/proc` | virtual pseudo-filesystem w/ info about system and processes | LFS201 2.7
filesystem | `/sys` | virtual pseudo-filesystem w/ info about system and processes; similar to a **device tree**, part of **Unified Device Model** | LFS201 2.7
filesystem | `/root` | home directory for **root** user | LFS201 2.7
filesystem | `/sbin` | essential system binaries | LFS201 2.7
filesystem | `/srv` | site-specific data; seldom used | LFS201 2.7
filesystem | `/tmp` | temporary files; lost on reboot in many distributions | LFS201 2.7
filesystem | `/usr` | multi-user applications, utilities and data; theoretically read-only | LFS201 2.7
filesystem | `/var` | variable data that changes during system operation | LFS201 2.7
filesystem | `/misc` | some distros | LFS201 2.7
filesystem | `/run` | some distros; pseudo-filesystem | LFS201 2.7
filesystem | `/tftpboot` | some distros | LFS201 2.7
apps, filesystem | `/usr/bin` | programs required only by non-root users (as opposed to those in `/bin`) | LFS201 2.9
boot, filesystem | `vmlinuz` | file for booting (compressed Linus kernel) | LFS201 2.10
boot, filesystem | `initramfs` | (**initial RAM Filesystem**, mounted before real root filesystem becomes available) | LFS201 2.10
boot, filesystem | `initrd` | (**initial RAM disk**) instead of `initramfs` on some distros | LFS201 2.10
filesystem | `config` | config file under `/boot`; used when compiling kernel; used for bookkeeping and reference when debugging | LFS201 2.11
filesystem | `System.map` | under `/boot`; kernel **symbol table**, useful for debugging; gives hex addresses of all kernel symbols | LFS201 2.11
filesystem | `/etc/skel` | used to populate new home dirs | LFS201 2.13
filesystem | `/etc/init.d` | which contains start up and shut down scripts when using System V initialization | LFS201 2.13
filesystem | `/lib/modules/<kernel-version-number>` | contains kernel modules that are loadable extensions of the kernel | LFS201 2.15
filesystem | `/lib/security` | contains Pluggable Authentication Modules | LFS201 2.15
filesystem | `/run/media/[username]/....` | contains removable media | LFS201 2.16
filesystem | `/opt/application` | example | LFS201 2.18
filesystem | `/opt/application/bin` | example | LFS201 2.18
filesystem | `/opt/application/man` | example | LFS201 2.18
filesystem | `/opt/skype` | example | LFS201 2.18
filesystem | `/opt/google` | example | LFS201 2.18
filesystem | `/opt/google/chrome` | example | LFS201 2.18
filesystem | `/opt/google/earth` | example | LFS201 2.18
filesystem | `/opt/google/talkplugin` | example | LFS201 2.18
filesystem | `/opt/bin` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/opt/doc` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/opt/include` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `opt/info` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/opt/lib` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `opt/man` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/proc/interrupts` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/meminfo` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/mounts` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/partitions` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/filesystems` | provides system configuration information and interfaces | LFS201 2.19
filesystem | `/proc/sys/` | provides system configuration information and interfaces | LFS201 2.19
filesystem | `/proc/scsi/` | information about all physical **SCSI** devices; similar for **process** directories | LFS201 2.19
filesystem | `$ sudo systemctl mask tmp.mount` | prevent using `/tmp` as a mount point for a **ram disk** using the **tmpfs** filesystem | LFS201 2.24
filesystem | `/usr/local` | contains local binaries | LFS201 2.25
filesystem | `/usr/share/man` | contains **man** pages | LFS201 2.25
filesystem | `/var/log` | contains most log files | LFS201 2.26
filesystem | `/var/spool` | contains local files for processes such as mail, printing, and cron jobs while awaiting action | LFS201 2.26
filesystem | `/proc/cpuinfo` | contains information on each of my 4 cores | LFS201 Lab 2.2
filesystem | `/proc/meminfo` | total, free, available, active, inactive, etc. memory | LFS201 Lab 2.2
filesystem | `/proc/mounts` | I don't know what I'm looking at here | LFS201 Lab 2.2
filesystem | `/proc/swaps` | shows the /dev/sda2 partition | LFS201 Lab 2.2
filesystem | `/proc/version` | linux version 3.10.0-862.3.2.el7.x86_64, gcc version, Red Hat version (no mention of CentOS 7) | LFS201 Lab 2.2
filesystem | `/proc/partitions` | lists partitions (sda, sda1, sda2, sr0) | LFS201 Lab 2.2
filesystem | `/proc/interrupts` | seems like interrupts by type and CPU | LFS201 Lab 2.2
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem | `$ sudo du -cshx --exclude=/proc *` | calculate overall size of directories | LFS201 Lab 2.1
filesystem | `$ sudo du --max-depth=1 -hx /` | calculate overall size of directories | LFS201 Lab 2.1
  