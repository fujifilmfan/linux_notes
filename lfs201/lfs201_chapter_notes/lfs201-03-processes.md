Chapter 3: Processes
--------------------

[3.3: Learning Objectives](#33-learning-objectives)  
[3.4: Processes, Programs and Threads](#34-processes-programs-and-threads)  
[3.5: The init Process](#35-the-init-process)  
[3.6: Processes](#36-processes)  
[3.7: Process Attributes](#37-process-attributes)  
[3.8: Controlling Processes with ulimit](#38-controlling-processes-with-ulimit)  
[3.9: Process Permissions and setuid](#39-process-permissions-and-setuid)  
[3.10: More on Process States](#310-more-on-process-states)  
[3.11: Execution Modes](#311-execution-modes)  
[3.12: User Mode](#312-user-mode)  
[3.13: Kernel Mode](#313-kernel-mode)  
[3.14: Daemons](#314-daemons)  
[3.15: Creating Processes in a Command Shell](#315-creating-processes-in-a-command-shell)  
[3.16: Kernel-Created Processes](#316-kernel-created-processes)  
[3.17: Process Creating and Forking](#317-process-creating-and-forking)  
[3.18: Using nice to Set Priorities](#318-using-nice-to-set-priorities)  
[3.19: Modifiying the Nice Value](#319-modifiying-the-nice-value)  
[3.20: Using renice to Set Priorities Demo](#320-using-renice-to-set-priorities-demo)  
[3.21: Static and Shared Libraries](#321-static-and-shared-libraries)  
[3.22: Shared Library Versions](#322-shared-library-versions)  
[3.23: Finding Shared Libraries](#323-finding-shared-libraries)  
[Lab 3.1: Controlling Processes with ulimit](#lab-31-controlling-processes-with-ulimit)  
[Lab 3.2: Examining System V IPC Activity](#lab-32-examining-system-v-ipc-activity)  
[Paths and Commands](#paths-and-commands)  
  
### 3.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Describe a process and the resources associated with it.
* Distinguish between processes, programs and threads.
* Understand process attributes, permissions and states. 
* Know the difference between running in user and kernel modes. 
* Describe daemon processes.
* Understand how new processes are forked (created).
* Use nice and renice to set and modify priorities.

### 3.4: Processes, Programs and Threads
----
? What's the difference? https://www.youtube.com/watch?v=exbKr6fnoUw  
A process is an executing program and associated resources, including environment, open files, signal handlers, etc. The same program may be executing more than once simultaneously, and thus, be responsible for multiple processes.  

At the same time, two or more tasks, or threads of execution, can share various resources, such as their entire memory spaces (or just particular memory areas), open files, etc. When there is an everything shared circumstance, one speaks of a multi-threaded process.  

In other operating systems, there may be a big distinction between full heavy weight processes and light weight ones; strictly speaking, the heavy weight process may include a number of light weight processes, or just one of them.  

In Linux, the situation is quite different. Each thread of execution is considered individually, the difference between heavy and light having to do only with sharing of resources and somewhat faster context switching between threads of execution.  

Unlike some other operating systems, Linux has always done an exceptionally fast job of creating, destroying, and switching between processes. Thus, the model adopted for multi-threaded applications resembles multiple processes; each thread is scheduled individually and normally, as if it were a stand-alone process. This is done instead of involving more levels of complication, such as having a separate method of scheduling among the threads of a process, as well as having a scheduling method between different processes.  

At the same time, Linux respects POSIX and other standards for multi-threaded processes; e.g., each thread returns the same process ID (called the thread group ID internally), while returning a distinct thread ID (called the process ID internally). This can lead to confusion for developers, but should be invisible to administrators.  

### 3.5: The init Process
----
* first user process on system with ID = 1
* started as soon as the kernal is initialized and has mounted the root filesystem
* will run until system is shut down; last user process to be terminated
* ancestral parent of all other user process, both directly and indirectly

### 3.6: Processes
----
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

### 3.7: Process Attributes
----
* all processes have certain attributes:
    * program being executed
    * context (state), such as state of CPU registers, where it is executing in the program, what is in the process' memory, and other information
    * permissions
    * associated resources
* **context switching** requires the kernel to store the entire context when swapping out the process and being able to restore it upon execution resumption (such as when it's sleeping while waiting for a condition to be met, like the user to make a request or data to arrive)

### 3.8: Controlling Processes with ulimit
----
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

### 3.9: Process Permissions and setuid
----
* every process has permissions based on which user invoked it; may also have permissions based on who owns its program file
* **setuid** programs:
    * marked with an **s** execute bit
    * have a different **effective user ID** than the **real user ID**
    * run with the user ID of the user who **owns** the program
    * known security problem with **setuid** programs owned by root
    * example: **passwd**; any user can run it; root permission required to update the write-restricted `/etc/passwd` and `/etc/shadow` files
* non-**setuid** programs run with the permissions of the user who **runs** them

### 3.10: More on Process States
----
Possible states:
* Running; either executing on a CPU or CPU core or sitting in the **run queue**, waiting for a time slice
* Sleeping (i.e., Waiting); waiting on a request (usually I/O); when request completed, kernel will put process back on run queue
* Stopped; suspended, such as when programmer wants to examine the executing program's memory, CPU registers, flags, or other attributes; generally done when process is being run under a debugger or the user hits **Ctrl-Z**
* Zombie; when process terminates and no other process (usually the parent) has inquired about its exit state, i.e. reaped it; called a **defunct** process; a zombie process has released all its resources except its exit state and its entry in the process table; if parent dies, the process is **adopted** by **init** (PID = 1) or **kthreadd** (PID = 2)

### 3.11: Execution Modes
----
A process (or any particular thread of a multi-threaded process) may be executing in either:
* **user mode** (Intel: **Ring 3**)
* **system mode** (or **kernel mode**) (Intel: **Ring 0**)
The mode is not a state of the system but the processor, and what instructions can be executed depends on the mode and is enforced at the hardware, not the software level

### 3.12: User Mode
----
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

### 3.13: Kernel Mode
----
* kernel mode:
    * CPU has full access to all hardware on the system, including peripherals, memory, disks, etc.
    * applications that need access to these resources must issue a **system** call, which causes a **context switch** from user mode to kernel mode; must be followed when reading and writing from files, creating a new process, etc.
    * times when the system is in kernel mode that have nothing to do with processes: handling hardware interrupts, running scheduling routines and other management tasks for the system, etc.
* application code never runs in kernel mode; it's only the system call itself which is kernel code

### 3.14: Daemons
----
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

### 3.15: Creating Processes in a Command Shell
----
when a user executes a command in a command shell interpreter, like **bash**:  
* a new process is created (forked from the user's login shell)
* a wait system call puts the parent shell process to sleep
* the command is loaded onto the child process's space via the **exec** system call, i.e., the code for the command replaces the **bash** program in the child processes's memory space
* the command completes executing, and the child process dies via the exit system call
* the parent shell is re-awakened by the death of the child process and proceeds to issue a new shell prompt; parent shell then waits for the next command request from the user, at which time the cycle will be repeated
some exceptions:
* if a command is issued for **background** processing (via the ampersand `&` at the end of the command line), the parent shell skips the wait request and is free to issue a new shell prompt immediately, allowing backround process to execute in parallel; otherwise, for **foreground** requests, the shell waits until the child process has completed or is stopped via a signal
* some shell commands (such as echo and kill) are built into the shell itself, and do not involve loading of program files; for these commands, no **fork** or **exec** are issued for the execution

### 3.16: Kernel-Created Processes
----
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
* run `$ ps -elf` to list all processes on the system while showing the parent process IDs
    * they will have **PPID = 2**, corresponding to **kthreadd**
    * names encapsulated in square brackets, such as **[ksoftirqd/0]**

### 3.17: Process Creating and Forking
----
* **forking** == creating new (child) processes
* often followed by an **exec**, in which the parent process terminates and the child process inherits the PID of the parent
* older UNIX systems used **spawn** (not part of the **POSIX** standard or a normal part of Linux)
* an application (like a web server handling many clients) can start many new processes or start new threads as part of the same process; there isn't much difference with respect to time and resources
* example:
    * the **init** process executes the **sshd** init script, which then launches the **sshd daemon** (the daemon listens for **ssh** requests from remote users)
    * when a request is received, **sshd** creates a copy of itself to service the requests, so each remote user gets their own copy of the **sshd** daemon running to service their remote login
    * the **sshd** process will start the login program to validate the user; if successful, the login process will fork off a shell (like **bash**) to interpret user commands, and so on

### 3.18: Using nice to Set Priorities
----
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

### 3.19: Modifiying the Nice Value
----
* by default, only a superuser can decrease niceness
* normal users can get that ability via `/etc/security/limits.conf`
* to change niceness of an already running process, use renice:
    *  `$ renice +3 13848`, where 13848 is the pid of the process; 3 would be the new niceness, not the change
    * more than one process can be changed at a time; see the man page

### 3.20: Using renice to Set Priorities Demo
----
* run `ps lf` to see processes and priorities
* `renice +5 3077`
* child processes have same, new priority
* can increase niceness as normal user but not decrease: "Permission denied"
* can run 'gnome-system-monitor' and change priorities there, too

### 3.21: Static and Shared Libraries
----
* two types of **libraries** of code:
    * **static**: code for library functions inserted in program at **compile time** and then does not change even if the library is updated
    * **shared**: code for library functions loaded in program at **run time**, and if library is changed later, running program runs with new library modifications
        * shared libraries more efficient b/c they can be used by many applications at once; memory usage, executable sizes, and application load time are reduced
        * also called **DLL**s (**D**ynamic **L**ink **L**ibrary)

### 3.22: Shared Library Versions
----
* shared libraries need to be carefully versioned to avoid **DLL Hell** (programs breaking when the library changes)
* some programs embed or bundle the library, but then can't benefit from bug fixes, security patches, etc. as quickly
* shared libraries have the extension **.so**
* full name like **libc.so.N** where **N** is a major version number
* example:  
    `$ ls -lF libgdbm.so.*`  
    > lrwxrwxrwx. 1 root root    16 May 10 11:56 libgdbm.so.4 -> libgdbm.so.4.0.0*  
    > -rwxr-xr-x. 1 root root 36720 Jun  9  2014 libgdbm.so.4.0.0*  
    `$ `  

### 3.23: Finding Shared Libraries
----
* **ldd** can be used to find what shared libraries an executable requires
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

### Lab 3.1: Controlling Processes with ulimit
----
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

### Lab 3.2: Examining System V IPC Activity
----
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

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
processes | `/proc/sys/kernel/pid_max` | stores max pid, by default a 16-bit number, 32768 | LFS201 3.6
processes | `/etc/security/limits.conf` | set resource limits for all logged-in users, not just current shell | LFS201 3.8 
processes | `/etc/passwd` | changed by running `passwd` but not directly editable by normal user | LFS201 3.9
processes | `/etc/shadow` | changed by running `passwd` but not directly editable by normal user | LFS201 3.9
processes | `/etc/init.d` | scripts here start various daemons | LFS201 3.14
processes | `/etc/init.d/functions` | contains functions used by scripts in `/etc/init.d` | LFS201 3.14
processes | `/etc/security/limits.conf` | allow normal users to set niceness | LFS201 3.19
processes | `/etc/ld.so.conf` | used by **ldconfig**; lists the directories searched for shared libraries | LFS201 3.23

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
processes | `$ ulimit [options] [limit]` | generic syntax | LFS201 3.8
processes | `$ ulimit -a` | displays or resets resource limits associated with processes running under a shell | LFS201 3.8
processes | `$ ulimit -n 1600` | changes maximum number of file descriptors | LFS201 3.8
processes | `$ passwd ...` | **setuid** program; changes password | LFS201 3.9
processes | `&` | at end of command for background processing: LFS201 3.14
processes | `$ ps -elf` | show kernel-created processes; list all processes on the system while showing the parent process IDs | LFS201 3.16
processes | `$ nice -n 5 command [ARGS]` | increase niceness by 5 | LFS201 3.18
processes | `$ nice -5 command [ARGS]` | increase niceness by 5 | LFS201 3.18
processes | `$ renice +3 13848` | increase niceness by 3 of the process with pid = 13848, where 13848 is the pid of the process; 3 would be the new niceness, not the change | LFS201 3.19
processes | `$ ps lf` | shows processes and priorities | LFS201 3.20
processes | `$ ldd /usr/bin/vi` | shows shared libraries that executable requires | LFS201 3.23
processes | `$ ulimit -H -n` | view hard limit | LFS201 Lab 3.1
processes | `$ ulimit -S -n` | view soft limt | LFS201 Lab 3.1
processes | `$ ulimit -n 4096` | set soft limit to hard limit value(??) | LFS201 Lab 3.1
processes | `$ ulimit -H -n 2048` | set hard limit to 2048(??) | LFS201 Lab 3.1
processes | `$ ipcs` |  | LFS201 Lab 3.2
processes | `$ ipcs -p` |  | LFS201 Lab 3.2
processes | `$ ps aux | grep -e 11737 -e 11044` | `-e` option is to match a pattern; also `--regexp` | LFS201 Lab 3.2
