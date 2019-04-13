Chapter 14: I/O Monitoring and Tuning
-------------------------------------

[14.3: Learning Objectives](#143-learning-objectives)  
[14.4: I/O Monitoring and Disk Bottlenecks](#144-io-monitoring-and-disk-bottlenecks)
[14.5: iostat](#145-iostat)
[14.6: iostat Options](#146-iostat-options)
[14.7: iostat Extended Options](#147-iostat-extended-options)
[14.8: iotop](#148-iotop)
[14.9: Using ionice to Set I/O Priorities](#149-using-ionice-to-set-io-priorities)
[14.10: Using iotop and iostat Demo](#1410-using-iotop-and-iostat-demo)
[Lab 14.1: bonnie++](#lab-141-bonnie)
[Lab 14.2: fs_mark](#lab-142-fs_mark)
[Paths and Commands](#paths-and-commands)  

### 14.3: Learning Objectives
----
By the end of this chapter, you should be able to:
  * Understand the importance of monitoring I/O activity and when it constitutes system performance bottlenecks.
  * Use **iostat** to monitor system I/O device activity.
  * Use **iotop** to display a constantly updated table of current I/O usage.
  * Use **ionice** to set both the **I/O scheduling class** and the **priority** for a given process.

### 14.4: I/O Monitoring and Disk Bottlenecks
----
* disk performance problems can be strongly coupled to insufficient memory or inadequate network hardware and tuning
* a system can be considered as **I/O-bound** when the CPU is found sitting idle waiting for I/O to complete or the network is waiting to clear buffers
   * slow I/O can create the appearance of insufficient memory or cause network throughput to suffer
   * real-time monitoring and tracing are necessary for locating and mitigating disk bottlenecks
  
### 14.5: iostat
----
* use `$ iostat` to show a default I/O report
   * **tps**: I/O transactions per second
   * blocks read and written per unit time, where blocks are generally 512-byte sectors
   * total blocks read and written
* information broken out by disk partition
  
### 14.6: iostat Options
----
* `$ iostat -k` to show results in KB instead of blocks (this seems to be the default behavior anyway)
* `$ iostat -m` to get results in MB
* `$ iostat -N` to show by device name (or -d for a somewhat different format)
  
### 14.7: iostat Extended Options
----
* `$ iostat -xk` to see a more detailed report

**Extended iostat Fields**

Field    | Meaning 
-----    | -------
Device   | Device or partition name 
rrqm/s   | Number of read requests merged per second, queued to device 
wrqm/s   | Number of write requests merged per second, queued to device 
r/s      | Number of read requests per second, issued to the device
w/s      | Number of write requests per second, issued to the device 
rkB/s    | KB read from the device per second 
wkB/s    | KB written to the device per second 
avgrq-sz | Average request size in 512 byte sectors per second 
avgqu-sz | Average queue length of requests issued to the device 
await    | Average time (in msecs) I/O requests between when a request is issued and when it is completed: queue time plus service time 
svctm    | Average service time (in msecs) for I/O requests 
%util    | Percentage of CPU time during the device serviced requests 
  
### 14.8: iotop
----
* must be run as root, `$ sudo iotop`
* shows table of current I/O usage, and it updates periodically
   * **be** stands for **best effort** (see ionice section)
   * **rt** stands for **real time** (see ionice section)
* `$ sudo iotop -o` use -o (or --only) option to show only processes or threads actually doing I/O
  
### 14.9: Using ionice to Set I/O Priorities
----
* **ionice** lets you set the I/O **scheduling class** and **priority**
* `$ ionice [-c class] [-n priority] [-p pid] [COMMAND [ARGS] ]` usage syntax
   * giving **pid** with **-p** shows results for requested process, otherwise it is the process that will be started by **COMMAAND**
   * with no arguments, **ionice** returns scheduling class and priority of current shell process:
      * `$ ionice` --> "idle; prio 7" or "unknown; prio 4"
   * the **-c** paramenter specifies the scheduling class
   * the **Best Effort** and **Real Time** classes take the **-n** argument, which gives the **priority** from 0 to 7, with 0 being the highest
* **ionice** only works when using the **CFQ** I/O Scheduler
* example usage: `$ ionice -c 2 -n 3 -p 30078`
* I tried: `$ sudo ionice -c 2 -n 6 -p 47` to change the priority from be/7 to be/6
   
**I/O Scheduling Classes**

I/O Scheduling Class | -c value | Meaning
-------------------- | -------- | -------
None or Unknown      | 0        | Default value
Real Time            | 1        | Get first access to the disk, can starve other processes. The priority defines how big a time slice each process gets.
Best effort          | 2        | All programs serviced in round-robin fashion, according to priority settings. The Default. 
Idle                 | 3        | No access to disk I/O unless no other program has asked for it for a defined period. 

### 14.10: Using iotop and iostat Demo
----
* `$ iostat -m /dev/sda /dev/sdb 2 200`
* `$ sudo iotop`
* `$ for names in */*.vmdk ; do /bin/cp $names /tmp/junk ; done`

### Lab 14.1: bonnie++
----

### Lab 14.2: fs_mark
----
  

### Paths and Commands
----

#### Paths  
 
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
monitoring, i/o | `$ iostat` | shows a default I/O report | LFS201 14.5
monitoring, i/o | `$ iostat -k` | use -k option to show results in KB instead of blocks | LFS201 14.6
monitoring, i/o | `$ iostat -m` | use -m option to get results in MB | LFS201 14.6
monitoring, i/o | `$ iostat -N` | use -N option to show by device name (or -d for a somewhat different format) | LFS201 14.6
monitoring, i/o | `$ iostat -xk` | use -x option to see a more detailed report | LFS201 14.7
monitoring, i/o | `$ sudo iotop` | shows table of I/O usage | LFS201 14.8
monitoring, i/o | `$ sudo iotop -o` | use -o (or --only) option to show only processes or threads actually doing I/O | LFS201 14.8
monitoring, i/o | `$ ionice [-c class] [-n priority] [-p pid] [COMMAND [ARGS] ]` | usage syntax | LFS201 14.9
monitoring, i/o | `$ sudo ionice -c 2 -n 3 -p 30078` | change best effort to priority 3 for process 30078 | LFS201 14.9
monitoring, i/o | `$ sudo ionice -c 2 -n 6 -p 47` | change the priority of process 47 from be/7 to be/6 | LFS201 14.9
