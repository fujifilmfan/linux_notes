Processes, System Monitoring, System Tunables: 3, 11, 12, 13, 14, 15, 25, 9 (101)
-----

### Ch.3
* Other stuff
    * find files in /usr/bin with SUID or SGID set (4 SUID, 2 SGID, 1 sticky)
    > $ for result in `find * -perm /u+s`; do echo $result; done | xargs ls -l
    > $ find * -perm -4000 | xargs ls -l
    * Identify processes started by setuid programs (s instead of x for setuid suid or setgid sgid)
    * Run a setuid program owned by a different user
    * Use top or ps to filter processes by state: running, sleeping, stopped, zombie
    * Identify daemons currently running on the system
    * Start a process, put it in the background, bring it back to foreground
    * View RUID, RGID, EUID, and EGID for a process
* Lab 3.1 
    1. Start a new shell by typing bash (or opening a new terminal) so that your changes are only effective in the new shell. View the current limit on the number of open files and explicitly view the hard and soft limits.
    ```
    $ bash
    $ ulimit -a
    $ ulimit -n
    $ ulimit -S -n
    $ ulimit -H -n
    ```
    2. Set the limit to the hard limit value and verify if it worked.
    ```
    $ ulimit -n hard
    $ ulimit -n
    ```
    3. Set the hard limit to 2048 and verify it worked.
    ```
    $ ulimit -n 2048
    $ ulimit -n
    ```
    4. Try to set the limit back to the previous value. Did it work?
    ```
    $ ulimit -n 4096
    $ ulimit -n
    ```
    5. Make the limits persistent
    ```
    $ sudo vim /etc/security/limits.conf
    ```
* Lab 12.1
    1. Run ps with the options -ef. Then run it again with the options aux and auxf. Note the differences in the output. (What does -elf do differently?)
    ```
    $ ps -ef
    $ ps aux
    ```
    2. Run ps so that only the process ID, priority, nice value, and the process command line are displayed. Add command, too. pstree -aAp <pid> just for kicks (-a shows args, -A uses ASCII, -p shows PIDs); look at the same in /proc/<pid>/task
    ```
    $ ps -o pid,pri,ni,cmd,command
    $ ps aux "%p %n %c" (no priority)
    $ ps axo pid,pri,ni,comm
    ```
    3. Start a new bash session by typing bash at the command line. Start another bash session using the nice command but this time giving it a nice value of 10.
    ```
    $ bash
    $ nice -n 10 bash
    $ ps -o pid,pri,ni,cmd
    ```
    4. Run ps as in step 2 to note the differences in priority and nice values. Note the process ID of the two bash sessions.  Use ps lf to see processes and priorities.
    5. Change the nice value of one of the bash sessions to 15 using renice. Once again, observe the change in priority and nice values.
    ```
    $ renice 15 -p 22171
    $ ps -o pid,pri,ni,cmd
    ```
    6. Run top and watch the output as it changes. Hit q to stop the program.
    ```
    $ top
    ```
* Lab 12.2 ps
    1. Use dd to start a background process which reads from /dev/urandom and writes to /dev/null.
    ```
    $ dd if=/dev/urandom of=/dev/null &
    ```
    2. Check the process state. What should it be?
    ```
    $ ps -C dd -o pid,cmd,stat
    Should be S or R.
    ```
    3. Bring the process to the foreground using the fg command. Then hit Ctrl-Z. What does this do? Look at the process state again, what is it?
    ```
    $ fg $ ^Z
    $ ps -C dd -o pid,cmd,stat
    State should be T.
    ```
    4. Run the jobs program. What does it tell you?
    ```
    $ jobs -l
    ```
    5. Bring the job back to the foreground, put it back to background with bg, then terminate it using kill from another window. Difference between kill (SIGTERM) and kill -9 (SIGKILL)?
    ```
    $ fg
    $ kill 25899
    ```
* Lab 25.1
    1. Check if you can ping your own system.
    ```
    1. $ ping localhost
    ```
    2. Check the current value of net.ipv4.icmp_echo_ignore_all, which is used to turn on and off whether your system will respond to ping. A value of 0 allows your system to respond to pings.
    ```
    2. $ sysctl net.ipv4.icmp_echo_ignore_all
    ```
    3. Set the value to 1 using the sysctl command line utility and then check if pings are responded to.
    ```
    3. $ sudo sysctl net.ipv4.icmp_echo_ignore_all=1 $ ping localhost
    ```
    4. Set the value back to 0 and show the original behavior in restored.
    ```
    4. $ sudo sysctl net.ipv4.icmp_echo_ignore_all=0 $ ping localhost
    ```
    5. Now change the value by modifying /etc/sysctl.conf and force the system to activate this setting file without a reboot.
    ```
    5. Add the following line to /etc/sysctl.conf: in /etc/sysctl.conf
            net.ipv4.icmp_echo_ignore_all=1
    and then do:
    $ sysctl -p
    ```
    6. Check that this worked properly.
    ```
    6. $ sysctl net.ipv4.icmp_echo_ignore_all $ ping localhost
    Since the changes to /etc/sysctl.conf are persistent, you probably want to restore things to its previous state.
    ```
* Lab 25.2
    1. Obtain the current maximum PID value.
    ```
    1. $ sysctl kernel.pid_max
    $ cat /proc/sys/kernel/pid_max
    ```
    2. Find out what current PIDs are being issued
    ```
    2. Type:
    $ cat &
    [1] 29222
        $ kill -9 29222
    ```
    3. Reset pid_max to a lower value than the ones currently being issued.
    ```
    3. $ sudo sysctl kernel.pid_max=24000
    $ echo 24000 > /proc/sys/kernel/pid_max # This must be done as root $ cat /proc/sys/kernel/pid_max
    ```
    4. Start a new process and see what it gets as a PID.  Kill the process.
    ```
    4. $ cat & [2] 311
    $ kill -9 311 or $ kill -SIGKILL 311
    ```
* see what command line a system was booted in
> cat /proc/cmdline
* show current kernel parameters
> sysctl -a
* where are the corresponding pseudofiles?
```
equivalent:
`$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'`
`$ sudo sysctl net.ipv4.ip_forward=1` sets a value with the **sysctl** interface
```
* sysctl config: /etc/sysctl.conf

* Lab 9.2 (LFS101)
    1. Open gedit on a new file.  Afterwards, you can no longer type in the terminal window. 
        > $ gedit somefile
    2. While your pointer is over the terminal window, hit CTRL-Z.  Afterwards, you can no longer type in the gedit window. 
    ^Z
    3. With jobs -l, see what processes have been launched from this terminal window:
    $ jobs -l
    4. Now put the most recent job (gedit somefile) in background. Afterwards, you should be able to type in the gedit window.
    $ bg
    4. Put the process in foreground again. Afterwards, you can no longer type in the terminal window.
    $ fg
    5. To clean up, suspend the process again and then use kill to terminate it:
    ^Z
* use the **at** utility program to execute any non-interactive command at a specified time; example:  
   `$ at now + 2 days`  
   `at> top`  
   `at> <EOT>` # enter **CTRL-D** here  
   > job 1 at Wed Jul 18 13:59:00 2018  
   
   `$ atq`  
   > 1 Wed Jul 18 13:59:00 2018 a student  
   
   `$ atrm 1`  
   `$ atq`  
   `$ `  
* use crontab -e to schedule a job
* put a job to sleep using sleep
#### Lab 9.4a (Lab 3): Using at for Future Batch Processing
Schedule a very simple task to run at a future time from now. This can be as simple as running ls or date and saving the output. (You can use a time as short as one minute in the future.)  
  
Note that the command will run in the directory from which you schedule it with at.  
  
Do this:
1. From a short bash script.  
2. Interactively.  
   * My solution (interactive):  
   `$ at now + 1 minute`  
   `at> top`  
   `at> <EOT>` # enter **CTRL-D** here  
     > job 1 at Wed Jul 18 13:59:00 2018  
        
     `$ atq`  
     > 1 Wed Jul 18 13:59:00 2018 a student  
   
     `$ atrm 1`  
     `$ atq`  
     `$ `  

     > Results were in mail, I think  
  
   * My implementation of lab solution (script):  
     > 705  touch testat.sh  
     > 706  vim testat.sh  
     > 707  ls -al  
     > 708  chmod +x testat.sh  
     > 709  ls -al  
     > 710  at now + 1 minute -f testat.sh  
     > 712  cat /tmp/datestamp  

#### Lab 9.4b (Lab 4): Scheduling a Periodic Task with cron
Set up a cron job to do some simple task every day at 10 AM.  
  
* My solution:  
`$ crontab -e`  
`00 10 * * * /home/student/testat.sh`  
  
* Lab solution:  
  Set up a cron job to do some simple task every day at 10 AM. Create a file named mycrontab with the following content:  

  `0 10 * * * /tmp/myjob.sh`  
  and then create /tmp/myjob.sh containing:  
   ```bash
   #!/bin/bash
   echo Hello I am running $0 at $(date)
   ```
  
   and make it executable:  
   `$ chmod +x /tmp/myjob.sh`  

   Put it in the crontab system with:  
   `$ crontab mycrontab`  

   and verify it was loaded with:  
   `$ crontab -l`  
   > 0 10 * * * /tmp/myjob.sh  
   
   `$ sudo ls -l /var/spool/cron/student`  
   > -rw------- 1 student student 25 Apr 22 09:59 /var/spool/cron/student  
   
   `$ sudo cat /var/spool/cron/student`  
   > 0 10 * * * /tmp/myjob.sh  
  
Note: if you don't really want this running every day, printing out messages like:  

 > Hello I am running /tmp/myjob.sh at Wed Apr 22 10:03:48 CDT 2015  

and mailing them to you, you can remove it with:  

 `$ crontab -r`  
* **killall** kills all processes with a given name; uses a command name rather than PID:
    * `$ killall bash`
    * `$ killall -9 bash`
    * `$ killall -SIGKILL bash`
* **pkill** sends a signal to a process using selection criteria
    * `$ pkill [-signal] [options] [pattern]`
    * `$ pkill -u libby foobar` (will kill all of **libby**'s processes with a name of **foobar**)
    * `$ pkill -HUP rsyslogd` (makes **rsyslog** re-read its config file)



### Ch.11

* view new messages continually: `$ sudo tail -f /var/log/messages` or `$ sudo tail -f /var/log/syslog` or `$ dmesg -w`
* logrotate
* view maximum number of threads allowed on the system: `$ cat /proc/sys/kernel/threads-max`
* change maximum number of threads: `$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'`
* same thing using **sysctl**: `$ sudo sysctl kernel.threads-max=100000`
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
* important files in /var/log:
File               | Purpose
----               | -------
boot.log           | System boot messages
dmesg              | Kernel messages saved after boot. To see the current contents of the kernel message buffer, type dmesg.
messages or syslog | All important system messages
secure             | Security related messages

### Ch.13
* show brief summary of memory usage - `$ free`
* show detailed virtual memory statistics in MB, active / inactive memory, and repeated every 2 seconds 4 times - `$ vmstat -S m -a 2 4`
* show table of memory statistics with units - `$ vmstat -s -S m`
* show table of disk statistics - `$ vmstat -d`
* show table of statistics for /dev/sda1 - `$ vmstat -p /dev/sda1 2 4`

* Lab 13.1
    * Turn off all swap with the command
    ```
    $ sudo /sbin/swapoff -a
    ```
    * Make sure you turn it back on later, when we are done, with
    ```
    $ sudo /sbin/swapon -a
    ```
    * Now we are going to put the system under increasing memory pressure. One way to do this is to exploit the stress program we installed earlier, running it with arguments such as:
    ```
    $ stress -m 8 -t 10s
    ```
    which would keep 2 GB busy for 10 seconds.
    You should see the OOM (Out of Memory) killer swoop in and try to kill processes in a struggle to stay alive. You can see what is going on by running dmesg or monitoring /var/log/messages or /var/log/syslog, or through graphical interfaces that expose the system logs.

### Ch.14
* show a default I/O report - `$ iostat`
* show an I/O report with results in KB instead of blocks - `$ iostat -k`
* show a detailed I/O report by KB - `$ iostat -xk`
* show an I/O report with results in MB - `$ iostat -m`
* show an I/O report with results by device name - `$ iostat -N`
* show table of current I/O usage - `$ sudo iotop`
* show table of only processes or threads actually doing I/O - `$ sudo iotop -o`


### Extras
* Lab 3.2 ipcs
* Lab 11.1 stress
* 13: badness value
* 14: ionice
* Lab 14.1 bonnie++ benchmarking
* Lab 14.2 fs_mark
* Lab 15.1 comparing I/O schedulers







