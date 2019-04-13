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

### Paths
----

### Commands
----

### Main Directory Layout
----

**Main Directory Layout (2.6)**
  
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
