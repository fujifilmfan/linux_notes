Chapter 9: Processes
--------------------

[9.0: Introduction/ Learning Objectives](#90-introduction-learning-objectives)  
[9.1: Introduction to Processes and Process Attributes](#91-introduction-to-processes-and-process-attributes)  
[9.2: Process Metrics and Process Control](#92-process-metrics-and-process-control)  
[9.3: Listing Processes: ps and top](#93-listing-processes-ps-and-top)  
[9.4: Starting Processes in the Future](#94-starting-processes-in-the-future)  
[9.5: Summary](#95-summary)  
[Paths and Commands](#paths-and-commands)  

### 9.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should be able to:
* Describe what a process is and distinguish between types of processes.
* Enumerate process attributes.
* Manage processes using **ps** and **top**.
* Understand the use of load averages and other process metrics.
* Manipulate processes by putting them in **background** and restoring them to **foreground**.
* Use **at**, **cron**, and **sleep** to schedule processes in the future or pause them.

### 9.1: Introduction to Processes and Process Attributes
----
#### What Is a Process?
* **process**: an instance of one or more related **tasks** (**threads**) executing on the computer
* not the same as  **program** or a **command**; a single program can start several processes simultaneously
* some are related to others, some are independent
* the OS, especially the kernel, allocate resources to processes

#### Process Types
* a terminal window is a process; allows users to run programs
* programs can also be run in the **background**, **detached** from the shell

Process Type          | Description | Example
------------          | ----------- | -------
Interactive Processes | Need to be started by a user, either at a command line or through a graphical interface such as an icon or a menu selection. | **bash, firefox, top**
Batch Processes       | Automatic processes which are scheduled from and then disconnected from the terminal. These tasks are queued and work on a **FIFO** (First In, First Out) basis. | **updatedb**
Daemons               | Server processes that run continuously. Many are launched during system startup and then wait for a user or system request indicating that their service is required. | **httpd, xinetd, sshd**
Threads               | Lightweight processes. These are **tasks** that run under the umbrella of a main process, sharing memory and other resources, but are scheduled and run by the system on an individual basis. An individual thread can end without terminating the whole process and a process can create new threads at any time. Many non-trivial programs are multi-threaded. | **firefox, gnome-terminal-server**
Kernel Threads        | Kernel tasks that users neither start nor terminate and have little control over. These may perform actions like moving a thread from one CPU to another, or making sure input/output operations to disk are completed. | **kthreadd, migration, ksoftirqd**

#### Process Scheduling and States
* the **scheduler** (a kernel function) moves processes on and off the CPU
* processes are in a **running** state when (1) executing instructions on CPU or (2) waiting for a **time slice**
* running processes are in a **run queue**
* processes are in a **sleep** state when they are waiting for something to happen (typically I/O to become available (9.2)) before they can resume
* sleeping processes are in a **wait** queue
* **zombie** state when child process completes but parent hasn't asked about it's state; shows in system's list of processes

#### Process and Thread IDs
* the **process ID** (**PID**) is used to track process state, CPU usage, memory use, where resources are located in memory, and other characteristics
* PIDs are assigned in ascending order; PID 1 is the **init** process

ID Type                  | Description
-------                  | -----------
Process ID (PID)         | Unique Process ID number
Parent Process ID (PPID) | Process (Parent) that started this process. If the parent dies, the PPID will refer to an adoptive parent; on recent kernels, this is **kthreadd** which has PPID=2.
Thread ID (TID)          | Thread ID number. This is the same as the PID for single-threaded processes. For a multi-threaded process, each thread shares the same PID, but has a unique TID.

#### Terminating a Process
`$ kill -SIGKILL <pid>`  
`$ kill -9 <pid>`  

#### User and Group IDs

User IDs                                                     | User Group IDs
--------                                                     | --------------
Real User ID (**RUID**): identifies user who started process | Real Group ID (**RGID**): identifies group that started process
Effective User ID (**EUID): determines access rights of the user | Effective Group ID (**EGID**): determines access rights of the group

#### More about Priorities
* the **priority** of a process determines how much CPU time it gets
* priority can be changed via the **nice value**, or **niceness** for the process
* one can also assign **real-time priority** to time-sensitive tasks; this is just a very high priority
* **hard real time** is conceptually different; involves making sure a job is complete within a well-defined time window

### 9.2: Process Metrics and Process Control
----
#### Load Averages
* **load average** is the average of the **load number**; takes into account processes that are:
  * actively running on CPU
  * runnable but waiting for a CPU to become available
  * sleeping
* ? what's the load number?
* unlike UNIX-like operating systems, Linux includes sleeping processes; it only includes **uninterruptible** sleepers, those which cannot be awakened easily
* load average can be viewed by running **w**, **top**, or **uptime**

#### Interpreting Load Averages
The load average is displayed using three different sets of numbers.  
Assuming our system is a single-CPU system, the three load average numbers are interpreted as follows:  
* 0.45: For the last minute the system has been 45% utilized on average.
* 0.17: For the last 5 minutes utilization has been 17%.
* 0.12: For the last 15 minutes utilization has been 12%.
  
If we saw a value of 1.00 in the second position, that would imply that the single-CPU system was 100% utilized, on average, over the past 5 minutes; this is good if we want to fully use a system. A value over 1.00 for a single-CPU system implies that the system was over-utilized: there were more processes needing CPU than CPU was available.  
  
If we had more than one CPU, say a quad-CPU system, we would divide the load average numbers by the number of CPUs. In this case, for example, seeing a 1 minute load average of 4.00 implies that the system as a whole was 100% (4.00/4) utilized during the last minute.  
  
Short-term increases are usually not a problem. A high peak you see is likely a burst of activity, not a new level. For example, at start up, many processes start and then activity settles down. If a high peak is seen in the 5 and 15 minute load averages, it may be cause for concern.  

#### Background and Foreground Processes
Linux supports **background** and **foreground** job processing. (A job in this context is just a command launched from a terminal window.) **Foreground** jobs run directly from the shell, and when one foreground job is running, other jobs need to wait for shell access (at least in that terminal window if using the GUI) until it is completed. This is fine when jobs complete quickly. But this can have an adverse effect if the current job is going to take a long time (even several hours) to complete.  
  
In such cases, you can run the job in the **background** and free the shell for other tasks. The background job will be executed at lower priority, which, in turn, will allow smooth execution of the interactive tasks, and you can type other commands in the terminal window while the background job is running. By default, all jobs are executed in the foreground. You can put a job in the background by suffixing `&` to the command, for example: `$ updatedb &`  
  
You can either use **CTRL-Z** to suspend a foreground job or **CTRL-C** to terminate a foreground job and can always use the **bg** and **fg** commands to run a process in the background and foreground, respectively.  

#### Managing Jobs
The **jobs** utility displays all jobs running in background. The display shows the job ID, state, and command name, as shown here.

`jobs -l` provides the same information as `jobs`, including the PID of the background jobs.

The background jobs are connected to the terminal window, so, if you log off, the jobs utility will not show the ones started from that window.

#### Lab 9.2a (Lab 1): Getting Uptime and Load Averages
1. Ascertain how long your system has been up.  
   * My solution: `$ uptime`  
     >  14:28:34 up 1 day,  1:19,  2 users,  load average: 0.91, 0.82, 0.45  
2. Display its load averages.  
   * My solution: `$ w`  
     >  14:28:45 up 1 day,  1:19,  2 users,  load average: 0.92, 0.83, 0.45  
     > USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT  
     > student  :0       :0               Sat16   ?xdm?  12:39   0.78s /usr/libexec/gnome-session-binary --session gnome-classic  
     > student  pts/0    :0               Sat21    5.00s  0.13s  0.00s w  
   * Another option: `$ top | head`  
     > top - 14:30:10 up 1 day,  1:21,  2 users,  load average: 1.26, 0.96, 0.53  
     > Tasks: 201 total,   1 running, 200 sleeping,   0 stopped,   0 zombie  
     > %Cpu(s): 15.6 us, 14.1 sy,  0.0 ni, 70.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st  
     > KiB Mem :  3863556 total,   807604 free,  1040748 used,  2015204 buff/cache  
     > KiB Swap:   975868 total,   974580 free,     1288 used.  2454772 avail Mem   
     >   
     >    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND  
     >    746 polkitd   20   0  649988  18112   5420 S  31.2  0.5   5:30.02 polkitd  
     >    782 root      20   0  396324   4096   3184 S  12.5  0.1   1:02.49 accounts-daemon  
     >    785 dbus      20   0   61892   4076   1856 S   6.2  0.1   1:19.45 dbus-daemon  

#### Lab 9.2b (Lab 2): Background and Foreground Jobs
We are going to launch a graphical program from a terminal window, so that one can no longer type in the window. **gedit** is an easy choice, but you can substitute any other program that does this.  
  
The Solution file contains a step-by-step procedure for putting jobs in background, bringing them back to foreground, etc. Please repeat the steps, substituting the program you are using if it is not **gedit**.  
  
* My solution:  
   > `$ gedit file1`  
   >  
   > `CTRL-Z`  
   > ^Z  
   > [1]+  Stopped                 gedit file1  
   > `$ jobs -l`  
   > [1]+  Stopped                 gedit file1  
   > `$ bg` or `$ bg 1`  
   > [1]+ gedit file1 &  
   > `$ jobs -l`  
   > [2]- 19997 Running                 gedit file1 &  
   > `$ fg`  
   > `CTRL-Z`  
   > `$ jobs -l`  
   > `$ kill -9 72049`  
   > [1]+  Killed                  gedit file1    
  
### 9.3: Listing Processes: ps and top
----
#### The ps Commmand (System V Style)
**ps** provides information about currently running processes keyed by **PID**. If you want a repetitive update of this status, you can use **top** or other commonly installed variants, such as **htop** or **atop**, from the command line, or invoke your distribution's graphical system monitor application.  
  
**ps** has many options for specifying exactly which tasks to examine, what information to display about them, and precisely what output format should be used.  
  
Without options, **ps** will display all processes running under the current shell. You can use the `-u` option to display information of processes for a specified username. The command `ps -ef` displays all the processes in the system in full detail. The command `ps -eLf` goes one step further and displays one line of information for every **thread** (remember, a process can contain multiple threads).  
  
#### The ps Command (BSD Style)
**ps** has another style of option specification, which stems from the **BSD** variety of UNIX, where options are specified without preceding dashes. For example, the command `ps aux` displays all processes of all users. The command `ps axo` allows you to specify which attributes you want to view.  
  
The screenshot shows a sample output of **ps** with the `aux` and `axo` qualifiers.  
  
#### The Process Tree
**pstree** displays the processes running on the system in the form of a **tree diagram** showing the relationship between a process and its parent process and any other processes that it created. Repeated entries of a process are not displayed, and threads are displayed in curly braces.

#### top
**top** gives constant real-time updates (every two seconds by default)

First Line of the top Output:  
* uptime
* how many users are logged on
* load average

Second Line of the top Output:  
* total number of processes
* the number of running, sleeping, stopped, and zombie processes

Third Line of the top Output:  
* how the CPU time is being divided between the users (**us**) and the kernel (**sy**) by displaying the percentage of CPU time used for each
* percentage of user jobs running at a lower priority (niceness - **ni**)
* idle mode (**id**) should be low if the load average is high, and vice versa
* percentage of jobs waiting (**wa**) for I/O is listed
* interrupts include the percentage of hardware (**hi**) vs. software interrupts (**si**)
* steal time (**st**) is generally used with virtual machines, which has some of its idle CPU time taken for other uses

Fourth and Fifth Lines of the top Output  
* memory usage divided into two categories:
  * physical memory (RAM) (line 4)
  * swap space (line 5) (temporary storage on the hard drive)
* both show total memory, used memory, and free space

Process List of the top Output  
* by default, ordered by highest CPU usage
* the following info is displayed:
  * Process Identification Number (PID)
  * Process owner (USER)
  * Priority (PR) and nice values (NI)
  * Virtual (VIRT), physical (RES), and shared memory (SHR)
  * Status (S)
  * Percentage of CPU (%CPU) and memory (%MEM) used
  * Execution time (TIME+)
  * Command (COMMAND)

Interactive Keys with top  

Command | Output
------- | ------
t       | Display or hide summary information (rows 2 and 3)
m       | Display or hide memory information (rows 4 and 5)
A       | Sort the process list by top resource consumers
r       | Renice (change the priority of) specific processes
k       | Kill a specific process
f       | Enter the top configuration screen
o       | Interactively select a new sort order in the process list
n       | Specifies the maximum number of iterations, or frames, top should produce before ending

### 9.4: Starting Processes in the Future
----
#### Scheduling Future Processes using at
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

#### cron
* **cron** is a time-based scheduling utility program; it can launch routine background jobs at specific times and/or days on an on-going basis
* **cron** is driven by a configuration file called `/etc/crontab` (cron table), which contains the various shell commands that need to be run at the properly scheduled times
* there are both system-wide crontab files and individual user-based ones
* each line of a crontab file represents a job, and is composed of a so-called CRON expression, followed by a shell command to execute
* the `crontab -e` command will open the crontab editor to edit existing jobs or to create new jobs
* each line of the crontab file will contain 6 fields:

Field | Description  | Values
----- | -----------  | ------
MIN   | Minutes      | 0 to 59
HOUR  | Hour field   | 0 to 23
DOM   | Day of Month | 1-31
MON   | Month field  | 1-12
DOW   | Day of Week  | 0-6 (0 = Sunday)
CMD   | Command      | Any command to be executed

* examples:
1. `* * * * * /usr/local/bin/execute/this/script.sh` will schedule a job to execute 'script.sh' every minute of every hour of every day of the month, and every month and every day in the week  
2. `30 08 10 06 * /home/sysadmin/full-backup` will schedule a full-backup at 8.30am, 10-June, irrespective of the day of the week  

#### sleep
* **sleep** suspends execution for at least the specified period of time, which can be given as the number of seconds (the default), minutes, hours, or days
* after that time has passed (or an interrupting signal has been received), execution will resume
* syntax:  
`$ sleep NUMBER[SUFFIX]...`  
where SUFFIX may be:  
1. `s` for seconds (the default)  
2. `m` for minutes  
3. `h` for hours  
4. `d` for days  
* **sleep** and **at** are quite different; **sleep** delays execution for a specific period, while **at** starts execution at a later time

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

If the machine is not up at 10 AM on a given day, **anacron** will run the job at a suitable time.  
  
### 9.5: Summary
----
Key concepts covered:
* Processes are used to perform various tasks on the system.
* Processes can be single-threaded or multi-threaded.
* Processes can be of different types, such as interactive and non-interactive.
* Every process has a unique identifier (PID) to enable the operating system to keep track of it.
* The **nice value**, or **niceness**, can be used to set priority.
* **ps** provides information about the currently running processes.
* You can use **top** to get constant real-time updates about overall system performance, as well as information about the processes running on the system.
* Load average indicates the amount of utilization the system is under at particular times.
* Linux supports background and foreground processing for a job.
* **at** executes any non-interactive command at a specified time.
* **cron** is used to schedule tasks that need to be performed at regular intervals.

### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
processes | `/etc/crontab` | configuration file (cron table) | LFS101 9.4
processes | `/var/spool/cron/<username>` | currently loaded crontab | LFS101 9.4

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
processes | `$ kill -SIGKILL <pid>` | terminate a process | LFS101 9.1
processes | `$ kill -9 <pid>` | terminate a process | LFS101 9.1
processes | `$ w` | display who is logged in and what they are doing (also uptime and load in header) | LFS101 9.2
processes | `$ uptime` | print system uptime and load | LFS101 9.2
processes | `$ updatedb &` | run process in background | LFS101 9.2
processes | `CTRL-Z` | suspend a foreground job, place in background as a stopped job | LFS101 9.2
processes | `CTRL-C` | terminate a foreground job | LFS101 9.2
processes | `$ fg <job>` | move background job to foreground | LFS101 9.2
processes | `$ bg <job>` | move stopped job to background | LFS101 9.2
processes | `$ jobs` | list active jobs launched from this terminal window | LFS101 9.2
processes | `$ jobs -l` | include PID in active jobs list | LFS101 9.2
processes | `$ ps` | provides information about currently running processes keyed by PID | LFS101 9.3
processes | `$ ps -u <username>` | display information of processes for a specified username | LFS101 9.3
processes | `$ ps -ef` | displays all the processes in the system in full detail | LFS101 9.3
processes | `$ ps -eLf` | displays one line of information for every thread | LFS101 9.3
processes | `$ ps aux` | displays all processes of all users | LFS101 9.3
processes | `$ ps axo <attributes>` | allows you to specify which attributes you want to view | LFS101 9.3
processes | `$ pstree` | displays the processes running on the system in the form of a tree diagram | LFS101 9.3
apps, processes | `$ top` | display and update sorted information about processes | LFS101 9.3
apps, processes | `$ top | head` | show just the top header | LFS101 9.3
processes | `$ at ...` | starts execution at a later time; contrast with `sleep` | LFS101 9.4
processes | `$ at now + 2 days` at> top  at> <EOT> CTRL-D | schedule top to run in two days  | LFS101 9.4
processes | `$ at now + 1 minute` | schedule program to run in one minute | LFS101 9.4
processes | `$ atq` | view scheduled jobs | LFS101 9.4
processes | `$ atrm 1` | remove schedule job | LFS101 9.4
processes | `$ crontab -e` | open the crontab editor to edit existing jobs or to create new jobs | LFS101 9.4
processes | `$ crontab -l` | displays the current crontab on STDOUT | LFS101 9.4
processes | `$ crontab <filename>` | add file to crontab system | LFS101 9.4
processes | `$ sudo ls -l /var/spool/cron/student` | view currently loaded crontab files | LFS101 9.4
processes | `$ sudo cat /var/spool/cron/student` | view currently loaded crontab file contents | LFS101 9.4
processes | `$ crontab -r` | removes the current crontab | LFS101 9.4
processes | `$ sleep NUMBER[SUFFIX]...` | delays execution for a specific period; contrast with `at` | LFS101 9.4
