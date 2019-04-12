Chapter 12: Process Monitoring
------------------------------

[12.3: Learning Objectives](#123-learning-objectives)  
[12.4: Monitoring Tools](#124-monitoring-tools)  
[12.5: Viewing Process States with ps](#125-viewing-process-states-with-ps)  
[12.6: BSD Option Format for ps](#126-bsd-option-format-for-ps)  
[12.7: ps Output Fields](#127-ps-output-fields)  
[12.8: UNIX Option Format for ps](#128-unix-option-format-for-ps)  
[12.9: Customizing the ps Output](#129-customizing-the-ps-output)  
[12.10: Using pstree](#1210-using-pstree)  
[12.11: Viewing System Loads with top](#1211-viewing-system-loads-with-top)
[12.12: top Options](#1212-top-options)
[12.13: sar](#1213-sar)
[Lab 12.1: Processes](#lab-121-processes)
[Lab 12.2: Monitoring Process States](#lab-122-monitoring-process-states)
[Paths and Commands](#paths-and-commands)  

### 12.3: Learning Objectives
----
By the end of this chapter, you should be able to:
  * Use **ps** to view characteristics and statistics associated with processes.
  * Identify different **ps** output fields and customize the **ps** output.
  * Use **pstree** to get a visual description of the process ancestry and multi-threaded applications.
  * Use **top** to view system loads interactively.

### 12.4: Monitoring Tools
----
  
**Process and Load Monitoring Utilities**
  
Utitity  | Purpose                                                 | Package
-------  | -------                                                 | -------
top      | Process activity, dynamically updated                   | procps
uptime   | How long the system is running and the average load     | procps
ps       | Detailed information about processes                    | procps
pstree   | A tree of processes and their connections               | psmisc (or pstree)
mpstat   | Multiple processor usage                                | sysstat
iostat   | CPU utilization and I/O statistics                      | sysstat
sar      | Display and collect information about system activity   | sysstat
numastat | nformation about NUMA (Non-Uniform Memory Architecture) | numactl
strace   | Information about all system calls a process makes      | strace
  
### 12.5: Viewing Process States with ps
----
* **ps** data comes from `/proc`
* options:
   * UNIX options, which must be preceded by -, and which may be grouped
   * BSD options, which must not be preceded by -, and which may be grouped
   * GNU long options, each of which must be preceded by --
   * this can be confusing, so most sys admins tent to use one or two standard combinations for daily use
   
### 12.6: BSD Option Format for ps
----
* `$ ps aux` shows all processes
* commands in square brackets (like \[ksoftirqd/0\]) exist entirely within kernel; if there is one for each CPU, the command is followed by the integer specifying the CPU it is running on

### 12.7: ps Output Fields
----
* **VSZ**: process's virtual memory size in KB
* **RSS**: resident set size; the non-swapped physical memory a task is using in KB
* **STAT**: describes the state of the process; **S** for sleeping, **R** for running; symbols after S and R:
   * < for high priority (not nice)
   * N for low priority (nice)
   * L for having pages locked in memory
   * s for session leader
   * l for multi-threaded
   * + for being in the foreground process group.
* `$ ps auxf` shows all processes and how they connect by ancestry

### 12.8: UNIX Option Format for ps
----
* `$ ps -elf`
* output shows PPID and NI (niceness)
* processes with PPID=2 are adopted by kernel process **kthreadd** because their parent process died; older systems would show PPID=1 for **sbin/init**, which is really the same thing

** Common Options**

Option   | Effect
------   | ------
-A or -e | Select all processes
-N       | Negate selection (means do the opposite)
-C       | Select by command name
-G       | Select by real group ID (also supports names)
-U       | Select by real user ID (also supports names)

### 12.9: Customizing the ps Output
----
* specify desired field in output using the **-o** option
* example: `$ ps -o pid,uid,cputime,pmem,command`

**Some ps Fields**

Field   | Meaning
-----   | -------
pid     | process ID
uid     | user ID
cmd     | command with all arguments
cputime | cumulative CPU time
pmem    | ratio of the process's resident set size to the physical memory on the machine, expressed as a percentage

### 12.10: Using pstree
----
* `$ pstree -aAp 2408` shows the process tree for PID 2408
   * -a shows command line arguments
   * -A uses ASCII characters
   * -p shows PIDs
* example:  
[student@centos ~]$ pstree -ap 10525  
ibus-daemon,10525 --xim --panel disable  
  ├─ibus-dconf,10530  
  │   ├─{ibus-dconf},10531  
  │   ├─{ibus-dconf},10535  
  │   └─{ibus-dconf},10537  
  ├─{ibus-daemon},10527  
  └─{ibus-daemon},10536  
* another way to see that child process 10530 has children of its own is `$ ls -l /proc/10530/task`: 

### 12.11: Viewing System Loads with top
----
* by default, top refreshes itself every 3.0 seconds

### 12.12: top Options
----
* top is interactive
   * press **1** to see each CPU shown separately
   * press **i** to see only active processes
   * press **h** or **?** to see a list of interactive commands
   * press **q** to exit
   * press **k** to kill a task
   * press **r** to renice a task
* variations: **htop**, **ntop**, and **atop**
* grapical monitors: **gnome-system-monitor**, **ksysguard**

### Lab 12.1: Processes
----

### Lab 12.2: Monitoring Process States
----
  

### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
monitoring, system | `/var/log` | log files | LFS201 11.5
monitoring, system | `/etc/logrotate.conf` | controls **logrotate** | LFS201 11.6
monitoring, system | `/proc/sys` | shows tunable system parameters | LFS201 11.10
monitoring, system | `/proc/sys/abi/` | Contains files with application binary information | LFS201 11.10
monitoring, system | `/proc/sys/debug/` | Debugging parameters; for now, just some control of exception reporting | LFS201 11.10
monitoring, system | `/proc/sys/dev/` | Device parameters, including subdirectories for cdrom, scsi, raid, and parport | LFS201 11.10
monitoring, system | `/proc/sys/fs/` | Filesystem parameters, including quota, file handles used, and maximums, inode and directory information, etc. | LFS201 11.10
monitoring, system | `/proc/sys/kernel/` | Kernel parameters | LFS201 11.10
monitoring, system | `/proc/sys/net/` | Network parameters; there are subdirectories for ipv4, netfilter, etc. | LFS201 11.10
monitoring, system | `/proc/sys/vm` | Virtual memory parameters | LFS201 11.10
monitoring, system | `/sys/class/net` | contains network device information | LFS201 11.12
monitoring, system | `/var/log/sa` | contains data stored by system activity data collector | LFS201 11.13
monitoring, system | `/etc/cron.d/sysstat` | cron job for periodic data collection for sadc | LFS201 11.13
monitoring, system | `/proc/<PID>/cmdline` | command line used to start the process | LFS201 11.15
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
monitoring, processes | `$ ps aux` | shows all processes | LFS201 12.6
monitoring, processes | `$ ps auxf` | adding **f** flag shows how processes relate by ancestry | LFS201 12.7
monitoring, processes | `$ ps -elf` | show processes in UNIX option format | LFS201 12.8
monitoring, processes | `$ ps -o pid,uid,cputime,pmem,command` | specific the fields in the output | LFS201 12.9
monitoring, processes | `$ pstree` | show processes in tree form | LFS201 12.10
monitoring, processes | `$ pstree -aAp 2408` | shows the process tree for PID 2408 | LFS201 12.10
monitoring, processes | `$ ls -l /proc/10530/task` | see child processes of 10530 | LFS201 12.10