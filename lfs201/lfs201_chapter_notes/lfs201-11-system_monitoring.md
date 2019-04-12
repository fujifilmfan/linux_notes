Chapter 11: System Monitoring
-----------------------------

[11.3: Learning Objectives](#113-learning-objectives)  
[11.4: Available Monitoring Tools](#114-available-monitoring-tools)  
[11.5 System Log Files](#115-system-log-files)  
[11.6 Important Log Files in /var/log](#116-important-log-files-in-varlog)  
[11.7 The /proc and /sys Pseudo-filesystems](#117-the-proc-and-sys-pseudo-filesystems)  
[11.8 /proc Basics](#118-proc-basics)  
[11.9 A Survey of /proc](#119-a-survey-of-proc)  
[11.10 /proc/sys](#1110-procsys)  
[11.11 /sys Basics](#1111-sys-basics)  
[11.12 A Survey of /sys](#1112-a-survey-of-sys)  
[11.13 sar](#1113-sar)  
[Lab 11.1: Using Stress](#lab-111-using-stress)  
[Paths and Commands](#paths-and-commands)  

### 11.3: Learning Objectives
----
By the end of this chapter, you should be able to:
  * Understand the concept of inventory and gain familiarity with available system monitoring tools.
  * Understand where the system stores log files and examine the most important ones.
  * Use the `/proc` and `/sys` pseudo-filesystems.
  * Use **sar** to gather system activity and performance data and create reports that are readable by humans.

### 11.4 Available Monitoring Tools
----
Most monitoring tools make use of `/proc` and `/sys`.  
  
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
  
**Memory Monitoring Utilities**
  
Utitity | Purpose                                                               | Package
------- | -------                                                               | -------
free    | Brief summary of memory usage                                         | procps
vmstat  | Detailed virtual memory statistics and block I/O, dynamically updated | procps
pmap    | Process memory map                                                    | procps
  
**I/O Monitoring Utilities**
  
Utitity | Purpose                                                               | Package
------- | -------                                                               | -------
iostat  | CPU utilization and I/O statistics                                    | sysstat
sar     | Display and collect information about system activity                 | sysstat
vmstat  | Detailed virtual memory statistics and block I/O, dynamically updated | procps
  
**Network Monitoring Utilities**
  
Utitity   | Purpose                                          | Package
-------   | -------                                          | -------
netstat   | Detailed networking statistics                   | netstat
iptraf    | Gather information on network interfaces         | iptraf
tcpdump   | Detailed analysis of network packets and traffic | tcpdump
wireshark | Detailed network traffic analysis                | wireshark
  
### 11.5 System Log Files
----
* log files are typically found in `/var/log`
* control of messages is by the **syslogd** daemon (or **rsyslogd** on modern systems) or **journalctl** on newer **systemd**-based systems
* messages can be seen on system console window, too
* view new messages continually: `$ sudo tail -f /var/log/messages` or `$ sudo tail -f /var/log/syslog` or `$ dmesg -w`

### 11.6 Important Log Files in /var/log
----
* log files can be viewed on a GNOME desktop, too
* important files in /var/log:

File               | Purpose
----               | -------
boot.log           | System boot messages
dmesg              | Kernel messages saved after boot. To see the current contents of the kernel message buffer, type dmesg.
messages or syslog | All important system messages
secure             | Security related messages

* **logrotate** keeps four previous copies of logs by default; controlled by `/etc/logrotate.conf`

### 11.7 The /proc and /sys Pseudo-filesystems
----
* these are pseudo-filesystems because they exist totally in memory
* information is gathered only when looked at; no constant or periodic polling to update entries

### 11.8 /proc Basics
----
* originally about system processes
* now includes info about system properties such as interrupts, memory, networking, etc.

### 11.9 A Survey of /proc
----
* subdirectory for each process on the system
* for interrupts, we see what type it is, how many times it has been handled by each CPU, and which devices are registered to respond to it

### 11.10 /proc/sys
----
* `/proc/sys` shows tunable system parameters
* see [Paths and Commands](#paths-and-commands) for a summary of the subdirectories
* parameters can be viewed and changed
   * view maximum number of threads allowed on the system: `$ cat /proc/sys/kernel/threads-max`
   * change maximum number of threads: `$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'`
   * same thing using **sysctl**: `$ sudo sysctl kernel.threads-max=100000`

### 11.11 /sys Basics
----
* the `/sys` pseudo-filesystem is an integral part of the **Unified Device Model**
* conceptually based on a **device tree**
* more tightly defined structure than `/proc`

### 11.12 A Survey of /sys
----
* the **sysfs** virtual filesystem should be mounted under `/sys`
* network devices can be examined with `$ ls -lF /sys/class/net`

### 11.13 sar
----
* **S**ystems **A**ctivity **R**eporter; gathers system activity and performance data and creates human-readable reports
* supported on the backend by **sadc** (system activity data collector)
   * sadc stores information in `/var/log/sa` with a daily frequency by default
   * periodic collection usually started as a cron job stored in `/etc/cron.d/sysstat`
* sar is invoked via `$ sar <options> <interval> <count>`
   * report is repeated after <interval> seconds a total of <count> (default = 1) times
   * with no options, it gives a report on CPU usage

**sar Options**

Option | Meaning
------ | -------
-A 	   | Almost all information
-b 	   | I/O and transfer rate statistics (similar to iostat)
-B 	   | Paging statistics including page faults
-x 	   | Block device activity (similar to iostat -x)
-n 	   | Network statistics
-P 	   | Per CPU statistics (as in sar -P ALL 3)
-q 	   | Queue lengths (run queue, processes and threads)
-r 	   | Swap and memory utilization statistics
-R 	   | Memory statistics
-u 	   | CPU utilization (default)
-v 	   | Statistics about inodes and files and file handles
-w 	   | Context switching statistics
-W 	   | Swapping statistics, pages in and out per second
-f 	   | Extract information from specified file, created by the -o option
-o 	   | Save readings in the file specified, to be read in later with -f option

* examples:
   * paging statistics: `$ sar -B 3 3 `
   * I/O and transfer rate statistics: `$ sar -b 3 3`

### Lab 11.1: Using Stress
----
* **stress** is designed to place a configurable amount of stress on the system  
* example: `$ stress -c 8 -i 4 -m 6 -t 20s`
   * fork off 8 CPU-intensive processes, each spinning on a sqrt() calculation
   * fork off 4 I/O-intensive process, each spinning on sync()
   * fork off 6 memory-intensive processes, each spinning on malloc(), allocating 256 MB by default; size can be changed as in `--vm-bytes 128M`
   * run the stress test for 20 s
  
My solution:  
* `$ sudo yum install stress`
* `$ stress -c 8 -i 4 -m 6 -t 20s`

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
monitoring, system | `$ sudo tail -f /var/log/messages` | view new messages continually | LFS201 11.5
monitoring, system | `$ sudo tail -f /var/log/syslog`  (or `$ sudo telinit 3`) | view new messages continually | LFS201 11.5
monitoring, system | `$ dmesg -w`  (or `$ sudo telinit 5`) | view new messages continually | LFS201 11.5
monitoring, system | `$ cat /proc/sys/kernel/threads-max` | view maximum number of threads allowed on the system | LFS201 11.10
monitoring, system | `$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'` | change maximum number of threads | LFS201 11.10
monitoring, system | `$ sudo sysctl kernel.threads-max=100000` | change maximum number of threads using **sysctl** | LFS201 11.10
monitoring, system | `$ sar <options> <interval> <count>` | generic sar (Systems Activity Reporter) usage | LFS201 11.13
monitoring, system | `$ sar -B 3 3 ` | using sar to get paging statistics | LFS201 11.13
monitoring, system | `$ sar -b 3 3` | using sar to get I/O and transfer rate statistics | LFS201 11.13
monitoring, system | `$ gnome-system-monitor` | launch graphical monitor | LFS201 Lab 11.1
monitoring, system | `$ stress -c 8 -i 4 -m 6 -t 20s` | stress test the system | LFS201 Lab 11.1
  