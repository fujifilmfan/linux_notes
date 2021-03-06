Processes, System Monitoring, System Tunables
-----

### Chapters: LFS101: 9, LFS201: 3, 11, 12, 13, 14, 15, 25

### Ch.3
* find files in /usr/bin with SUID / SGID /sticky bits set - `$ find /usr/bin -perm -4000 | xargs ls -l`
    * Identify processes started by setuid programs (s instead of x for setuid suid or setgid sgid)
    * Run a setuid program owned by a different user
    * Use top or ps to filter processes by state: running, sleeping, stopped, zombie
    * Identify daemons currently running on the system
    * View RUID, RGID, EUID, and EGID for a process

### ulimit
* view all current resource limits - `$ ulimit -a`
* view the maximum number of open file descriptors - `$ ulimit -n`
* view the current soft limit of open file descriptors - `$ ulimit -S -n`
* view the current hard limit of open file descriptors - `$ ulimit -H -n`
* set the limit of open file descriptors to the hard limit and verify - `$ ulimit -n hard`, `$ ulimit -n`
* set the hard limit of open file descriptors to 2048 and verify - `$ ulimit -n 2048`, `$ ulimit -n`
* set the limit of open file descriptors back to 4096 - `$ ulimit -n 4096`
* make hard and soft limits of open file descriptors persistent - `$ sudo vim /etc/security/limits.conf`

### Processes
* (help) run ps with -ef, -elf - what is the difference?
* (help) run ps with aux, auxf - what is the difference?
* (help) run ps so that only the process ID, priority, nice value, process command line, and command with args are displayed - `$ ps -o pid,pri,ni,cmd,command`, `$ ps axo "%p %n %c" (no priority)`, `$ ps axo pid,pri,ni,comm, command`
* run pstree to and show args and PIDs - `$ pstree -ap`
* choose the trunk of any branch in pstree and show children from `/proc` - `$ ls -al /proc/<pid>/task`
* start a new bash session and then another using nice, giving it a nice value of 10 - `$ bash`, `$ nice -n 10 bash`, `$ ps -o pid,pri,ni,cmd` or `$ ps lf`
* change the nice value of one of the bash sessions to 15 using renice and verify - `$ renice 15 -p 22171`
* lower the niceness of the same job to 5 - `$ sudo renice -n 5 -p 22171`
* show the bash processes in top - `$ top` (apply sort-by field (F), filtering (U), sorting (R), and locating (L))

### Monitoring process states
* use dd to start a background process which reads from `/dev/urandom` and writes to `/dev/null` - `$ dd if=/dev/urandom of=/dev/null &`
* check the process state, what should it be? - `$ ps -C dd -o pid,cmd,stat`, should be S or R
* bring the process to the foreground using the fg command, look at process state again - `$ fg`
* hit Ctrl-Z, what does this do? look at the process state again, what is it? - `^Z`, should be T (stopped by job control signal)
* run the jobs program, what does it tell you? - `$ jobs -l`
* bring the job back to the foreground, put it back to background with bg, then terminate it using kill (SIGTERM)  or killall from another window - `$ fg`, `$ bg`, `$ kill 25899`

### System tunables with sysctl
* check if you can ping your own system - `$ ping localhost`
* check the current value of net.ipv4.icmp_echo_ignore_all - `$ sysctl net.ipv4.icmp_echo_ignore_all`
* set the value to 1 using the sysctl command line utility, verify - `$ sudo sysctl net.ipv4.icmp_echo_ignore_all=1`
* set the value back to 0, verify - `$ sudo sysctl net.ipv4.icmp_echo_ignore_all=0`
* change the value by modifying `/etc/sysctl.conf` and force the system to activate this setting file without a reboot, verify, revert - add `net.ipv4.icmp_echo_ignore_all=1`, then `$ sysctl -p`
  
### Changing maximum process ID
* obtain the current maximum PID value - `$ sysctl kernel.pid_max` or `$ cat /proc/sys/kernel/pid_max`
* find out what current PIDs are being issued - `$ cat &`, `$ kill <pid>`
* reset pid_max to a lower value than the ones currently being issued, verify - `$ sudo sysctl kernel.pid_max=24000` or `$ echo 24000 > /proc/sys/kernel/pid_max`
* start a new process and see what it gets as a PID; kill the process - `$ cat &`, `$ kill -9 <pid>`
* see what command line a system was booted in - `$ cat /proc/cmdline`
* show current kernel parameters - `$ sysctl -a`
* view pseudofiles corresponding to current kernel paramters - `$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'` or `$ sudo sysctl net.ipv4.ip_forward=1` sets a value with the **sysctl** interface
* what's in sysctl config? - `$ cat /etc/sysctl.conf`
  
### Scheduling
* interactively schedule a task to run in one minute from now - `$ at now + 1 minute`, `at> top`, `at> CTRL-D` (keep in mind: "The user will be mailed standard error and standard output from his commands, if any.")
* view the at queue - `$ atq`
* remove a job from the at queue - `$ atrm <job number>`
* schedule a task from a file - create an executable, then `$ at now + 1 minute -f testat.sh`
* set up a cron job to do some simple task every Tuesday - `$ crontab -e` (simple file: "#!/bin/bash  \n  echo Hello I am running $0 at $(date)")
* set up a cron job to do some simple task on the 14th day of every month - `$ crontab -e`
* set up a cron job to do some simple task every 15 minutes during Sunday 'football hours' using a 'mycrontab' file - `$ vim mycrontab`, add relevant line, make it executable, `$ crontab mycrontab`, `$ sudo ls -l /var/spool/cron/student`, `$ sudo cat /var/spool/cron/student`
* verify the cron job was loaded - `$ crontab -l`, 
* remove the cron job - `$ crontab -r`
* put a job to sleep using sleep - 
  
### Threads, I/O, paging
* view new messages continually: `$ sudo tail -f /var/log/messages` or `$ sudo tail -f /var/log/syslog` or `$ dmesg -w`
* logrotate
* view maximum number of threads allowed on the system - `$ cat /proc/sys/kernel/threads-max`
* change maximum number of threads using bash - `$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'`
* change maximum number of threads using sysctl - `$ sudo sysctl kernel.threads-max=100000`
* sadc stores information in `/var/log/sa` with a daily frequency by default
* paging statistics: `$ sar -B 3 3 `
* I/O and transfer rate statistics: `$ sar -b 3 3`
  
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
  
### Memory
* show brief summary of memory usage - `$ free`
* get basic memory statistics - `$ free -m` or `$ free -h`
* show detailed virtual memory statistics in MB, active / inactive memory, and repeated every 2 seconds 4 times - `$ vmstat -S m -a 2 4`
* show table of memory statistics with units - `$ vmstat -s -S m`
* show table of disk statistics - `$ vmstat -d`
* show table of statistics for /dev/sda1 - `$ vmstat -p /dev/sda1 2 4`
  
### Swap
* check current swap space - `$ cat /proc/swaps`
* add more swap space by adding either a new partition or a file - `$ dd if=/dev/zero of=swpfile bs=1M count=1024`, `$ mkswap swpfile` or `$ mkswap <partition name>`
* activate the new swap space - `$ sudo swapon swpfile`
* remove the swap file from use and delete it to save space - `$ sudo swapoff swpfile`, `$ sudo rm swpfile`
* turn off all swap - `$ sudo /sbin/swapoff -a`
* turn on all swap - `$ sudo /sbin/swapon -a`

### Stress
* stress system and monitor logs - `$ stress -m 8 -t 10s`, `$ dmesg` or `/var/log/messages`, `/var/log/syslog`

### I/O monitoring
* show a default I/O report - `$ iostat`
* show an I/O report with results in KB instead of blocks - `$ iostat -k`
* show a detailed I/O report by KB - `$ iostat -xk`
* show an I/O report with results in MB - `$ iostat -m`
* show an I/O report with results by device name - `$ iostat -N`
* show table of current I/O usage - `$ sudo iotop`
* show table of only processes or threads actually doing I/O - `$ sudo iotop -o`

### Important files in /var/log

File               | Purpose
----               | -------
boot.log           | System boot messages
dmesg              | Kernel messages saved after boot. To see the current contents of the kernel message buffer, type dmesg.
messages or syslog | All important system messages
secure             | Security related messages

### Extras
* Lab 3.2: ipcs
* Lab 11.1: stress
* 13: badness value
* 14: ionice
* Lab 14.1: bonnie++ benchmarking
* Lab 14.2: fs_mark
* Lab 15.1: comparing I/O schedulers
* LFS101 9: pkill
    * `$ pkill [-signal] [options] [pattern]`
    * `$ pkill -u libby foobar` (will kill all of **libby**'s processes with a name of **foobar**)
    * `$ pkill -HUP rsyslogd` (makes **rsyslog** re-read its config file)
  