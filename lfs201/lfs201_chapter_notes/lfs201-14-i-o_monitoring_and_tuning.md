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
* **bonnie++** is a widely available benchmarking program that tests and measures the performance of drives and filesystems. It
is descended from **bonnie**, an earlier implementation.  
* Results can be read from the terminal window or directed to a file, and also to a **csv** format (comma separated value).
Companion programs, bon **csv2html** and **bon csv2txt**, can be used convert to html and plain text output formats.  
* We recommend you read the **man** page for **bonnie++** before using as it has quite a few options regarding which tests to
perform and how exhaustive and stressful they should be. A quick synopsis is obtained with:
* `$ bonnie++ --help` gives a synopsis of commands
* `$ time sudo bonnie++ -n 0 -u 0 -r 100 -f -b -d /mnt` run a system test where:
    * **-n** 0 means don’t perform the file creation tests.
    * **-u** 0 means run as root.
    * **-r 100** means pretend you have 100 MB of RAM.
    * **-f** means skip per character I/O tests.
    * **-b** means do a **fsync** after every write, which forces flushing to disk rather than just writing to cache.
    * **-d** `/mnt` just specifies the directory to place the temporary file created; make sure it has enough space, in this case 300
MB, available.
    * If you don’t supply a figure for your memory size, the program will figure out how much the system has and will create a testing
file 2-3 times as large.
    * running the command:
        ```
        Using uid:0, gid:0.
        Writing intelligently...done
        Rewriting...done
        Reading intelligently...done
        start 'em...done...done...done...done...done...
        Version  1.97       ------Sequential Output------ --Sequential Input- --Random-
        Concurrency   1     -Per Chr- --Block-- -Rewrite- -Per Chr- --Block-- --Seeks--
        Machine        Size K/sec %CP K/sec %CP K/sec %CP K/sec %CP K/sec %CP  /sec %CP
        centos         300M           +++++ +++ +++++ +++           +++++ +++ +++++ +++
        Latency                        8008us     177us               120us    1801us

        1.97,1.97,centos,1,1558802244,300M,,,,+++++,+++,+++++,+++,,,+++++,+++,+++++,+++,,,,,,,,,,,,,,,,,,,8008us,177us,,120us,1801us,,,,,,

        real	0m3.961s
        user	0m0.046s
        sys	0m0.885s
        ```
* `$ bon_csv2html < bonnie++.out > bonnie++.html` convert **bonnie++.out** to HTML
    * that worked
    * see "Screenshot-2019-5-25 Bonnie++ Benchmark results.png"
* `$ bon_csv2txt < bonnie++.out > bonnie++.txt` convert **bonnie++.out** to plain text
* Try longer and larger, more ambitious tests. Try some of the tests we turned off. If your system is behaving well, save the results for future benchmarking comparisons when the system is sick.

### Lab 14.2: fs_mark
----
* The **fs mark** benchmark gives a low level bashing to file systems, using heavily asynchronous I/O across multiple directories
and drives. It’s a rather old program written by Rick Wheeler that has stood the test of time.
* It can be downloaded from http://sourceforge.net/projects/fsmark/ Once you have obtained the tarball, you can unpack it and compile it with:
    * `$ tar zxvf fs_mark-3.3.tgz`
    * `$ cd fs_mark`
    * `$ sudo yum install glibc-static` I had to do this first
    * `$ make`
* Read the README file as we are only going to touch the surface.
* For a test we are going to create 1000 files, each 10 KB in size, and after each write we’ll perform an **fsync** to flush out to disk. This can be done in the `/tmp` directory with the command: `$ fs_mark -d /tmp -n 1000 -s 10240`
    ```

    #  ./fs_mark  -d  /tmp  -n  1000  -s  10240 
    #	Version 3.3, 1 thread(s) starting at Sat May 25 14:23:16 2019
    #	Sync method: INBAND FSYNC: fsync() per file in write loop.
    #	Directories:  no subdirectories used
    #	File names: 40 bytes long, (16 initial bytes of time stamp with 24 random bytes at end of name)
    #	Files info: size 10240 bytes, written with an IO size of 16384 bytes per write
    #	App overhead is time in microseconds spent in the test not doing file writing related system calls.

    FSUse%        Count         Size    Files/sec     App Overhead
        41         1000        10240       2205.3             9263

    ```
* While this is running, gather extended **iostat** statistics with: `$ iostat -x -d /dev/sda 2 20` in another terminal window.
    ```
    Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
    sda               0.00     0.00   16.50    0.00    68.00     0.00     8.24     0.00    0.06    0.06    0.00   0.06   0.10

    Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
    sda               0.00  1131.00    0.00  541.50     0.00  8122.00    30.00     0.05    0.09    0.00    0.09   0.09   4.85

    Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
    sda               0.00  1919.00    0.00  959.50     0.00 14082.00    29.35     0.08    0.08    0.00    0.08   0.08   7.65

    Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
    sda               0.00     0.00   26.00    2.00   422.00    14.00    31.14     0.03    1.23    0.38   12.25   0.88   2.45

    ```
* The numbers you should surely note are the number of files per second reported by **fs_mark** and the percentage of CPU time
utilized reported by **iostat**. If this is approaching 100 percent, you are I/O-bound.
* Depending on what kind of filesystem you are using you may be able to get improved results by changing the **mount** options. For example, for **ext3** or **ext4** you can try:
    * `$ mount -o remount,barrier=1 /tmp` or for **ext4** you can try:
    * `$ mount -o remount,journal_async_commit /tmp`
  
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
monitoring, performance, i/o, testing | `$ bonnie++ ...` | benchmarking application | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ time sudo bonnie++ -n 0 -u 0 -r 100 -f -b -d /mnt` | run a system test using **bonnie++** | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ bon_csv2html < bonnie++.out > bonnie++.html` | convert **bonnie++.out** to HTML | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ bon_csv2txt < bonnie++.out > bonnie++.txt` | convert **bonnie++.out** to plain text | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ fs_mark -d /tmp -n 1000 -s 10240` | test filesystem by creating 1000 files, each 10 KB in size, and then performing an **fsync** to flush out to disk | LFS201 Lab 14.2
monitoring, performance, i/o, testing | `$ iostat -x -d /dev/sda 2 20` | gather i/o statistics while running **fs_mark** test | LFS201 Lab 14.2
monitoring, performance, i/o, testing | `$ mount -o remount,barrier=1 /tmp` | change **mount** options for **ext3** or **ext4** to improve performance | LFS201 Lab 14.2
monitoring, performance, i/o, testing | `$ mount -o remount,journal_async_commit /tmp` | change **mount** options for **ext4** to improve performance | LFS201 Lab 14.2
  