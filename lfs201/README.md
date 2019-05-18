LFS201 Essentials of Linux System Administration
=====

Contents
--------

[Links to Chapter Notes](#links-to-chapter-notes)  
[Paths](#paths)  
[Commands](#commands)  
[Main Directory Layout](#main-directory-layout)  
[Signals](#signals)  

### Links to Chapter Notes
----

[Chapter 11: System Monitoring](lfs201_chapter_notes/lfs201-11-system_monitoring.md)  
[Chapter 12: Process Monitoring](lfs201_chapter_notes/lfs201-12-process_monitoring.md)  
[Chapter 13: Memory: Monitoring Usage and Tuning](lfs201_chapter_notes/lfs201-13-memory-monitoring_usage_and_tuning.md)  
[Chapter 14: I/O Monitoring and Tuning](lfs201_chapter_notes/lfs201-14-i-o_monitoring_and_tuning.md)
[Chapter 15: I/O Scheduling](lfs201_chapter_notes/lfs201-15-i-o_scheduling.md)  
[Chapter 16: Linux Filesystems and the VFS](lfs201_chapter_notes/lfs201-16-linux_filesystems_and_vfs.md)  
[Chapter 17: Disk Partitioning](lfs201_chapter_notes/lfs201-17-disk_partitioning.md)  
[Chapter 18: Filesystem Features I: Attributes, Creating, Checking, Mounting](lfs201_chapter_notes/lfs201-18-filesystem_features_i.md)  
[Chapter 19: Filesystem Features II: Swap, Quotas, Usage](lfs201_chapter_notes/lfs201-19-filesystem_features_ii.md)  
[Chapter 20: The ext2/ext3/ext4 Filesystems](lfs201_chapter_notes/lfs201-20-the_ext_filesystems.md)  
[Chapter 21: The XFS and btrfs Filesystems](lfs201_chapter_notes/lfs201-21-the_xfs_and_btrfs_filesystems.md)  
[Chapter 22: Encrypting Disks](lfs201_chapter_notes/lfs201-22-encrypting_disks.md)  
[Chapter 23: Logical Volume Management (LVM)](lfs201_chapter_notes/lfs201-23-logical_volume_management_lvm.md)  
[Chapter 24: RAID](lfs201_chapter_notes/lfs201-24-raid.md)  
[Chapter 25: Kernel Services and Configuration](lfs201_chapter_notes/lfs201-25-kernel_services_config.md)  
[Chapter 26: Kernel Modules](lfs201_chapter_notes/lfs201-26-kernel_modules.md)  
[Chapter 27: Devices and udev](lfs201_chapter_notes/lfs201-27-devices-and-udev.md)  
[Chapter 28: Virtualization Overview](lfs201_chapter_notes/lfs201-28-virtualization-overview.md)  
[Chapter 29: Containers Overview](lfs201_chapter_notes/lfs201-29-containers-overview.md)  
[Chapter 30: User Account Management](lfs201_chapter_notes/lfs201-30-user-account-management.md)  
[Chapter 31: Group Management](lfs201_chapter_notes/lfs201-31-group-management.md)  
[Chapter 32: File Permissions and Ownership](lfs201_chapter_notes/lfs201-32-file-permissions-and-ownership.md)  
[Chapter 33: Pluggable Authentication Modules (PAM)](lfs201_chapter_notes/lfs201-33-pluggable-authentication-modules-pam.md)  
[Chapter 34: Network Addresses](lfs201_chapter_notes/lfs201-34-network-addresses.md)  
[Chapter 35: Network Devices and Configuration](lfs201_chapter_notes/lfs201-35-network-devices-and-configuration.md)  
[Chapter 36: Firewalls](lfs201_chapter_notes/lfs201-36-firewalls.md)  
[Chapter 37: System Startup and Shutdown](lfs201_chapter_notes/lfs201-37-system-startup-and-shutdown.md)  
[Chapter 38: GRUB](lfs201_chapter_notes/lfs201-38-grub.md)  
[Chapter 39: init: SystemV, Upstart, systemd](lfs201_chapter_notes/lfs201-39-init-systemv,-upstart,-systemd.md)  
[Chapter 40: Backup and Recovery Methods](lfs201_chapter_notes/lfs201-40-backup-and-recovery-methods.md)  
[Chapter 41: Linux Security Modules](lfs201_chapter_notes/lfs201-41-linux-security-modules.md)  
[Chapter 42: Local System Security](lfs201_chapter_notes/lfs201-42-local-system-security.md)  
[Chapter 43: Basic Troubleshooting](lfs201_chapter_notes/lfs201-43-basic-troubleshooting.md)  
[Chapter 44: System Rescue](lfs201_chapter_notes/lfs201-44-system-rescue.md)  

### Paths
----

### Commands
----

### Main Directory Layout
----

**Main Directory Layout (LFS201 2.7)**
  
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
  
### Signals
----
  
**Available Signals for the x86 Platform**
  
Signal    | Value | Default Action | POSIX? | Meaning
------    | ----- |--------------- | ------ | -------
SIGHUP    | 1     | Terminate      | Yes    | Hangup detected on controlling terminal or death of controlling process.
SIGINT    | 2     | Terminate      | Yes    | Interrupt from keyboard.
SIGQUIT   | 3     | Core dump      | Yes    | Quit from keyboard.
SIGILL    | 4     | Core dump      | Yes    | Illegal Instruction.
SIGTRAP   | 5     | Core dump      | No     | Trace/breakpoint trap for debugging.
SIGABRT   | 6     | Core dump      | Yes    | Abnormal termination.
SIGIOT    | 6     | Core dump      | Yes    | Abnormal termination.
SIGBUS    | 7     | Core dump      | Yes    | Bus error.
SIGFPE    | 8     | Core dump      | Yes    | Floating point exception.
SIGKILL   | 9     | Terminate      | Yes    | Kill signal (can not be caught or ignored).
SIGUSR1   | 10    | Terminate      | Yes    | User-defined signal 1.
SIGSEGV   | 11    | Core dump      | Yes    | Invalid memory reference.
SIGUSR2   | 12    | Terminate      | Yes    | User-defined signal 2.
SIGPIPE   | 13    | Terminate      | Yes    | Broken pipe: write to pipe with no readers.
SIGALRM   | 14    | Terminate      | Yes    | Timer signal from alarm. 
SIGTERM   | 15    | Terminate      | Yes    | Process termination. 
SIGSTKFLT | 16    | Terminate      | No     | Stack fault on math co-processor. 
SIGCHLD   | 17    | Ignore         | Yes    | Child stopped or terminated. 
SIGCONT   | 18    | Continue       | Yes    | Continue if stopped. 
SIGSTOP   | 19    | Stop           | Yes    | Stop process (can not be caught or ignored). 
SIGTSTP   | 20    | Stop           | Yes    | Stop types at tty. 
SIGTTIN   | 21    | Stop           | Yes    | Background process requires tty input. 
SIGTTOU   | 22    | Stop           | Yes    | Background process requires tty output. 
SIGURG    | 23    | Ignore         | No     | Urgent condition on socket (4.2 BSD).
SIGXCPU   | 24    | Core dump      | Yes    | CPU time limit exceeded (4.2 BSD). 
SIGXFSZ   | 25    | Core dump      | Yes    | File size limit exceeded (4.2 BSD). 
SIGVTALRM | 26    | Terminate      | No     | Virtual alarm clock (4.2 BSD). 
SIGPROF   | 27    | Terminate      | No     | Profile alarm clock (4.2 BSD). 
SIGWINCH  | 28    | Ignore         | No     | Window resize signal (4.3 BSD, Sun).
SIGIO     | 29    | Terminate      | No     | I/O now possible (4.2 BSD) (System V). 
SIGPOLL   | 29    | Terminate      | No     | I/O now possible (4.2 BSD) (System V). 
SIGPWR    | 30    | Terminate      | No     | Power Failure (System V). 
SIGSYS    | 31    | Terminate      | No     | Bad System Called. Unused signal.
SIGUNUSED | 31    | Terminate      | No     | Bad System Called. Unused signal. 
