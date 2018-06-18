LFS201 Essentials of System Administration Notes
=====

Chapter 2: Linux filesystem tree layout
-----
### Goals 2
You should be able to:
* Explain why Linux requires the organization of one big filesystem tree, and what the major considerations are for how it is done.
* Know the role played by the Filesystem Hierarchy Standard.
* Describe what must be available at boot in the root (/) directory, and what can be available only once the system has started.
* Explain each of the main subdirectory trees in terms of purpose and contents.

### Notes 2

#### 2.3 One Big Filesystem
It's an inverted tree, with root, `/`, at the top of the tree

#### 2.4 Data Distinctions
Two distinctions on what kind of information has to be read and written:
1. Shareable (between diff. hosts) vs. non-shareable (specific to a particular host)
2. Variable vs. static (require sys admin to change, like binaries, libraries, documentation)

#### 2.5 Filesystem Hierarchy Standard (FHS)

#### 2.6b Main Directory Layout II
Does not violate **FHS** to have other directories, but to have components in directories other than those dicatated in the standard

#### 2.7 The Root (`/`) Directory and Filesystems
* It's special
* Must contain all essential files for booting and then mounting other file systems, config files, boot loader info, etc.; must be adequate to:
  1. boot the system
  2. restore system from backups on external media
  3. recover and/or repair the system
* No application or package should create new subdirectories of the root directory

#### 2.8 `/bin`
* Contains executables needed before other filesystems have been mounted, for example when booting into single user or recovery mode
* May contain executables used indirectly by scripts
* May not include any subdirectories
* Required programs which must exist in `/bin/` include:
**cat, chgrp, chmod, chown, cp, date, dd, df, dmesg, echo, false, hostname, kill, ln, login, ls, mkdir, mknod, more, mount, mv, ps, pwd, rm, rmdir, sed, sh, stty, su, sync, true, umount, and uname** (**test** may be there as well, and optionally, it may include: **csh, ed, tar, cpio, gunzip, zcat, netstat and ping**)
* Less important command binaries go in `/usr/bin`; programs required only by non-root users
* Some recent distros are abandoning the separation of `/bin` and `/usr/bin`

#### 2.9 and 2.10 `/boot`
Absolutely essential:
* `vmlinuz` (compressed Linus kernel)
* `initramfs` (**initial RAM Filesystem**, mounted before real root filesystem becomes available)
  * names can vary
  * `initrd` (**initial RAM disk**) instead of `initramfs` on some distros

 Stores saved master boot sectors, sector map files, other data not edited manually; used to be in `/`

 Multiple kernel version available in `/boot`, and for each, there are four files (choice between kernels made by using **GRUB** at boot time); other two files, not required for boot or running:
 * `config` (config file used when compiling kernel; used for bookkeeping and reference when debugging)
 * `System.map` (kernel **symbol table**, useful for debugging; gives hex addresses of all kernel symbols)
 ? What's a kernel symbol?


#### 2.11 `/dev`
* Contains **special device files**, or **device nodes**
* Such files represent **character** and **block I/O** devices
? like stderr, stdin, stdout? check the 101 course
* Network devices do not have device nodes and are represented by name, such as eth1 or wlan0

#### 2.12 `/etc`
* Should not contain executable binaries
* Files which might be found:
**csh.login, exports, fstab, ftpusers, gateways, gettydefs, group, host.conf, hosts.allow, hosts.deny, hosts,equiv, hosts.lpd, inetd.conf, inittab, issue, ld.so.conf, motd, mtab, mtools.conf, networks, passwd, printcap, profile, protocols, resolv.conf, rpc, securetty, services, shells, syslog.conf**
* Red Hat adds subdirectories to `/etc`, like `/etc/sysconfig`, `/etc/skel` used to populate new home dirs, and `/etc/init.d` which contains start up and shut down scripts when using System V initialization

#### 2.13 `/home`
In some systems, mounted automatically upon use with an **automount** facility
`$ ls -l $HOME/public_html`
`$ ls -l ~/public_html`

#### 2.14 `/lib` and `/lib64`
* Contain libraries needed to execute binaries in `/bin` and `/sbin`
* kernel modules that are loadable extensions of the kernel: `/lib/modules/<kernel-version-number>`
* **PAM** (Pluggable Authentication Modules) in `/lib/security`

#### 2.15 `/media`
Modern Linux systems mount such media dynamically upon insertion, and udev creates directories under `/media` and then mounts the removable filesystems there, with names that are set with udev rules specified in configuration files. Upon unmounting and removal, the directories used as mount points under `/media` will disappear.

If the media has more than one partition and filesystem, more than one entry will appear on `/media`. On many Linux distributions the file manager (such as Nautilus) will pop up when the media is mounted.

On some newer distributions (including SUSE and RHEL 7) removable media will pop up under `/run/media/[username]/....` We will discuss `/run` at the end of this section.

#### 2.16 `/mnt`
* Provided so that a sys dmin can temporarily mount a FS when needed, like for network filesystems, including:
  * NFS
  * Samba
  * CIFS
  * AFS
* Used to be used for kinds of files now mounted under `/media` or `/run/media`
* Should not be used by installation programs

#### 2.17 `/opt`
* For software packages that want all or most files in one place instead of scattered over the system
  * `/opt/application`, `/opt/application/bin`, `/opt/application/man`, etc.
  * **RPM**, **APT**, etc. make this unnecessary
* Used by application providers with proprietary software or who like to avoid complications of distribution variance, like `/opt/skype` and `/opt/google`, `/opt/google/chrome`, `/opt/google/earth`, and `/opt/google/talkplugin`
* `/opt/bin`, `/opt/doc`, `/opt/include`, `opt/info`, `/opt/lib`, and `opt/man` are reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them

#### 2.18 `/proc`
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

#### 2.19 `/sys`
* mount point for **sysfs pseudo-filesystem**
* empty on a non-running system like `/proc` and `/dev`
* gathers system information, modifies behavior while running
* most pseudo files contain only one line or value

#### 2.20 `/root`
* root account should only be used for actions which require superuser privileges; otherwise, use another account

#### 2.21 `/sbin`
* contains binaries essential for booting, restoring, recovering, and/or repairing in addition to those binaries in the `/bin` directory
* should be included: **fdisk, fsck, getty, halt, ifconfig, init, mkfs, mkswap, reboot, route, swapon, swapoff, update**
* some recent distros are abandoning the separation of `/sbin` and `/usr/sbin`

#### 2.22 `/srv`
* helps users find location of data files for a particular service and a place for read-only data, writable data, and scripts (like **cgi** scripts) can be reasonable placed
* no concensus on naming subdirectories; sometimes it's by protocol, such as **ftp, rsync, www**
* some swear by its use, others ignore it
* confusion about what is best to go here as opposed to `/var`

#### 2.23 `/tmp`
* used for temporary files, accessible by any user or application
  * **cron** jobs might remove files older than 10 days (RHEL 6 policy)
  * some distros remove with every reboot (Ubuntu)
  * some use a virtual filesystem, using `/tmp` as a mount point for a **ram disk** using the **tmpfs** filesystem, so information lost on reboot (Fedora); in this system, files occupy memory rather than disk, so beware of large files
  * this policy can be canceled on systems (such as Fedora), using **systemd**: `$ sudo systemctl mask tmp.mount` followed by a system reboot

#### 2.24 `/usr`
* the `/usr` directory can be thought of as a **secondary hierarchy**
* used for files not needed for booting and need not reside in the same partition as the root directory
* can be shared among hosts of the same system architecture across a network
* software packages should not create subdirectories directly under `/usr` but symlinks are okay
* typically read-only data and binaries not needed in single user mode
* contains `/usr/local` where local binaries may be stored
* **man** pages in `/usr/share/man`

#### 2.25 `/var`
* contains variable (or volatile) data files that change frequently during system operation, including:
  * log files
  * spool (?) directories and files for printing, mail queues, etc.
  * administrative data files
  * transient and temporary files, such as cache contents
* cannot be mounted read-only, obviously
* for security reasons, good to mount as a separate filesystem
* `/var/log` contains most log files
* `/var/spool` where local files for processes such as mail, printing, and cron jobs are stored while awaiting action

#### 2.26 `/run`
* purpose is to store transient files: those that contain runtime information, which may need to be written early in system startup, and which do not need to be preserved when rebooting
* implemented as an empty mount point, with a **tmpts** ram disk (like `/dev/shm`) mounted there at runtime; thus, `/run` is a pseudo-filesystem existing only in memory
* some locations, such as `/var/run` and `/var/lock`, will now just be symbolic links to directories under `/run`; some distros might point to location under `/run`

### Labs 2
2.1 Use du to calculate overall size of each of your system's top-level directories
-c total
-h human readable
-s summarize (otherwise every file on the system!
-x skip directories on different file systems
-X exclude

Mine: `$ sudo du -cshx --exclude=/proc *`
Solution: `$ sudo du --max-depth=1 -hx /`
  pseudo-filesystems not followed: /dev /proc /run /sys
  symbolic links to counterparts under /usr not followed: /bin /sbin /lib /lib64

2.2 Touring the `/proc` Filesystem
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


Chapter 3: Processes
-----
### Goals 3
You should be able to:
* Describe a process and the resources associated with it.
* Distinguish between processes, programs and threads.
* Understand process attributes, permissions and states. 
* Know the difference between running in user and kernel modes. 
* Describe daemon processes.
* Understand how new processes are forked (created).
* Use nice and renice to set and modify priorities. 

### Notes 3

#### 3.3 Processes, Programs and Threads
? What's the difference? https://www.youtube.com/watch?v=exbKr6fnoUw
A process is an executing program and associated resources, including environment, open files, signal handlers, etc. The same program may be executing more than once simultaneously, and thus, be responsible for multiple processes.

At the same time, two or more tasks, or threads of execution, can share various resources, such as their entire memory spaces (or just particular memory areas), open files, etc. When there is an everything shared circumstance, one speaks of a multi-threaded process.

In other operating systems, there may be a big distinction between full heavy weight processes and light weight ones; strictly speaking, the heavy weight process may include a number of light weight processes, or just one of them.

In Linux, the situation is quite different. Each thread of execution is considered individually, the difference between heavy and light having to do only with sharing of resources and somewhat faster context switching between threads of execution.

Unlike some other operating systems, Linux has always done an exceptionally fast job of creating, destroying, and switching between processes. Thus, the model adopted for multi-threaded applications resembles multiple processes; each thread is scheduled individually and normally, as if it were a stand-alone process. This is done instead of involving more levels of complication, such as having a separate method of scheduling among the threads of a process, as well as having a scheduling method between different processes.

At the same time, Linux respects POSIX and other standards for multi-threaded processes; e.g., each thread returns the same process ID (called the thread group ID internally), while returning a distinct thread ID (called the process ID internally). This can lead to confusion for developers, but should be invisible to administrators. 

#### 3.4 The init Process
* first user process on system with ID = 1
* started as soon as the kernal is initialized and has mounted the root filesystem
* will run until system is shut down; last user process to be terminated
* ancestral parent of all other user process, both directly and indirectly

#### 3.5 Processes
* a process is an instance of a program in execution; it may be in different states, such as running and sleeping; every process has the following:
  * **pid** (process ID)
  * **ppid** (parent process ID)
  * **pgid** (process group ID)
  * program code
  * data
  * variables
  * file descriptors
  * environment
* **init** if first user process
  * ancestor of all subsequent processes running on the system
  * except those initiated directly from kernel (will have [] around their name in a **ps** listing)
* if parent process dies before child, ppid of child set to 1; process adopted by init
  * recent Linux systems using systemd will set the ppid to 2, corresponding to an internal kernel thread known as kthreadd, which has taken over the role of adopter of orphaned children from init
* A child process which terminates (either normally or abnormally) before its parent, which has not waited for it and examined its exit code, is known as a zombie (or defunct) process. Zombies have released almost all resources and remain only to convey their exit status. One function of the init process is to check on its adopted children and let those who have terminated die gracefully. Hence, it is sometimes known as the zombie killer, or more grimly, the child reaper.
* processes are controlled by **scheduling**, which is completely preemptive; only the kernel can preempt a process
* largest pid is a 16-bit number, 32768, but can be altered by changing `/proc/sys/kernel/pid_max`; when pid_max is reached, they will start again at 300
? what if all are used? or does that not happen?

#### 3.6 Process Attributes
* all processes have certain attributes:
  * program being executed
  * context (state), such as state of CPU registers, where it is executing in the program, what is in the process' memory, and other information
  * permissions
  * associated resources
* **context switching** requires the kernel to store the entire context when swapping out the process and being able to restore it upon execution resumption (such as when it's sleeping while waiting for a condition to be met, like the user to make a request or data to arrive)

#### 3.7 Controlling Processes with ulimit
* `ulimit -a`
* sysadmin can change values
  * restrict capabilities to prevent system resource exhaustion
  * expand capabilities so a process does not run into resource limits
* two kinds of limits:
  * hard: maximum value set only by root user, that a user can raise resource limit to
  * soft: current limiting value, which a user can modify but not exceed hard limit
* `$ ulimit [options] [limit]` as in `$ ulimit -n 1600`
  * only affects current shell
  * for all logged-in users, modify `/etc/security/limits.conf` and then reboot

#### 3.8 Process Permissions and setuid
* every process has permissions based on which user invoked it; may also have permissions based on who owns its program file
* **setuid** programs:
  * marked with an **s** execute bit
  * have a different **effective user ID** than the **real user ID**
  * run with the user ID of the user who **owns** the program
  * known security problem with **setuid** programs owned by root
  * example: **passwd**; any user can run it; root permission required to update the write-restricted `/etc/passwd` and `/etc/shadow` files
* non-**setuid** programs run with the permissions of the user who **runs** them

#### 3.9 More on Process States
Possible states:
* Running; either executing on a CPU or CPU core or sitting in the **run queue**, waiting for a time slice
* Sleeping (i.e., Waiting); waiting on a request (usually I/O); when request completed, kernel will put process back on run queue
* Stopped; suspended, such as when programmer wants to examine the executing program's memory, CPU registers, flags, or other attributes; generally done when process is being run under a debugger or the user hits **Ctrl-Z**
* Zombie; when process terminates and no other process (usually the parent) has inquired about its exit state, i.e. reaped it; called a **defunct** process; a zombie process has released all its resources except its exit state and its entry in the procss table; if parent dies, the process is **adopted** by **init** (PID = 1) or **kthreadd** (PID = 2)

#### 3.10 Execution Modes
A process (or any particular thread of a multi-threaded process) may be executing in either:
* **user mode** (Intel: **Ring 3**)
* **system mode** (or **kernel mode**) (Intel: **Ring 0**)
The mode is not a state of the system but the processor, and what instructions can be executed depends on the mode and is enforced at the hardware, not the software level

#### 3.11 User Mode
* **user mode**
  * mode in which processes execute except when executing a **system call**
  * in **user mdoe**, processes have lesser privileges than in kernel mode
  * each process in this mode has its own memory space, parts of which may be shared with other processes; except for the shared memory segments, a user process is not able to read or write into or from the memory space of any other process
  * even processes run by root user or as a **setuid** program run in user mode, except when jumping into a system call, and has only limited ability to access hardware
* **process resource isolation**
  * when a process is started it is isolated in its own user space to protect it from other processes
  * promotes security and creates greater stability

  User  ---System Call---> Kernel
  Mode    <---Return---    Mode

#### 3.12 Kernel Mode
* kernel mode:
  * CPU has full access to all hardware on the system, including peripherals, memory, disks, etc.
  * applications that need access to these resources must issue a **system** call, which causes a **context switch** from user mode to kernel mode; must be followed when reading and writing from files, creating a new process, etc.
  * times when the system is in kernel mode that have nothing to do with processes: handling hardware interrupts, running scheduling routines and other management tasks for the system, etc.
* application code never runs in kernel mode; it's only the system call itself which is kernel code

#### 3.13 Daemons
**daemons** run in the background and provide a specific service to users of the system
* only operate when needed (so efficient)
* many started at boot time
* name often end with **d**, such as **httpd** and **systemd-udevd**
* may respond to external events (**systemd-udevd**) or elapsed time (**crond**)
* generally have no controlling terminal and no standard input/output devices
* sometimes provide better security control
? better than what?  
when using **SysVinit**, scripts in the `/etc/init.d` directory start various system daemons; these scripts invoke commands as arguments to a shell function named **daemon**, defined in the `/etc/init.d/functions` file  
? example?
The README in `/etc/init.d` says:
>>>
[root@centos init.d]# cat README 
You are looking for the traditional init scripts in /etc/rc.d/init.d,
and they are gone?

Here's an explanation on what's going on:

You are running a systemd-based OS where traditional init scripts have
been replaced by native systemd services files. Service files provide
very similar functionality to init scripts. To make use of service
files simply invoke "systemctl", which will output a list of all
currently running services (and other units). Use "systemctl
list-unit-files" to get a listing of all known unit files, including
stopped, disabled and masked ones. Use "systemctl start
foobar.service" and "systemctl stop foobar.service" to start or stop a
service, respectively. For further details, please refer to
systemctl(1).

Note that traditional init scripts continue to function on a systemd
system. An init script /etc/rc.d/init.d/foobar is implicitly mapped
into a service unit foobar.service during system initialization.

Thank you!

Further reading:
        man:systemctl(1)
        man:systemd(1)
        http://0pointer.de/blog/projects/systemd-for-admins-3.html
        http://www.freedesktop.org/wiki/Software/systemd/Incompatibilities
>>>

#### 3.14 Creating Processes in a Command Shell
when a user executed a command in a command shell interpreter, like **bash**:
* a new process is created (forked from the user's login shell)
* a wait system call puts the parent shell process to sleep
* the command is loaded onto the child process's space via the **exec** system call, i.e., the code for the command replaces the **bash** program in the child processe's memory space
* the command completes executing, and the child process dies via the exit system call
* the parent shell is re-awakened by the death of the child process and proceeds to issue a new shell prompt; parent shell then waits for the next command request from the user, at which time the cycle will be repeated
some exceptions:
* if a command is issued for **background** processing (via the ampersand `&` at the end of the command line), the parent shell skips the wait request and is free to issue a new shell prompt immediately, allowing backround process to execute in parallel; otherwise, for **foreground** requests, the shell waits until the child process has completed or is stopped via a signal
* some shell commands (such as echo and kill) are built into the shell itself, and do not involve loading of program files; for these commands, no **for** or **exec** are issued for the execution


Linux filesystem and paths
=====

## Main Directory Layout (2.6)
Directory | In FHS? | Purpose
--------- | ------- | -------
/         | Yes     | primary directory of entire FH
/bin      | Yes     | executables needed in **single user mode**
/boot     | Yes     | files needed for boot, like kernel, initrd or initramfs, bootloaders
/dev      | Yes     | **device nodes**; pseudo-filesystem
/etc      | Yes     | system-wide config files
/home     | Yes     | user **home directories**
/lib      | Yes     | libraries required by binaries in /bin and /sbin; Linux kernel modules
/lib64    | No      | 64-bit libraries required by binaries in /bin, /sbin
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
/misc     | No      | some distros (see 2.6b)
/run      | No      | some distros (see 2.6b); pseudo-filesystem
/tftpboot | No      | some distros (see 2.6b)


Linux commands
=====
`$ sudo du -shxc --exclude=proc *`
`$ du -shc share/*`
`$ file *cgi-bin/*` # in `/usr/lib/cups` (should be in lib64)
`$ ls -lF` # difference from `lf -al` ? '-F' appends a symbol at the end of the name corresponding to the type
`$ ulimit -a` displays or resets resource limits associated with processes running under a shell
`$ ulimit [options] [limit]` as in `$ ulimit -n 1600` (changes maximum number of file descriptors)
? What's a file descriptor?
`&` at end of command for background processing (see 3.14)


