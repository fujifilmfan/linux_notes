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

#### Lab 2.1 Use du to calculate overall size of each of your system's top-level directories
-c total
-h human readable
-s summarize (otherwise every file on the system!
-x skip directories on different file systems
-X exclude

Mine: `$ sudo du -cshx --exclude=/proc *`
Solution: `$ sudo du --max-depth=1 -hx /`
  pseudo-filesystems not followed: /dev /proc /run /sys
  symbolic links to counterparts under /usr not followed: /bin /sbin /lib /lib64

#### Lab 2.2 Touring the `/proc` Filesystem
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
* **init** is first user process
  * ancestor of all subsequent processes running on the system
  * except those initiated directly from kernel (will have [] around their name in a **ps** listing)
* if parent process dies before child, ppid of child set to 1; process adopted by init
  * recent Linux systems using systemd will set the ppid to 2, corresponding to an internal kernel thread known as kthreadd, which has taken over the role of adopter of orphaned children from init
* A child process which terminates (either normally or abnormally) before its parent, which has not waited for it and examined its exit code, is known as a **zombie** (or defunct) process. Zombies have released almost all resources and remain only to convey their exit status. One function of the init process is to check on its adopted children and let those who have terminated die gracefully. Hence, it is sometimes known as the zombie killer, or more grimly, the child reaper.
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

> [root@centos init.d]# cat README 
> You are looking for the traditional init scripts in /etc/rc.d/init.d, and they are gone?
> 
> Here's an explanation on what's going on:
> 
> You are running a systemd-based OS where traditional init scripts have been replaced by native systemd services files. Service files provide very similar functionality to init scripts. To make use of service files simply invoke "systemctl", which will output a list of all currently running services (and other units). Use "systemctl list-unit-files" to get a listing of all known unit files, including stopped, disabled and masked ones. Use "systemctl start foobar.service" and "systemctl stop foobar.service" to start or stop a service, respectively. For further details, please refer to systemctl(1).
> 
> Note that traditional init scripts continue to function on a systemd system. An init script /etc/rc.d/init.d/foobar is implicitly mapped into a service unit foobar.service during system initialization.
> 
> Thank you!
> 
> Further reading:  
>         man:systemctl(1)  
>         man:systemd(1)  
>         http://0pointer.de/blog/projects/systemd-for-admins-3.html  
>         http://www.freedesktop.org/wiki/Software/systemd/Incompatibilities  

#### 3.14 Creating Processes in a Command Shell
when a user executed a command in a command shell interpreter, like **bash**:
* a new process is created (forked from the user's login shell)
* a wait system call puts the parent shell process to sleep
* the command is loaded onto the child process's space via the **exec** system call, i.e., the code for the command replaces the **bash** program in the child processe's memory space
* the command completes executing, and the child process dies via the exit system call
* the parent shell is re-awakened by the death of the child process and proceeds to issue a new shell prompt; parent shell then waits for the next command request from the user, at which time the cycle will be repeated
some exceptions:
* if a command is issued for **background** processing (via the ampersand `&` at the end of the command line), the parent shell skips the wait request and is free to issue a new shell prompt immediately, allowing backround process to execute in parallel; otherwise, for **foreground** requests, the shell waits until the child process has completed or is stopped via a signal
* some shell commands (such as echo and kill) are built into the shell itself, and do not involve loading of program files; for these commands, no **fork** or **exec** are issued for the execution

#### 3.15 Kernel-Created Processes
the **Linux** kernel creates two kinds of processes on its own initiative (not created, or **forked** from user parents):
* internal kernel processes
  * maintenance work, such as ensuring that:
    * buffers get flushed out to disk
    * the load on different CPUs is balanced evenly
    * device drivers handle work that has been queued up for them to do
  * these often run as long as the system is running, sleeping when they don't have something to do
* external user processes
  * run in user space like normal applications but which the kernel started
  * very few of these and usually short-lived  
  ? what is 'user space'?
* run `$ ps -elf` to see processes of this nature
  * they will have **PPID = 2**, corresponding to **kthreadd**
  * names encapsulated in square brackets, such as **[ksoftirqd/0]**

#### 3.16 Process Creating and Forking
* **forking** == creating new (child) processes
* often followed by an **exec**, in which the parent process terminates and the child process inherits the PID of the parent
* older UNIX systems used **spawn** (not part of the **POSIX** standard or a normal part of Linux)
* an application (like a web server handling many clients) can start many new processes or start new threads as part of the same process; there isn't much difference with respect to time and resources
* example:
  * the **init** process executes the **sshd** init script, which then launches the **sshd daemon** (the daemon listens for **ssh** requests from remote users)
  * when a request is received, **sshd** creates a copy of itself to service the requests, so each remote user gets their own copy of the **sshd** daemon running to service their remote login
  * the **sshd** process will start the login program to validate the user; if successful, the login process will fork off a shell (like **bash**) to interpret user commands, and so on

#### 3.17 Using nice to Set Priorities
* process priority can be controlled via **nice** and **renice** commands
* a *nice* process yields to other, so higher niceness == lower priority
* niceness range: -20 (highest priority) to +19 (lowest priority)
* examples (these are equivalent):
  * `$ nice -n 5 command [ARGS]`
  * `$ nice -5 command [ARGS]`
  ? This doesn't seem to work in CentOS 7; running `$ nice -n 5 cat`, for instance, still expects input, and adding `&` creates a stopped process instead of reducing priority of the existing process; ah, see next section
* if you do not give a nice value, default is to increase niceness by 10
* no args reports current niceness
* examples:
  * `$ nice` --> 0
  * `$ nice cat &` --> [1] 24908
  * `$ ps -l`
* increasing the niceness does not mean a process won't run (such as if there is nothing else with which to compete)

#### 3.18 Modifiying the Nice Value
* by default, only a superuser can decrease niceness
* normal users can get that ability via `/etc/security/limits.conf`
* to change niceness of an already running process, use renice:
  *  `$ renice +3 13848`, where 13848 is the pid of the process; 3 would be the new niceness, not the change
  * more than one process can be changed at a time; see the man page

#### 3.19 Using renice to Set Priorities Demo
* run `ps lf` to see processes and priorities
* `renice +5 3077`
* child processes have same, new priority
* can increase niceness as normal user but not decrease: "Permission denied"
* can run 'gnome-system-monitor' and change priorities there, too

#### 3.20 Static and Shared Libraries
* two types of **libraries** of code:
  * **static**: code for library functions inserted in program at **compile time** and then does not change even if the library is updated
  * **shared**: code for library functions loaded in program at **run time**, and if library is changed later, running program runs with new library modifications
    * shared libraries more efficient b/c they can be used by many applications at once; memory usage, executable sizes, and application load time are reduced
    * also called **DLL**s (**D**ynamic **L**ink **L**ibrary)

#### 3.21 Shared Library Versions
* shared libraries need to be carefully versioned to avoid **DLL Hell** (programs breaking when the library changes)
* some programs embed or bundle the library, but then can't benefit from bug fixes, security patches, etc. as quickly
* shared libraries have the extension **.so**
* full name like **libc.so.N** where **N** is a major version number
* example:  
  `$ ls -lF libgdbm.so.*  
  lrwxrwxrwx. 1 root root    16 May 10 11:56 libgdbm.so.4 -> libgdbm.so.4.0.0*  
  -rwxr-xr-x. 1 root root 36720 Jun  9  2014 libgdbm.so.4.0.0*  
  $ `  

#### 3.22 Finding Shared Libraries
**ldd** can be used to find what shared libraries an executable requires
* it shows the **soname** of the library and what file it points to
* example:  
`[student@centos lib]$ ldd /usr/bin/vi`

> linux-vdso.so.1 =>  (0x00007ffda6469000)
> libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fc3686e2000)
> libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007fc3684b8000)
> libacl.so.1 => /lib64/libacl.so.1 (0x00007fc3682af000)
> libc.so.6 => /lib64/libc.so.6 (0x00007fc367ee2000)
> libpcre.so.1 => /lib64/libpcre.so.1 (0x00007fc367c80000)
> libdl.so.2 => /lib64/libdl.so.2 (0x00007fc367a7c000)
> /lib64/ld-linux-x86-64.so.2 (0x00007fc368909000)
> libattr.so.1 => /lib64/libattr.so.1 (0x00007fc367877000)
> libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fc36765b000)

`[student@centos lib]$ `
* **ldconfig** is generally run at boot time and uses the file `/etc/ld.so.conf`, which lists the directories searched for shared libraries
  * must be run as root
* besides searching the data base built by **ldconfig**, the linker will first search directories specified by the **LD_LIBRARY_PATH** environment variable
* one can do:
`$ LD_LIBRARY_PATH=$HOME/foo/lib`  
`$ foo [args]`  
  or  
`$ LD_LIBRARY_PATH=$HOME/foo/lib foo [args]`  

### Labs 3

#### Lab 3.1 Controlling Processes with ulimit
Please do:
`$ help ulimit` and read /etc/security/limits.conf before doing the following steps.
1. Start a new shell by typing bash (or opening a new terminal) so that your changes are only effective in the new shell.  View the current limit on the number of open files and explicitly view the hard and soft limits.
2. Set the limit to the hard limit value and verify if it worked.
3. Set the hard limit to 2048 and verify it worked.
4. Try to set the limit back to the previous value. Did it work?

Mine:
1. typed `$ bash` to start a new shell and confirmed with `$ ps`.
`$ ulimit -a`  

> core file size          (blocks, -c) 0  
> data seg size           (kbytes, -d) unlimited  
> scheduling priority             (-e) 0  
> file size               (blocks, -f) unlimited  
> pending signals                 (-i) 14969  
> max locked memory       (kbytes, -l) 64  
> max memory size         (kbytes, -m) unlimited  
> open files                      (-n) 1024  
> pipe size            (512 bytes, -p) 8  
> POSIX message queues     (bytes, -q) 819200  
> real-time priority              (-r) 0  
> stack size              (kbytes, -s) 8192  
> cpu time               (seconds, -t) unlimited
> max user processes              (-u) 4096  
> virtual memory          (kbytes, -v) unlimited  
> file locks                      (-x) unlimited

`$ ulimit -H -n`

> 4096

`$ ulimit -S -n`

> 1024

2. Set soft limit to hard limit value
`$ ulimit -n 4096
$ ulimit -S -n`

> 4096

3. Set hard limit to 2048
`$ ulimit -H -n 2048`

> 4096

This doesn't work, even with sudo or changing user to root; need to change the soft limit first:

`[student@centos ~]$ ulimit -S -n 2048
[student@centos ~]$ ulimit -H -n 2048
[student@centos ~]$ ulimit -H -n 4096`
> bash: ulimit: open files: cannot modify limit: Operation not permitted
`[student@centos ~]$ sudo ulimit -H -n 4096`
> sudo: ulimit: command not found

4. Change to root user and set hard limit back to 4096

`[student@centos ~]$ sudo su -`
> Last login: Mon Jun 18 01:34:21 CDT 2018 on pts/0
`[root@centos ~]# ulimit -H -n 4096
[root@centos ~]# exit`
> logout

This didn't work:
`[student@centos ~]$ ulimit -H -n`

> 2048

Solution:

2. `$ ulimit -n hard`

3. `$ ulimit -n 2048`

4. We can't do this anymore, but a limit like stack size (-s) could be raised since the hard limit is `unlimited`

#### Lab 3.2 Examining System V IPC Activity
**System V IPC** is an old method of **I**nter **P**rocess **C**ommunication that dates to the early days of **UNIX**; involves three mechanisms:
1. **Shared Memory Segments**
2. **Semaphores**
3. **Message Queues**
Modern programs tend to use **POSIX IPC** methods for these mechanisms, but still plenty of System V IPC applications found in the wild

`$ ipcs`  
> 
> ------ Message Queues --------
> key        msqid      owner      perms      used-bytes   messages    
> 
> ------ Shared Memory Segments --------
> key        shmid      owner      perms      bytes      nattch     status      
> 0x00000000 3637248    student    600        16777216   2          dest         
> 0x00000000 163841     student    600        4194304    2          dest         
> 0x00000000 360450     student    600        524288     2          dest         
> 
> ------ Semaphore Arrays --------
> key        semid      owner      perms      nsems     

Shared memory segments have a key of 0 (also known as `IPC_PRIVATE`), which means they are only shared between processes in a parent-child relationship. "Dest" means marked for destruction when there are no further attachments.  If there is no status, then there are no attachments, so it might persist forever and leak memory if no subsequent process attaches to it.

`$ ipcs -p`  
> 
> ------ Message Queues PIDs --------
> msqid      owner      lspid      lrpid     
> 
> ------ Shared Memory Creator/Last-op PIDs --------
> shmid      owner      cpid       lpid      
> 262144     student    11310      3869      
> 851969     student    12144      12151     
> 524291     student    11044      11780     
> 688132     student    11737      3869  

The `-p` option shows the PIDs of creator and last operator.

`$ ps aux |grep -e 11737 -e 11044`  
> student   11044  1.4  3.7 3688388 144132 ?      Sl   18:16   0:12 /usr/bin/gnome-shell
> student   11737  0.0  0.8 760220 32268 ?        Sl   18:17   0:00 /usr/bin/gedit --gapplication-service
> student   12431  0.0  0.0 112708   980 pts/0    S+   18:31   0:00 grep --color=auto -e 11737 -e 11044


Chapter 4: Signals
-----
### Introduction 4
**signals** emit notifications between processes or from kernal to app
* one of the oldest forms of **inter-process communication (IPC)**
* used to signal when an action is completed or to terminate a process, for example
* the command for sending a signal is named "kill", but not all signals are kill signals

### Goals 4
By the end of this chapter, you should be able to:
* Explain what signals are and how they are used.
* Know the different signals and types of signals available in Linux.
* Use kill, killall and pkill to send signals from the command line.

### Notes 4

#### 4.3 What Are Signals?
* **Signals** are one of the oldest methods of **Inter-Process Communication (IPC) and are used to notify processes about **asynchronous** events (or exceptions); by asynchronous, we mean the signal-receiving process may:
  * not expect the event to occur
  * expect the event, but not know when it is most likely to occur
* for example, if a user decides to terminate a running program, it could send a signal to the process through the kernel to interrupt and kill the process.  
* there are two pathy by which signals are sent to a process:
  * from the kernel to a user process, as a result of an exception or programming error
  * from a user process (using a system call) to the kernel which will then send it to a user process; the process sending the signal can be the same as the one receiving it
* signals can only be sent:
  * between processes owned by the same user
  * from a process ownded by the superuser to any process
* what the process does when receiving a signal depends on the way the program was written:
  * it can take actions to **handle** the signal
  * it can respond according to system defaults
  * **SIGKILL** and **SIGSTOP** cannot be handled and will always terminate the program

#### 4.4 Types of Signals
* there are different types of signals, and the particular signal dispatched indicates the type of event that occurred; generally, signals handle two things:
  * exceptions detected by hardware (e.g. illegal memory reference)
  * exceptions generated by the environment (e.g. premature death of a process from user's terminal)
* use `$ kill -l` to see a list of signals with their numbers  
* the signals from **SIGRTMIN** (34) and on are called **real-time signals** and are arelatively recent addition
  * they have no predefined purpose
  * they can be queued up and are handled in a **FIFO** **F**irst **I**n **F**irst **O**ut) order
* the meaning attached to the signal type indicates what event caused the signal to be sent
* type `$ man 7 signal` for documentation

#### 4.5 kill
* a process cannot send a signal directly to another process; it must ask the kernel to send the signal by executing a **system call**
* users, including the superuser, can send signals to process by using **kill**:
  * `$ kill 1991` (PID = 1991; will send a **SIGTERM (15)** by default, which can be handled; program can take elusive action or clean up after itself rather than die immediately)
  * `$ kill -9 1991` (**SIGKILL (9)** cannot be ignored, causes termination with extreme prejudice)
  * `$ kill -SIGKILL 1991`
* **kill** is a misnomer b/c it is used to send any and all signals, even benign, informative ones
* passing **kill -SIGTSTP** or pressing **Ctrl+Z** do the same thing

#### 4.6 killall and pkill
* **killall** kills all processes with a given name; uses a command name rather than PID:
  * `$ killall bash`
  * `$ killall -9 bash`
  * `$ killall -SIGKILL bash`
* **pkill** sends a signal to a process using selection criteria
  * `$ pkill [-signal] [options] [pattern]`
  * `$ pkill -u libby foobar` (will kill all of **libby**'s processes with a name of **foobar**)
  * `$ pkill -HUP rsyslogd` (makes **rsyslog** re-read its config file)

### Labs 4

#### Lab 4.1 Examining Signal Priorities and Execution

When run, the signals program:
* Does not send the signals SIGKILL or SIGSTOP, which can not be handled and will always terminate a program.
* Stores the sequence of signals as they come in, and updates a counter array for each signal that indicates how many times the signal has been handled.
* Begins by suspending processing of all signals and then installs a new set of signal handlers for all signals.
* Sends every possible signal to itself multiple times and then unblocks signal handling and the queued up signal handlers will be called.
* Prints out statistics including:
  * The total number of times each signal was received.
  * The order in which the signals were received, noting each time the total number of times that signal had been received up to that point.
Note the following:
* If more than one of a given signal is **raised** while the process has blocked it, does the process **receive** it multiple times? Does the behavior of **real time** signals differ from normal signals?
* Are all signals received by the process, or are some handled before they reach it?
* What order are the signals received in?
One signal, SIGCONT (18 on **x86**) may not get through; can you figure out why? Note:  
On some **Linux** distributions signals 32 and 33 can not be blocked and will cause the program to fail. Even though system header files indicate SIGRTMIN=32, the command kill -l indicates SIGRTMIN=34.
Note that **POSIX** says one should use signal names, not numbers, which are allowed to be completely implementation dependent. You should generally avoid sending these signals.

Chapter 5: Package Management Systems
-----
### Introduction 5

### Goals 5
By the end of this chapter, you should be able to:
* Explain why software package management systems should be used.
* Understand the role of both binary and source packages.
* List the main available package management systems.
* Understand why two levels of utilities are needed; one that deals with just bare packages, and one that deals with dependencies among packages.
* Explain how creating your own package enhances the control you have over exactly what goes in software and how it is installed.
* Understand the role of source control systems and git in particular.

### Notes 5

#### 5.3 Software Packaging Concepts
**Package management systems** supply the tools that allow system administrators to automate installing, upgrading, configuring and removing software packages in a known, predictable and consistent manner. These systems:
* Gather and compress associated software files into a single package (archive), which may require one or more other packages to be installed first.​
* Allow for easy software installation or removal.​
* Can verify file integrity via an internal database.​
* Can authenticate the origin of packages.​
* Facilitate upgrades.​
* Group packages by logical features.​
* Manage dependencies between packages.
A given package may contain executable files, data files, documentation, installation scripts and configuration files. Also included are **metadata** attributes such as version numbers, checksums, vendor information, dependencies, descriptions, etc.  
Upon installation, all that information is stored locally into an internal database which can be conveniently queried

#### 5.4 Why Use Packages
Features include:
* Automation:  No need for manual installs and upgrades.
* Scalability:  Install packages on one system, or 10,000 systems.
* Repeatability and predictability.
* Security and auditing.

#### 5.5 Package Types
Packages come in several different types:
* **Binary** packages contain files ready for deployment, including executable files and libraries. These are architecture-dependent and must be compiled for each type of machine.
* **Source** packages are used to generate binary packages; one should always be able to rebuild a binary package (for example, by using `rpmbuild --rebuild` on **RPM**-based systems) from the source package. One source package can be used for multiple architectures.
* **Architecture-independent** packages contain files and scripts that run under script interpreters, as well as documentation and configuration files.
* **Meta-packages** are essentially groups of associated packages that collect everything needed to install a relatively large subsystem, such as a desktop environment, or an office suite, etc.
Binary packages are the ones that system administrators have to deal with most of the time.  
On 64-bit systems that can run 32-bit programs, one may have two binary packages installed for a given program, perhaps one with **x86_64** or **amd64** in its name, and the other with **i386** or **i686** in its name.  
Source packages can be helpful in keeping track of changes and source code used to come up with binary packages. 

#### 5.6 Available Package Management Systems
There are two very common package management systems:

* **RPM** (**Red Hat Package Manager**)
  * This system is used by all Red Hat-derived distributions, such as **Red Hat Enterprise Linux**, **CentOS**, and **Scientific Linux**, as well as by **SUSE** and its related community **openSUSE** distribution.
* **dpkg** (**Debian Package**)
  * This system is used by all **Debian**-derived distributions ,including **Debian**, **Ubuntu**, and **Linux Mint**.
There are other package management systems, such as **portage/emerge** used by **Gentoo**, **pacman**** used by **Arch**, and specialized ones used by **Embedded Linux** systems and **Android**.

#### 5.7 Packaging Tool Levels and Varieties
Two levels to packaging systems:
1. **Low Level Utility**
* installs or removes a single package or list of packages; dependencies not fully handled
  * if another package needs to be installed, first installation will fail
  * if the package is needed by another package, removal will fail
  * the **rpm** and **dpkg** utilities play this role for the packaging systems that use them

2. **High Level Utility**
* solves the dependency problems
  * if another package or group of packages needs to be installed before software can be installed, such needs will be satisfied
  * if removing a package interferes with another installed package, the administrator will be given the choice of either aborting, or removing all affected software
  * the **yum**, **dnf**, and **zypper** utilities (and more recently, **PackageKit**) take care of the dependency resolution for rpm systems, and **apt-get** and **apt-cache** and other utilities take care of it for dpkg systems

#### 5.8 Package Sources
* every distribution has one or more package **repositories** where system utilities go to obtain software and to update with new versions
* there are always other, external repositories, which can be added to the standard distribution-supported list; sometimes, these are closely associated with the distribution, and only rarely produce significant problems; an example would be the **EPEL** (**E**xtra **Pa**ckages for **E**nterprise **L**inux) set of version-dependent repositories, which fit well with **RHEL** since their source is **Fedora** and the maintainers are close to **Red Hat**. 
* when a package is updated in the main repository, dependent packages may not be updated in the external one, which can lead to one form of **dependency hell** 

#### 5.9 Creating Software Packages
* Building your own package allows you to control exactly what goes in the software and exactly how it is installed; can automate tasks like:
  * creating needed symbolic links
  * creating directories as needed
  * setting permissions
  * anything that can be scripted
* mechanisms of how to build **.rpm** or **.deb** packages will not be discussed

#### 5.10 Revision Control System
* **Source Control Systems** (or Revision Control Systems) coordinate cooperative development

#### 5.11 Available Source Control Systems
Some products released under **GPL** license:

**Product**| **URL**
-----------| -------
RCS        | http://www.gnu.org/software/rcs
CVS        | http://ximbiot.com/cvs/wiki 
Subversion | http://subversion.tigris.org 
git        | http://www.kernel.org/pub/software/scm/git
GNU Arch   | http://www.gnu.org/software/gnu-arch 
Monotone   | http://www.monotone.ca
Mercurial  | http://mercurial-scm.org/
PRCS       | http://prcs.sourceforge.net

Git arose from the Linux kernel development community  

#### 5.12 The Linux Kernel and the Birth of git
* see the section for details
* original author was Linus Torvalds
* links:
  * source code: http://www.kernel.org/pub/software/scm/git/
  * documentation: http://www.kernel.org/pub/software/scm/git/docs/

#### 5.13 How git Works
* **git** is not a source control management system in the usual sense, and basic units it works with are not files
* two important data structures: an **object** database and a directory cache
* object database contains objects of three varieties:
  * **blobs**: chunks of binary data containing file contents
  * **trees**: sets of blobs including file names and attributes, giving the directory structure
  * **commits**: Changesets describing tree snapshots
* directory cache captures the state of the directory tree

### Labs 5

#### Lab 5.1
Basic git stuff; nothing to note  

Chapter 6: RPM
-----
### Introduction 6

### Goals 6
By the end of this chapter, you should be able to:
* Understand how the **RPM** system is organized and what major operations the **rp** program can accomplish.
* Explain the naming conventions used for both binary and source **rpm** files.
* Know how to query, verify, install, uninstall, upgrade and freshen packages.
* Grasp why new kernels should be installed rather than upgraded.
* Know how to use **rpm2cpio** to copy packaged files into a **cpio** archive, as well as to extract the files without installing them.
* **yum** and **zypper** hanlded dependencies in a more robust fashion; rpm more for packages listed on command line (?)

### Notes 6

#### 6.3 RPM
* **RPM** == the **R**ed **H**at **P**ackage **M**anager
  * all files related to a specific task are packaged into a single rpm file
  * contains information about how and where to install and uninstall files
  * new versions of software lead to new rpm files which are then used for updating
* rpm files contain dependency information
* rpm does not retrieve from the network, but only from the local machine using absolute or relative paths
* rpm files are usually distribution-dependent

#### 6.4 Advantages of Using RPM
For system administrators, RPM makes it easy to:  
  * determine what package (if any) any file on the system is part of
  * determine what version is installed
  * install and uninstall (erase) packages without leaving debris behind
  * verify that a package was installed correctly; this is useful for both troubleshooting and system auditing
  * distinguish documentation files from the rest of the package and optionally decide not to install them to save space
  * use ftp or HTTP to install packages over the Internet
For developers RPM offers advantages as well:  
  * software often is made available on more than one operating system; with RPM the original full and unmodified source is used as the basis, but a developer can separate out the changes needed to build on Linux
  * more than one architecture can be built using only one source package

#### 6.5 Package File Names
RPM package file names are based on fields that represent specific information, as documented in the RPM standard  (http://www.rpm.org/)  
  * the standard naming format for a binary package is:
    * <name>-<version>-<release>.<distro>.<architecture>.rpm
    * sed-4.2.1-10.el6.x86_64.rpm
  * the standard naming format for a source package is:
    * <name>-<version>-<release>.<distro>.src.rpm
    * sed-4.2.1-10.el6.src.rpm
Note that the distro field often actually specifies the repository that the package came from, as a given installation may use a number of different package repositories as we shall discuss when we discuss yum and zypper which work above RPM.  

#### 6.6 Database Directory
* `/var/lib/rpm` is the default system directory which holds RPM database files in the form of **Berkeley DB** hash files; database files should not be manually modified; updates should be done only through use of the rpm program
* an alternative database directory can be specified with the **--dbpath** option to the rpm program; one might do this, for example, to examine an RPM database copied from another system
* you can use the **--rebuilddb** option to rebuild the database indices from the installed package headers; this is more of a repair, and not a rebuild from scratch

#### 6.7 Helper Programs and Modifying Settings
* Helper programs and scripts used by RPM reside in /usr/lib/rpm; there are quite a few; for example on a RHEL 7 system:  
`$ ls /usr/lib/rpm | wc -l`
> 73
where wc is reporting the number of lines of output
* you can create an rpmrc file to specify default settings for rpm; by default, rpm looks for the following in order:
  * `/usr/lib/rpm/rpmrc`
  * `/etc/rpmrc`
  * `~/.rpmrc`
* note all these files are read; rpm does not stop as soon as it finds that one exists; an alternative rpmrc file can be specified using the **--rcfile** option

#### 6.8 Queries
All rpm inquiries include the **-q** option, which can be combined with numerous sub-options, as in:
  * Which version of a package is installed?
    `$ rpm -q bash`
  * Which package did this file come from?
    `$ rpm -qf /bin/bash` # **-f** for file
  * What files were installed by this package?
    `$ rpm -ql bash`
  * Show information about this package.
    `$ rpm -qi bash`
  * Show information about this package from the package file, not the package database.
    `$ rpm -qip foo-1.0.0-1.noarch.rpm` (didn't work for me, and not when using packages on my system, either - "No such file or directory")
  * List all installed packages on this system.
    `$ rpm -qa`
  * Return a list of prerequisites for a package:
    `$ rpm -qp --requires foo-1.0.0-1.noarch.rpm`
  * Show what installed package provides a particular requisite package:
    `$ rpm -q --whatprovides libc.so.6`

#### 6.9 Verifying Packages
* **-V** option to **rpm** lets you verify whether files from package are consistent with database
* to verify all packages on system:
  * `$ rpm -Va`
* output generated only when there is a probem (my CentOS7 packages seem to have a lot of problems, then)
* codes (in order):

Character | Meaning
--------- | -------
S         | file size differs
M         | file permissions and/or type differs
5         | MD5 checksum differs
D         | device major/minor number mismatch
L         | symbolic link path mismatch
U         | user ownership differs
G         | group ownership differs
T         | modification time differs
P         | capabilities differ
.         | (at any of the above positions) test passed
?         | (at any of the above positions) test cannot be performed

* these do not necessarily indicate a problem; for example, many config files are modified as system evolves 
* if you specify one or more package names as arguments, you examine only that package:
  * no output when everything is okay: 
  `$ rpm -V bash`  
  * output indicating that a file's size, checksum, and modification time have changed:
  `$ rpm -V talk`  
  > S.5....T in.ntalkd.8  
  * output indicating that a file is missing:
  `$ rpm -V talk`  
  > missing /usr/bin/talk  

#### 6.10 Installing Packages
Installing a package is as simple as:  
`$ sudo rpm -ivh foo-1.0.0-1.noarch.rpm`  where the **-i** is for install, **-v** is for verbose, and **-h** just means print out hash marks while doing to show progress.

RPM performs a number of tasks when installing a package:
  * performs dependency checks
  * performs conflict checks
  * executes commands required before installation
  * deals intelligently with configuration files: when installing a configuration file, if the file exists and has been changed since the previous version of the package was installed, RPM saves the old version with the **suffix .rpmsave**; allows you to integrate the changes in the old config file into the new version of the file
  * unpacks the files from packages and installs them with correct attributes
  * executes commands required after installation
  * updates the system RPM database; uses info in database when checking for conflicts

#### 6.11 Uninstalling Packages
* a successful uninstall produces no output
* errors occur when the package isn't installed or is required by other packages
`$ sudo rpm -e system-config-lvm` where **-e** is for erase and 'system-config-lvm' is the package name, not rpm file name
`$ sudo rpm -e --test -vv system-config-lvm` where **--test** will tell you whether the install would succeed or fail w/o actually doing it and **-vv** gives more information
* do not uninstall the rpm package itself! (would require booting into rescue env. or reinstalling OS)

#### 6.12 Using the 'rpm' Command Demo
`$ rpm -qa | grep bzip2` # **-q** for query, **-a** for all 
> bzip2-libs-1.0.6-13.el7.x86_64 # libraries  
> bzip2-1.0.6-13.el7.x86_64 # actual program  
`$ find / -name "bzip2*" -type d 2>&1 | grep -v 'Permission denied'`  
> /usr/share/doc/bzip2-libs-1.0.6  
> /usr/share/doc/perl-IO-Compress-2.061/io/bzip2  
> /usr/share/doc/bzip2-1.0.6  
`$ rpm -qil bzip2 | less` # **-i** for information, **l** for list  
`$ ls -lF $(rpm -ql bzip2)` # make it a shell command (pipe through less if too much output)  
? Why doesn't `$ rpm -ql bzip2 | ls -lF` work?  
`$ sudo rpm -e --test bzip2`  
> error: Failed dependencies:  
>  bzip2 is needed by (installed) libvirt-daemon-driver-qemu-3.9.0-14.el7_5.5.x86_64  
>  bzip2 is needed by (installed) sos-3.5-7.el7.centos.noarch  
>  /usr/bin/bzip2 is needed by (installed) file-roller-3.22.3-1.el7.x86_64  
`$ rpm -q --whatprovides bzip2`  
> bzip2-1.0.6-13.el7.x86_64  
`$ rpm -q --whatrequires bzip2`  
> libvirt-daemon-driver-qemu-3.9.0-14.el7_5.5.x86_64  
> sos-3.5-7.el7.centos.noarch  

#### 6.13 Upgrading Packages
* upgrading replaces the original package if installed:
`$ sudo rpm -Uvh bash-4.2.45-5.el7_0.4.x86_64.rpm` # **-U** for Upgrade
* old packaged removed after newer version is installed except for config from original, which is saved with an **.rpmsave** extension
* **-U** allows installation without error if the package is not already installed
* **-i** not designed for upgrades
* however, different versions can be installed if each version does not contain the same files (typically kernel packages and library packages from alternative architectures)
* use **--oldpackage** with **rpm -U** to replace current version with an earlier one

#### 6.14 Freshening Packages
`$ sudo rpm -Fvh *.rpm`# **-F** for Freshen
1. if an older version of a package is installed, it will be upgraded to the newer version in the directory
2. if the version on the system is the same as the one in the directory, nothing happens
3. if there is no version of a package installed, the package in the directory is ignored
* useful for applying a lot of patches (i.e., upgraded packages) at once

#### 6.15 Upgrading the Kernel
When you install a new kernel on your system, it requires a reboot (one of the few updates that do) to take effect. You should not do an upgrade (-U) of a kernel: an upgrade would remove the old currently running kernel.  
This in and of itself won't stop the system, but if, after a reboot, you have any problems, you will no longer be able to reboot into the old kernel, since it has been removed from the system. However, if you install (**-i**), both kernels coexist and you can choose to boot into either one; i.e., you can revert back to the old one if need be.  
To install a new kernel do:  
`$ sudo rpm -ivh kernel-{version}.{arch}.rpm` filling in the correct version and architecture names.  
When you do this, the **GRUB** configuration file will automatically be updated to include the new version; it will be the default choice at boot, unless you reconfigure the system to do something else.  
Once the new kernel version has been tested, you may remove the old version if you wish, though this is not necessary. Unless you are short on space, it is recommended that you keep one or more older kernels available.  

#### 6.16 Using rpm2cpio
Suppose you have a need to extract files from an rpm but do not want to actually install the package? The **rpm2cpio** program can be used to copy the files from an **rpm** to a **cpio** archive, and also extract the files if so desired.  
* Create the cpio archive with:
`$ rpm2cpio foobar.rpm > foobar.cpio`
* To list files in an rpm:
`$ rpm2cpio foobar.rpm | cpio -t`
* but a better way is to do:
`$  rpm -qilp foobar.rpm`
* To extract onto the system:
`$ rpm2cpio bash-4.2.45-5.el7_0.4.x86_64.rpm |  cpio -ivd bin/bash`  
`$ rpm2cpio foobar.rpm | cpio --extract --make-directories`

### Labs 6

#### Lab 6.1 Using RPM

1. Find out what package the file /etc/logrotate.conf belongs to.
  * My solution: 
  * `$ rpm -qf /etc/logrotate.conf`
> logrotate-3.8.6-15.el7.x86_64

2. List information about the package including all the files it contains.
  * My solution:
  * `$ rpm -qil logrotate-3.8.6-15.el7.x86_64`
> Name        : logrotate
> V> ersion     : 3.8.6
> Release     : 15.el7
> Architecture: x86_64
> Install Date: Thu 10 May 2018 09:57:07 AM PDT
> Group       : System Environment/Base
> Size        : 106988
> License     : GPL+
> Signature   : RSA/SHA256, Wed 25 Apr 2018 04:25:49 AM PDT, Key ID 24c6a8a7f4a80eb5
> Source RPM  : logrotate-3.8.6-15.el7.src.rpm
> Build Date  : Tue 10 Apr 2018 05:51:42 PM PDT
> Build Host  : x86-01.bsys.centos.org
> Relocations : (not relocatable)
> Packager    : CentOS BuildSystem <http://bugs.centos.org>
> Vendor      : CentOS
> URL         : https://github.com/logrotate/logrotate
> Summary     : Rotates, compresses, removes and mails system log files
> Description :
> The logrotate utility is designed to simplify the administration of
> log files on a system which generates a lot of log files.  Logrotate
> allows for the automatic rotation compression, removal and mailing of
> log files.  Logrotate can be set to handle a log file daily, weekly,
> monthly or when the log file gets to a certain size.  Normally,
> logrotate runs as a daily cron job.
> 
> Install the logrotate package if you need a utility to deal with the
> log files on your system.
> /etc/cron.daily/logrotate
> /etc/logrotate.conf
> /etc/logrotate.d
> /etc/rwtab.d/logrotate
> /usr/sbin/logrotate
> /usr/share/doc/logrotate-3.8.6
> /usr/share/doc/logrotate-3.8.6/CHANGES
> /usr/share/doc/logrotate-3.8.6/COPYING
> /usr/share/man/man5/logrotate.conf.5.gz
> /usr/share/man/man8/logrotate.8.gz
> /var/lib/logrotate
> /var/lib/logrotate/logrotate.status
  * Fancier solution from the book which combines 1 and 2:
  * `$ rpm -qil $(rpm -qf /etc/logrotate.conf)`

3. Verify the package installation.
  * My solution: 
  * $ rpm -V logrotate-3.8.6-15.el7.x86_64
> ..?......  c /etc/cron.daily/logrotate
  * means MD5 checksum test cannot be performed

4. Try to remove the package.
  * My solution: 
  * `$ rpm -e --test logrotate-3.8.6-15.el7.x86_64`
> error: Failed dependencies:
>   logrotate is needed by (installed) vsftpd-3.0.2-22.el7.x86_64
>   logrotate >= 3.5.2 is needed by (installed) rsyslog-8.24.0-16.el7_5.4.x86_64


#### Lab 6.2 Rebuilding the RPM Database
There are conditions (which?) under which the RPM database stored in /var/lib/rpm can be corrupted. In this exercise we will construct a new one and verify its integrity.

1. Backup the contents of /var/lib/rpm as the rebuild process will overwrite the contents. If you neglect to do this and something goes wrong you are in serious trouble.
  * My solution: 
  * `sudo /bin/rm -f /var/lib/rpm/__db*`
  * `sudo tar czvf $(hostname).rpmdatabase.tar.gz /var/lib/rpm`
  * `sudo mv centos.rpmdatabase.tar.gz /var/lib/`
  * Correct solution:
  * `$ sudo cp -a rpm rpm_BACKUP` # then the questions 3 and 5 can use **ls** in a straightforward way

2. Rebuild the data base.
  * My solution:
  * `$ sudo rpm --rebuilddb`

3. Compare the new contents of the directory with the backed up contents; don’t examine the actual file contents as they are binary data, but note the number and names of the files.
  * My solution:
  * `$ tar -xzvf centos.rpmdatabase.tar.gz` # that unpacked the files into the original directory instead of my current directory
  * `$ tar -xzvf centos.rpmdatabase.tar.gz -C ~/rpm`
  * `$ ls -al | wc`
>   18     155    1054
  * `$ ls -al /var/lib/rpm | wc`
>     18     155     986

4. Get a listing of all rpms on the system. You may want to compare this list with one generated before you actually do the rebuild procedure. If the query command worked, your new database files should be fine.
  * My solution:
  * `$ rpm -qa | wc -l`
> 1627
  * Book solution:
  * `$ rpm -qa | tee /tmp/rpm-qa.output`

5. Compare again the two directory contents. Do they have the same files now?
  * I didn't get a count before the rebuild

6. You could delete the backup (probably about 100MB in size) but you may want to keep it around for a while to make sure your system is behaving properly before trashing it.
You may want to look at http://www.rpm.org/wiki/Docs/RpmRecovery for a more complete examination of steps you can take to verify and/or recover the database integrity.
  * That link gives a 404 currently

Chapter 7: DPKG
-----
Skip

Chapter 8: yum
-----
### Introduction 8
The **yum** program provides a higher level of intelligent services for using the underlying **rpm** program. It can automatically resolve dependencies when installing, updating and removing packages. It accesses external software **repositories**, synchronizing with them and retrieving and installing software as needed.

### Goals 8
By the end of this chapter, you should be able to: 
* Discuss package installers and their characteristics.
* Explain what **yum** is.
* Configure **yum** to use repositories.
* Discuss the queries **yum** can be used for.
* Verify, install, remove, and upgrade packages using **yum**.

### Notes 8

#### 8.3 Package Installers
The higher-level package management systems:
* Can use both local and remote **repositories** as a source to install and update binary as well as source software packages.
* Are used to automate the install, upgrade, and removal of software packages.
* Resolve dependencies automatically.
* Save time because there is no need to either download packages manually or search out dependency information separately.

#### 8.4 What Is yum?
* yum is used by the majority of distributions that use **rpm**, including **RHEL**, **CentOS**, **Scientific Linux**, and **Fedora**
* yum caches info and databases to speed performance; to remove cached information:
`$ yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]`
* yum has a number of modular extensions (plugins) and companion programs that can be found under `/usr/bin/yum*` and `/usr/sbin/yum*`

#### 8.5 Configuring yum to Use Repositories
Repository configuration files are kept in `/etc/yum.repos.d/` and have a **.repo** extension.

#### 8.6 Repository Files
* A very simple repository file might look like:  
**[repo-name]**  
**name=Description of the repository**  
**baseurl=http://somesystem.com/path/to/repo**  
**enabled=1**  
**gpgcheck=1**  
* Here's an example:  
`$ cat CentOS-Base.repo`
> [base]
> name=CentOS-$releasever - Base
> mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
> #baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
> gpgcheck=1
> gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

One can toggle the use of a particular repository on or off by changing the value of enabled to 0 or 1, or using the **--disablerepo=somerepo** and **--enablerepo=somerepo** options when using **yum**. One can (but should not) also turn off integrity checking with the **gpgcheck** variable.

#### 8.7 Queries
* Search for packages with keyword in name:  
`$ sudo yum search keyword` # tells about packages
> Loaded plugins: fastestmirror, langpacks  
> Loading mirror speeds from cached hostfile  
>  * base: repos.lax.quadranet.com  
>  * epel: mirrors.develooper.com  
>  * extras: mirror.sjc02.svwh.net  
>  * updates: centos.sonn.com  
> ============================================= N/S matched: keyword ==============================================  
> perl-B-Keywords.noarch : Lists of reserved barewords and symbol names  
> perl-Devel-Declare.x86_64 : Adding keywords to perl, in perl  
> rednotebook.noarch : Daily journal with calendar, templates and keyword searching  
>   
>   Name and summary matches only, use "search all" for everything.  
>   
`$ sudo yum list "*keyword*"` # tells what is installed and what else is available  
> Loaded plugins: fastestmirror, langpacks  
> Loading mirror speeds from cached hostfile  
>  * base: repos.lax.quadranet.com  
>  * epel: mirrors.develooper.com  
>  * extras: mirror.sjc02.svwh.net  
>  * updates: centos.sonn.com  
> Available Packages  
> perl-B-Keywords.noarch  
* Display information about a package:  
`$ sudo yum info keyword`  
> Loaded plugins: fastestmirror, langpacks  
> Loading mirror speeds from cached hostfile  
>  * base: repos.lax.quadranet.com  
>  * epel: mirrors.develooper.com  
>  * extras: mirror.sjc02.svwh.net  
>  * updates: centos.sonn.com  
> Error: No matching Packages to list  
Information includes size, version, what repository it came from, a source URL, and a longer description. Wildcards can be given, as in **yum info "libc\*"**, for this and most **yum** commands. Note that the package need not be installed, unlike queries made with **rpm -q**.  
* List all packages, or just those installed, available, or updates that have not yet been installed:  
`$ sudo yum list [installed | updates | available ]`  
* Show information about package groups installed or available, etc.:  
`$ sudo yum grouplist`  
`$ sudo yum groupinfo group1` like `$ sudo yum groupinfo "Development Tools"`  
* Show packages that contain a certain file name:  
`$ sudo yum provides` as in `$ sudo yum provides "/logrotate.conf"`  

#### 8.8 Verifying Pacakges
* Package verification requires installation of the **yum-plugin-verify** package (a yum plugin, not an executable - plugins extend the possible set of commands and arguments yum can take); to install:
`$ sudo yum install yum-plugin-verify`  
* To verify a package, giving the most information:
`$ sudo yum verify [package]`  
* To mimic **rpm -V** exactly:
`$ sudo yum verify-rpm [package]`  
* To list all differences, including configuration files:
`$ sudo yum verify-all [package]`  
* Without arguments, the above commands will verify all packages installed on the system.
* By default, the verification commands ignore configuration files which may change through normal and safe usage. There are some other options: see man yum-verify.

#### 8.9 Installing/Removing/Upgrading Packages
* Install one or more packages from repositories, resolving and installing any necessary dependencies:  
`$ sudo yum install package1 [package2]`  
* Install from a local rpm:  
`$ sudo yum localinstall package-file`; this will attempt to resolve dependencies by accessing remote repositories unlike `$ rpm -i package-file`  
* Install a specific software group from a repository, resolving and installing any necessary dependencies for each package in the group:  
`$ sudo yum groupinstall group-name` or `$ sudo yum install @group-name`  
* Remove packages from the system:  
`$ sudo yum remove package1 [package2]`  
  * One must be careful with package removal, as yum will not only remove requested packages, but all packages that depend on them! This may not be what you want, so never run yum remove with the -y option, which assumes automatic confirmation of removal.  
* Update a package from a repository:  
`$ sudo yum update [package]`  
  * If no package name is given, all packages are updated.  
* During installation (or update), if a package has a configuration file which is updated, it will rename the old configuration file with an **.rpmsave** extension. If the old configuration file will still work with the new software, it will name the new configuration file with an **.rpmnew** extension. You can search for these filename extensions (almost always in the /etc subdirectory tree) to see if you need to do any reconciliation, by doing:  
`$ sudo find /etc -name "*.rpm*"`  

#### 8.10 Additional Commands
* List plugins:  
`$ sudo yum list "yum-plugin*"`  
* Show a list of all enabled repositories:  
`$ sudo yum repolist`  
* Initiate an interactive shell in which to run multiple YUM commands:  
`$ sudo yum shell [text-file]`  
  * If **text-file** is given, yum will read and execute commands from that file instead of from the terminal.
* Download packages, but do not install them; just store them under the /var/cache/yum directory, or another directory you can specify:  
`$ sudo yum install --downloadonly package`  
  * or you can type "d" instead of "y" or "n" when prompted after issuing an install command. The package(s) will be downloaded under `/var/cache/yum` in a location depending on the repository from which the download proceeds, unless the **--downloaddir=** option is used. Any other necessary packages will also be downloaded to satisfy dependencies.  
* You can view the history of yum commands, and, with the correct options, even undo or redo previous commands:  
`$ sudo yum history`  

#### 8.11 dnf
* **dnf** is intended to be a next generation replacement for **yum**
* not widely adopted
* [Part II. Package Management](https://docs-old.fedoraproject.org/en-US/Fedora/24/html/System_Administrators_Guide/part-Package_Management.html)

#### 8.12 Using the yum and dnf Utilities Demo
* `$ yumex` for "Yum Extender" (yumex-dnf on Fedora), launches graphical yum interface
* EPEL: Extra Packages for Enterprise Linux

### Labs 8

#### Lab 8.1 Basic YUM Commands
1. Check to see if there are any available updates for your system.
* My solution:  
`$ sudo yum list updates`  
* Lab solution (in addition to above):  
`$ sudo yum check-update`  

2. Update a particular package.
* My solution:  
`$ sudo yum update docker.x86_64`  
`$ sudo yum update`  
  
3. List all installed kernel-related packages, and list all installed or available ones.
* My solution:  
* Lab solution:  
`$ sudo yum list installed "kernel*"`  
`$ sudo yum list "kernel*"`  

4. Install the httpd-devel package, or anything else you might not have installed yet. Doing a simple:
`$ sudo yum list`  
will let you see a complete list; you may want to give a wildcard argument to narrow the list.  
* My solution:  
`$ sudo yum install httpd-devel`  
  
#### Lab 8.2 Using yum to Find Information about a Package
 Using yum (and not rpm directly), find:  
1. All packages that contain a reference to bash in their name or description.  
* My solution:  
`$ sudo yum search bash`  

2. Installed and available bash packages.  
* My solution:  
`$ sudo yum list installed "bash*"`  
`$ sudo yum list installed "*bash*"`  
`$ sudo yum list available "bash*"`  
`$ sudo yum list available "*bash*"`  
  
3. The package information for bash.  
* My solution:  
`$ sudo yum info "bash*"`  

4. The dependencies for the bash package.  
* My solution:  
* Correct solution:  
`$ sudo yum deplist bash`  
  
Try the commands you used above both as root and as a regular user. Do you notice any difference?  
* Not really
  
#### Lab 8.3 Managing Groups of Packages with **yum**
1. Use the following command to list all package groups available on your system:  
`$ yum grouplist`  

2. Identify the Backup Client group and generate the information about this group using the command  
`$ yum groupinfo "Backup Client"`  

3. Install using:  
`$ sudo yum groupinstall "Backup Client"`  

4. Identify a package group that’s currently installed on your system and that you don’t need. Remove it using yum groupremove as in:  
`$ sudo yum groupremove "Backup Client"`  
Note you will be prompted to confirm removal so you can safely type the command to see how it works.
You may find that the groupremove does not remove everything that was installed; whether this is a bug or a feature can be discussed.  
  
#### Lab 8.4 Adding a New **yum** Repository
According to its authors (at http://www.webmin.com/index.htm):  
> “Webmin is a web-based interface for system administration for Unix. Using any modern web browser, you can setup user accounts, Apache, DNS, file sharing and much more. Webmin removes the need to manually edit Unix configuration files like /etc/passwd, and lets you manage a system from the console or remotely.”  
We are going to create a repository for installation and upgrade. While we could simply go the download page and get the current rpm, that would not automatically give us any upgrades.  
1. Create a new repository file called webmin.repo in the /etc/yum.repos.d directory. It should contain the following:  
  [Webmin]  
  name=Webmin Distribution Neutral  
  baseurl=http://download.webmin.com/download/yum  
  mirrorlist=http://download.webmin.com/download/yum/mirrorlist  
  enabled=1  
  gpgcheck=0  
(Note you can also cut and paste the contents from http://www.webmin.com/download.html.)  
2. Install the webmin package.  
`$ sudo yum install webmin`  
  
Chapter 11: System Monitoring
-----
### Introduction 11

### Goals 11

### Notes 11

### Labs 11


Linux filesystem and paths
=====

Main Directory Layout (2.6)
-----
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

Linux paths
-----
`/etc/security/limits.conf` (3.18)  
#### 8.4 What Is yum?
`/usr/bin/yum*` and `/usr/sbin/yum*`  
#### 8.5 Configuring yum to Use Repositories
`/etc/yum.repos.d/` (8.5)  
  
  
Linux commands
-----
`$ sudo du -shxc --exclude=proc *`  
`$ du -shc share/*`  
`$ file *cgi-bin/*` # in `/usr/lib/cups` (should be in lib64)  
`$ ls -lF` # difference from `lf -al` ? '-F' appends a symbol at the end of the name corresponding to the type  
`$ ulimit -a` displays or resets resource limits associated with processes running under a shell  
`$ ulimit [options] [limit]` as in `$ ulimit -n 1600` (changes maximum number of file descriptors)  
? What's a file descriptor? (same as file streams? stdin, stdout, stderr)  
`&` at end of command for background processing (see 3.14)  
`$ ps -elf` to see kernel created processes  
`$ nice -n 5 command [ARGS]` (3.17)  
`$ nice -5 command [ARGS]` (3.17)  
`$ renice +3 13848` (3.18)  
`$ ldd /usr/bin/vi`  
`$ ulimit -H -n` (Lab 3.1)  
`$ ulimit -S -n` (Lab 3.1)  
`$ ulimit -n 4096` (Lab 3.1)  
`$ ulimit -H -n 2048` (Lab 3.1)  
`$ ipcs` (Lab 3.2)  
`$ ipcs -p` (Lab 3.2)  
`$ ps aux | grep -e 11737 -e 11044`  (Lab 3.2) the `-e` option is to match a pattern; also `--regexp`  
`$ kill -l` (4.4)  
`$ man 7 signal` (4.4)  
`$ kill 1991` (4.5)  
`$ kill -9 1991` (4.5)  
`$ kill -SIGKILL 1991` (4.5)  
`$ killall bash` (4.6)  
`$ killall -9 bash` (4.6)  
`$ killall -SIGKILL bash` (4.6)  
`$ pkill [-signal] [options] [pattern]` (4.6)  
`$ pkill -u libby foobar` (4.6)  
`$ pkill -HUP rsyslogd` (4.6)  
`$ gcc -o signals signals.c` (Lab 4)  
`$ gnome-software` a Linux app store (5.14)  
`$ rpm -q bash` (6.8)  
`$ rpm -qf /bin/bash` (6.8)  
`$ rpm -ql bash` (6.8)  
`$ rpm -qi bash` (6.8)  
`$ rpm -qip foo-1.0.0-1.noarch.rpm` (6.8)  
`$ rpm -qa` (6.8)  
`$ rpm -Va` (6.9)  
`$ rpm -V bash` (6.9)  
`$ rpm -V talk` (6.9)  
`$ sudo rpm -ivh foo-1.0.0-1.noarch.rpm`  where the **-i** is for install, **-v** is for verbose, and **-h** just means print out hash marks while doing to show progress. (6.10)  
`$ sudo rpm -e system-config-lvm` where **-e** is for erase and 'system-config-lvm' is the package name, not rpm file name (6.11)  
`$ sudo rpm -e --test -vv system-config-lvm` where **--test** will tell you whether the install would succeed or fail w/o actually doing it and **-vv** gives more information (6.11)  
`$ rpm -qa | grep bzip2` # **-q** for query, **-a** for all (6.12)  
`$ find / -name "bzip2*" -type d 2>&1 | grep -v 'Permission denied'` (6.12)  
`$ rpm -qil bzip2 | less` # **-i** for information, **l** for list (6.12)  
`$ ls -lF $(rpm -ql bzip2)` # make it a shell command (pipe through less if too much output) (6.12)  
? Why doesn't `$ rpm -ql bzip2 | ls -lF` work? (6.12)  
`$ sudo rpm -e --test bzip2` (6.12)  
`$ rpm -q --whatprovides bzip2` (6.12)  
`$ rpm -q --whatrequires bzip2` (6.12)  
`$ sudo rpm -Uvh bash-4.2.45-5.el7_0.4.x86_64.rpm` # **-U** for Upgrade (6.13)  
`$ sudo rpm -Fvh *.rpm`# **-F** for Freshen (6.14)  
`$ sudo rpm -ivh kernel-{version}.{arch}.rpm` (6.15)  
`$ rpm2cpio foobar.rpm > foobar.cpio` (6.16)  
`$ rpm2cpio foobar.rpm | cpio -t` (6.16)  
`$  rpm -qilp foobar.rpm` (6.16)  
`$ rpm2cpio bash-4.2.45-5.el7_0.4.x86_64.rpm |  cpio -ivd bin/bash` with
`$ rpm2cpio foobar.rpm | cpio --extract --make-directories` (6.16)  
#### 8.4 What Is yum?
`$ yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]`  
#### 8.6 Repository Files
`$ cat CentOS-Base.repo`  
#### 8.7 Queries
`$ sudo yum search keyword`  
`$ sudo yum list "*keyword*"`  
`$ sudo yum info keyword` or `$ yum info "libc*"`  
`$ sudo yum list [installed | updates | available ]`  
`$ sudo yum grouplist`  
`$ sudo yum groupinfo group1`  
`$ sudo yum groupinfo "Development Tools"` 
`$ sudo yum provides`  
`$ sudo yum provides "/logrotate.conf"`  
#### 8.8 Verifying Pacakges
`$ sudo yum install yum-plugin-verify`  
`$ sudo yum verify [package]`  
`$ sudo yum verify-rpm [package]`  
`$ sudo yum verify-all [package]`  
#### 8.9 Installing/Removing/Upgrading Packages
`$ sudo yum install package1 [package2]`  
`$ sudo yum localinstall package-file`  
`$ sudo yum groupinstall group-name` or `$ sudo yum install @group-name`  
`$ sudo yum remove package1 [package2]`  
`$ sudo yum update [package]`  
`$ sudo find /etc -name "*.rpm*"`  
#### 8.10 Additional Commands
`$ sudo yum list "yum-plugin*"`  
`$ sudo yum repolist`  
`$ sudo yum shell [text-file]`  
`$ sudo yum install --downloadonly package`  
`$ sudo yum history`  
#### 8.12 Using the yum and dnf Utilities Demo
`$ yumex`  
#### Lab 8.2 Using yum to Find Information about a Package
`$ sudo yum deplist bash`  
#### Lab 8.3 Managing Groups of Packages with **yum**
`$ yum grouplist`  
`$ yum groupinfo "Backup Client"`  
`$ sudo yum groupinstall "Backup Client"`  
`$ sudo yum groupremove "Backup Client"`  
#### Lab 8.4 Adding a New **yum** Repository
`$ sudo yum install webmin`  



Available Signals for the x86 Platform
-----
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

