Chapter 15: I/O Scheduling
--------------------------

[15.3: Learning Objectives](#153-learning-objectives)  
[15.4: I/O Scheduling](#154-io-scheduling)  
[15.5: I/O Scheduler Choices](#155-io-scheduler-choices)  
[15.6: I/O Scheduling and SSD Devices](#156-io-scheduling-and-ssd-devices)  
[15.7: Tunables and Switching the I/O Scheduler at Runtime](#157-tunables-and-switching-the-io-scheduler-at-runtime)  
[15.8: I/O Schedulers Demo](#158-io-schedulers-demo)  
[15.9: CFQ (Completey Fair Queue) Scheduler](#159-cfq-completey-fair-queue-scheduler)  
[15.10: CFQ Tunables](#1510-cfq-tunables)  
[15.11: Deadline Scheduler](#1511-deadline-scheduler)  
[15.12: Deadline Tunables](#1512-deadline-tunables)  
[Lab 15.1: Comparing I/O Schedulers](#lab-151-comparing-io-schedulers)  
[Paths and Commands](#paths-and-commands)    
  
### 15.3: Learning Objectives
----
By the end of this chapter, you should be able to:
  * Explain the importance of I/O scheduling and describe the conflicting requirements that need to be satisfied.
  * Delineate and contrast the options available under Linux.  
  * Understand how the **CFQ** (**C**ompletely **F**air **Q**ueue) and **Deadline** algorithms work.
  
### 15.4: I/O Scheduling
----
* the I/O scheduler provides the interface between the generic block layer and low-level physical device drivers  
? what does that mean?  
block layer: [A block layer introduction part 1: the bio layer](https://lwn.net/Articles/736534/)  
block device: file that refers to a device [What is a block device?](https://unix.stackexchange.com/questions/259193/what-is-a-block-device)  
running `$ ls -l /dev` gives results w/ a preceding **c** for character special files and a **b** for block special device  
* requirements (sometimes conflicting):
   * hardware access times should be minimized; leads to **elevator** scheme where requests are inserted in the queue according to physical location on disk
   * merge requests to get as big a contiguous region as possible
   * minimize latency
   * favor reads over writes; leads to better parallelism and system responsiveness (since write data is cached)
   * processes should share the I/O bandwidth in a fair, or at least consciously prioritized fashion; even it means some overall performance slowdown of the I/O layer, process throughput should not suffer inordinately
  
### 15.5: I/O Scheduler Choices
----
* different I/O schedulers for different systems (a large database server vs. a desktop system) based on needs, hardware, etc.
* can be selected at boot as in `linux ...  elevator=[cfq|deadline|noop]`
* choices:
   * CFQ
   * Deadline Scheduling
   * noop (a simple scheme)
* the default can be configured; modern systems use CFQ or Deadline
* `$ cat /sys/block/sda/queue/scheduler` shows which I/O schedulers are available on `/dev/sda`
* my system for sda and sr0 (cdrom): noop [deadline] cfq (deadline is normal for HDDs)
  
### 15.6: I/O Scheduling and SSD Devices
----
* SSDs do not require an elevator scheme and benefit from **wear leveling** (they have limited write/erase cycles)
* run `$ cat /sys/block/<device>/queue/rotational` to check whether the device is an SSD (1 == HDD)
  
### 15.7: Tunables and Switching the I/O Scheduler at Runtime
----
* to change I/O scheduler on the command line:
   * `$ cat /sys/block/sda/queue/scheduler`
     > noop deadline \[cfq\]
   * `$ echo noop > /sys/block/sda/queue/scheduler`
   * `$ cat /sys/block/sda/queue/scheduler`
     > \[noop\] deadline cfq
* to view tunables: `$ ls -l /sys/block/<device>/queue/iosched`
* for my system:
`$ ls -l /sys/block/sda/queue/iosched`
  > total 0  
  > -rw-r--r-- 1 root root 4096 Apr 12 22:14 fifo_batch  
  > -rw-r--r-- 1 root root 4096 Apr 12 22:14 front_merges  
  > -rw-r--r-- 1 root root 4096 Apr 12 22:14 read_expire  
  > -rw-r--r-- 1 root root 4096 Apr 12 22:14 write_expire  
  > -rw-r--r-- 1 root root 4096 Apr 12 22:14 writes_starved  
  
### 15.8: I/O Schedulers Demo
----
* `$ cd /sys/block`
* `$ ls -l`
* `$ cd sda`
* `$ cd queue`
* `$ cat rotational`
* `$ su`
* `$ ls -l rotational`
* `$ cat scheduler`
* `$ ls -l iosched/`
* `$ echo cfq > scheduler`
* `$ ls -l iosched/`
* `$ ls -l rotational
* `$ cat scheduler 
  
### 15.9: CFQ (Completey Fair Queue) Scheduler
----
* goal of equal spreading of I/O bandwidth among all processes submitting requests
* number of queues fixed at 64
* dequeuing of requests is done round robin style on all the queues, each one of which works in **FIFO** (**F**irst **I**n **F**irst **O**ut) order
  
### 15.10: CFQ Tunables
----
* in the examples below, the parameter HZ is a kernel-configured quantity, that corresponds to the number of jiffies per second, which the kernel uses as a coarse measure of time
* time units HZ/2 is 0.5 seconds and 5 * HZ is 5 seconds etc.
   * **quantum**: Maximum queue length in one round of service.  (Default = 4);
   * **queued**: Minimum request allocation per queue.  (Default = 8);
   * **fifo_expire_sync**: FIFO timeout for sync requests.  (Default = HZ/2);
   * **fifo_expire_async**: FIFO timeout for async requests.  (Default = 5  *  HZ);
   * **fifo_batch_expire**: Rate at which the FIFO's expire.  (Default = HZ/8);
   * **back_seek_max**: Maximum backwards seek, in KB. (Default = 16K);
   * **back_seek_penalty**: Penalty for a backwards seek.  (Default = 2).
  
### 15.11: Deadline Scheduler
----
* the Deadline I/O scheduler aggressively reorders requests with the simultaneous goals of improving overall performance and preventing large latencies for individual requests; i.e., limiting starvation
* with each and every request, the kernel associates a deadline
* read requests get higher priority than write requests
* five separate I/O queues are maintained:
   * two sorted lists are maintained, one for reading and one for writing, and arranged by starting block.
   * two **FIFO** lists are maintained, again one for reading and one for writing; these lists are sorted by submission time.
   * a fifth queue contains the requests that are to be shoveled to the device driver itself; this is called the dispatch queue.
* exactly how the requests are peeled off the first four queues and placed on the fifth (dispatch queue) is where the art of the algorithm is
  
### 15.12: Deadline Tunables
----
* available tunables for the **Deadline** scheduler:
   * **read_expire**: How long (in milliseconds) a read request is guaranteed to occur within. (Default = HZ/2 = 500). 
   * **write_expire**: How long (in milliseconds) a write request is guaranteed to occur within. (Default = 5 * HZ = 5000).
   * **writes_starved**: How many requests we should give preference to reads over writes. (Default = 2).
   * **fifo_batch**: How many requests should be moved from the sorted scheduler list to the dispatch queue, when the deadlines have expired. (Default = 16).
   * **front_merges**: Back merges are more common than front merges as a contiguous request usually continues to the next block. Setting this parameter to 0 disables front merges and can give a boost if you know they are unlikely to be needed. (Default = 1)
  
### Lab 15.1: Comparing I/O Schedulers
----
* `lab iosched.sh` does the following:
    * Cycle through the available I/O schedulers on a hard disk while doing a configurable number of parallel reads and writes of files of a configurable size.
    * Test reads and writes as separate steps.
    * When testing reads make sure you’re actually reading from disk and not from cached pages of memory; you can flush out the cache by doing: `$ echo 3 > /proc/sys/vm/drop_caches` before doing the reads. You can **cat** into `/dev/null` to avoid writing to disk.
    * Make sure all reads are complete before obtaining timing information; this can be done by issuing a **wait** command under the shell.
    * Test writes by simply copying a file (which will be in cached memory after the first read) multiple times simultaneously. To make sure you wait for all writes to complete before you get timing information you can issue a **sync** call.
* takes two arguments:
    * number of simultaneous reads and writes to perform
    * size (in MB) of each file
* This script must be run as root as it echoes values into the **/proc** and **/sys** directory trees.
* Compare the results you obtain using different I/O schedulers.
* Extra Credit: For additional exploring you might try changing some of the tunable parameters and see how results vary.
* `$ sudo ./ioscript.sh 8 100`
    ```
    Doing: 8 parallel read/writes on: 100 MB size files

    creating as needed random input files

    doing timings of parallel reads

    REAL    USER    SYS

    testing IOSCHED = noop
    [noop] deadline cfq 
    0.296   0.009   0.989
    testing IOSCHED = deadline
    noop [deadline] cfq 
    0.268   0.010   0.882
    testing IOSCHED = cfq
    noop deadline [cfq] 
    0.432   0.006   0.433

    doing timings of parallel writes

    REAL    USER    SYS

    testing IOSCHED = noop
    [noop] deadline cfq 
    8.388   0.027   1.303
    testing IOSCHED = deadline
    noop [deadline] cfq 
    12.927   0.036   2.031
    testing IOSCHED = cfq
    noop deadline [cfq] 
    24.573   0.023   1.337
    ```
  
### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
i/o | `/sys/block/sda/queue/scheduler` | I/O scheduling information | LFS201 15.5
i/o | `/sys/block/sda/queue/scheduler` | I/O scheduling information | LFS201 15.6
i/o | `/sys/block/<device>/queue/iosched` | contains tunables | LFS201 15.7
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
i/o | `$ cat /sys/block/sda/queue/scheduler` | shows which I/O schedulers are available on `/dev/sda` | LFS201 15.5
i/o | `$ cat /sys/block/<device>/queue/rotational` | check whether a device is an SSD | LFS201 15.6
i/o | `$ echo noop > /sys/block/sda/queue/scheduler` | change I/O scheduler to noop | LFS201 15.7
i/o | `$ echo 3 > /proc/sys/vm/drop_caches` | flush cached pages of memory | LFS201 Lab 15.1
