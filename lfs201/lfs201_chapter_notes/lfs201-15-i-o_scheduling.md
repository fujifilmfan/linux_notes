Chapter 15: I/O Scheduling
--------------------------

[15.3: Learning Objectives](#153-learning-objectives)  
[15.4: I/O Scheduling](#154-io-scheduling)  
[15.5: I/O Scheduler Choices](#155-io-scheduler-choices)  
[15.6: I/O Scheduling and SSD Devices](#156-io-scheduling-and-ssd-devices)  
[15.7: Tunables and Switching the I/O Scheduler at Runtime](#157-tunables-and-switching-the-io-scheduler-at-runtime)  
[15.8: I/O Schedulers Demo](#158-io-schedulers-demo)  
[15.9: CFQ (Completey Fair Queue) Scheduler](#15.9: CFQ (Completey Fair Queue) Scheduler)  
[15.10: CFQ Tunables](#15.10: CFQ Tunables)  
[15.11: Deadline Scheduler](#15.11: Deadline Scheduler)  
[15.12: Deadline Tunables](#15.12: Deadline Tunables)  
[Lab 15.1: Comparing I/O Schedulers](#Lab 15.1: Comparing I/O Schedulers)  
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

### 15.10: CFQ Tunables
----

### 15.11: Deadline Scheduler
----

### 15.12: Deadline Tunables
----

### Lab 15.1: Comparing I/O Schedulers
----

  

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