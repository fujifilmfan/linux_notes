Chapter 13: Memory: Monitoring Usage and Tuning
-----------------------------------------------
[13.3: Learning Objectives](#133-learning-objectives)
[13.4: Memory Tuning Considerations](#134-memory-tuning-considerations)
[13.5: Memory Monitoring Tools](#135-memory-monitoring-tools)
[13.6: /proc/sys/vm](#136-procsysvm)
[13.7: vmstat](#137-vmstat)
[13.8: /proc/meminfo](#138-procmeminfo)
[13.9: OOM Killer](#139-oom-killer)
[13.10: swap and OOM Killer Demo](#1310-swap-and-oom-killer-demo)
[Lab 13.1: Invoking the OOM-Killer](#lab-131-invoking-the-oom-killer)
[Paths and Commands](#paths-and-commands)  

### 13.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* List the primary (inter-related) considerations and tasks involved in memory tuning.
* Use entries in `/proc/sys/vm`, and decipher `/proc/meminfo`.
* Use **vmstat** to display information about memory, paging, I/O, processor activity, and processes' memory consumption.
* Understand how the **OOM-killer** decides when to take action and selects which processes should be exterminated to open up some memory.

### 13.4: Memory Tuning Considerations
----
* most memory is used to cached contents of files on disk, so closely related to I/O throughput
* tweak parameters in `/proc/sys/vm` one at a time and look for effects
* primary (inter-related) tasks:
    * controlling flushing parameters, i.e., how many pages are allowed to be **dirty** and how often they are flushed out to disk
    * controlling **swap** behavior, i.e., how much pages that reflect file contents are allowed to remain in memory, as opposed to those that need to be swapped out as they have no other backing store
    * controlling how much memory **overcommission** is allowed, since many programs never need the full amount of memory they request, particularly because of **copy on write** (**COW**) techniques
* what works in one situation might be far from optimal in others
  
### 13.5: Memory Monitoring Tools
----

**Memory Monitoring Utilities**
  
Utitity | Purpose                                                               | Package
------- | -------                                                               | -------
free    | Brief summary of memory usage                                         | procps
vmstat  | Detailed virtual memory statistics and block I/O, dynamically updated | procps
pmap    | Process memory map                                                    | procps

* `$ free -m` shows memory usage (-m option to show the amounts in megabytes)

### 13.6: /proc/sys/vm
----
* the `/proc/sys/vm` directory contains many tunable knobs to control the **Virtual Memory** system
* they can be changed by writing to the entry or using the **sysctl** utility
* values can be set at boot time by modifying `/etc/sysctl.conf`

**`/proc/sys/vm` Entries**

Entry                      | Purpose
-----                      | -------
admin_reserve_kbytes       | Amount of free memory reserved for privileged users
block_dump                 | Enables block I/O debugging
compact_memory             | Turns on or off memory compaction (essentially defragmentation) when configured into the kernel
dirty_background_bytes     | Dirty memory threshold that triggers writing uncommitted pages to disk
dirty_background_ratio     | Percentage of total pages at which kernel will start writing dirty data out to disk
dirty_bytes                | The amount of dirty memory a process needs to initiate writing on its own
dirty_expire_centisecs     | When dirty data is old enough to be written out in hundredths of a second)
dirty_ratio                | Percentage of pages at which a process writing will start writing out dirty data on its own
dirty_writeback_centisecs  | Interval in which periodic writeback daemons wake up to flush. If set to zero, there is no automatic periodic writeback
drop_caches                | Echo 1 to free page cache, 2 to free dentry and inode caches, 3 to free all. Note only clean cached pages are dropped; do sync first to flush dirty pages
extfrag_threshold          | Controls when the kernel should compact memory
hugepages_treat_as_movable | Used to toggle how huge pages are treated
hugetlb_shm_group          | Sets a group ID that can be used for System V huge pages
laptop_mode                | Can control a number of features to save power on laptops
legacy_va_layout           | Use old layout (2.4 kernel) for how memory mappings are displayed
lowmen_reserve_ratio       | Controls how much low memory is reserved for pages that can only be there; i.e., pages which can go in high memory instead will do so. Only important on 32-bit systems with high memory
max_map_count              | Maximum number of memory mapped areas a process may have. The default is 64 K
min_free_kbytes            | Minimum free memory that must be reserved in each zone
mmap_min_addr              | How much address space a user process cannot memory map. Used for security purposes, to avoid bugs where accidental kernel null dereferences can overwrite the first pages used in an application
nr_hugepages               | Minimum size of hugepage pool
nr_pdflush_hugepages       | Maximum size of the hugepage pool = nr_hugepages *nr_overcommit_hugepages 
nr_pdflush_threads         | Current number of pdflush threads; not writeable
oom_dump_tasks             | If enabled, dump information produced when oom-killer cuts in
oom_kill_allocating_task   | If set, the oom-killer kills the task that triggered the out of memory situation, rather than trying to select the best one
overcommit_kbytes          | One can set either overcommit_ratio or this entry, but not both
overcommit_memory          | If 0, kernel estimates how much free memory is left when allocations are made. If 1, permits all allocations until memory actually does run out. If 2, prevents any overcommission
overcommit_ratio           | If overcommit_memory = 2 memory commission can reach swap plus this percentage of RAM
page-cluster               | Number of pages that can be written to swap at once, as a power of two. Default is 3 (which means 8 pages)
panic_on_oom               | Enable system to crash on an out of memory situation
percpu_pagelist_fraction   | Fraction of pages allocated for each cpu in each zone for hot-pluggable CPU machines
scan_unevictable_pages     | If written to, system will scan and try to move pages to try and make them reclaimable
stat_interval              | How often vm statistics are updated (default 1 second) by vmstat
swappiness                 | How aggressively should the kernel swap
user_reserve_kbytes        | If overcommit_memory is set to 2 this sets how low the user can draw memory resources
vfs_cache_pressure         | How aggressively the kernel should reclaim memory used for inode and dentry cache. Default is 100; if 0 this memory is never reclaimed due to memory pressure

### 13.7: vmstat
----
* **vmstat** displays information about memory, paging, I/O, processor activity, and processes
* general use: `$ vmstat <options> <delay> <count>`
   * report is repeated after <interval> seconds a total of <count> times
   * if no count is specified, it will report statistics until killed by a signal, like CTRL-C
* example use: `$ vmstat -S m -a 2 4`
   * -S m shows memory statistics in MB instead of KB
   * -a option shows information about active and inactive memory
* **active memory pages** are those which have been recently used
   * **clean** (disk contents are up to date)
   * **dirty** (need to be flushed to disk eventually)
* **inactive memory pages** have not been recently used and are more likely to be clean and are released sooner under memory pressure
* `$ vmstat -s -S m` will show a table of memory statistics and certain event counters
  
**vmstat Fields**

Field     | Subfield | Meaning 
-----     | -------- | -------
Processes | r        | Number of processes waiting to be scheduled in
Processes | b        | Number of processes in uninterruptible sleep 
memory    | swpd     | Virtual memory used (KB) 
memory    | free     | Free (idle) memory (KB) 
memory    | buff     | Buffer memory (KB) 
memory    | cache    | Cached memory (KB) 
swap      | si       | Memory swapped in (KB) 
swap      | so       | Memory swapped out (KB) 
I/O       | bi       | Blocks read from devices (blocks/sec) 
I/O       | bo       | Blocks written to devices (blocks/sec) 
system    | in       | Interrupts/second 
system    | cs       | Context switches/second 
CPU       | us       | CPU time running user code (percentage) 
CPU       | sy       | CPU time running kernel (system) code (percentage)
CPU       | id       | CPU time idle (percentage)
CPU       | wa       | Time waiting for I/O (percentage) 
CPU       | st       | Time "stolen" from virtual machine (percentage) 

* `$ vmstat -d` will show a table of disk statistics

**vmstat Disk Fields**

Field  | Subfield | Meaning
-----  | -------- | -------
reads  | total    | Total reads completed successfully
reads  | merged   | Grouped reads (resulting in one I/O)
reads  | ms       | Milliseconds spent reading
writes | total    | Total writes completed successfully
writes | merged   | Grouped writes (resulting in one I/O)
writes | ms       | Milliseconds spent writing
I/O    | cur      | I/O in progress
I/O    | sec      | seconds spent for I/O
  
* use the -p option to get quick statistics on one partition: `$ vmstat -p /dev/sdb1 2 4`
  
### 13.8: /proc/meminfo
----
* `/proc/meminfo` contains a summary of memory statistics

**`/proc/meminfo` Entries**

Entry           | Meaning
-----           | -------
MemTotal        | Total usable RAM (physical minus some kernel reserved memory)
MemFree         | Free memory in both low and high zones
Buffers         | Memory used for temporary block I/O storage
Cached          | Page cache memory, mostly for file I/O
SwapCached      | Memory that was swapped back in but is still in the swap file
Active          | Recently used memory, not to be claimed first
Inactive        | Memory not recently used, more eligible for reclamation
Active(anon)    | Active memory for anonymous pages
Inactive(anon)  | Inactive memory for anonymous pages
Active(file)    | Active memory for file-backed pages
Inactive(file)  | Inactive memory for file-backed pages
Unevictable     | Pages which can not be swapped out of memory or released
Mlocked         | Pages which are locked in memory
SwapTotal       | Total swap space available
SwapFree        | Swap space not being used
Dirty           | Memory which needs to be written back to disk
Writeback       | Memory actively being written back to disk
AnonPages       | Non-file back pages in cache
Mapped          | Memory mapped pages, such as libraries
Shmem           | Pages used for shared memory
Slab            | Memory used in slabs
SReclaimable    | Cached memory in slabs that can be reclaimed
SUnreclaim      | Memory in slabs that can't be reclaimed
KernelStack     | Memory used in kernel stack
PageTables      | Memory being used by page table structures
Bounce          | Memory used for block device bounce buffers
WritebackTmp    | Memory used by FUSE filesystems for writeback buffers
CommitLimit     | Total memory available to be used, including overcommission
Committed_AS    | Total memory presently allocated, whether or not it is used
VmallocTotal    | Total memory available in kernel for vmalloc allocations
VmallocUsed     | Memory actually used by vmalloc allocations
VmallocChunk    | Largest possible contiguous vmalloc area
HugePages_Total | Total size of the huge page pool
HugePages_Free  | Huge pages that are not yet allocated
HugePages_Rsvd  | Huge pages that have been reserved, but not yet used
HugePages_Surp  | Huge pages that are surplus, used for overcommission
Hugepagesize    | Size of a huge page


### 13.9: OOM Killer
----
* ways to deal with memory pressure
   * simplest: permit memory allocations to succeed as long as free memory is available and then fail when all memory is exhausted
   * second simplest: use **swap** space on disk
      * total memory = RAM + swap
      * the hard part of this is to figure out which pages of memory to swap out when pressure demands
      * in this approach, once the swap space itself is filled, requests for new memory must fail
   * Linux: permits system to overcommit memory (for pages dedicated to user processes) since many or even most processes to no use all requested memory
      * example 1: A program allocates a 1 MB buffer and then uses only a few pages of the memory.
      * example 2: Every time a child process is forked, it receives a copy of the entire memory space of the parent. Because Linux uses the COW (copy on write) technique, unless one of the processes modifies memory, no actual copy needs be made. However, the kernel has to assume that the copy might need to be done.
* modify overcommission settings in `/proc/sys/vm/overcommit_memory`
   * 0: (default) Permit overcommission, but refuse obvious overcommits, and give root users somewhat more memory allocation than normal users.
   * 1: All memory requests are allowed to overcommit.
   * 2: Turn off overcommission. Memory requests will fail when the total memory commit reaches the size of the swap space plus a configurable percentage (50 by default) of RAM. This factor is modified changing `/proc/sys/vm/overcommit_ratio`.
* my VM is set to 0, and the ratio is set to 50 in `/proc/sys/vm/overcommit_ratio`   
* if available memory is exhausted, Linux invokes the **OOM**-killer (**O**ut **O**f **M**emory) to decide which process(es) to kill to free some memory
   * funny article on this: [Re: [PATCH] oom_pardon, aka don't kill my xlock](https://lwn.net/Articles/104185/)
   * to decide, a **badness** value is calculated
   * **badness** value found at `/proc/<pid>/oom_score`
   * `/proc/<pid>/oom_adj` shows number of bits to adjust score if two entries are in same directory
      * normal users can only increase the badness
      * superusers can decrease the badness
      * **oom_adj** is deprecated
   * `/proc/<pid>/oom_score_adj` is used to directly adjust the point value

### 13.10: swap and OOM Killer Demo
----
* `$ free -m`
* `$ gcc -o wastemem wastemem.c` compiles wastemem program that can be used to tie up memory
* `$ ./wastemem 3000`
* `$ sudo swapoff -a` to stop using swap
* `$ dmesg to see what happened`

### Lab 13.1: Invoking the OOM Killer
----
My solutions:  
* Examine what swap partitions and files are present on your system by examining `/proc/swaps`.
    > Filename				Type		Size	Used	Priority  
    > /dev/sda2                               partition	975868	275968	-2  
* Turn off all swap with the command `$ sudo /sbin/swapoff -a`
    * that took at least 10 seconds
* Make sure you turn it back on later, when we are done, with `$ sudo /sbin/swapon -a`
* Now we are going to put the system under increasing memory pressure. One way to do this is to exploit the stress program we installed earlier, running it with arguments such as: `$ stress -m 8 -t 10s` which would keep 2 GB busy for 10 seconds.
* You should see the OOM (Out of Memory) killer swoop in and try to kill processes in a struggle to stay alive. You can see what is going on by running dmesg or monitoring `/var/log/messages` or `/var/log/syslog`, or through graphical interfaces that expose the system logs.  Who gets clobbered first?  
        ```
        stress: info: [49348] dispatching hogs: 0 cpu, 0 io, 8 vm, 0 hdd
        stress: FAIL: [49348] (415) <-- worker 49355 got signal 9
        stress: WARN: [49348] (417) now reaping child worker processes
        stress: FAIL: [49348] (451) failed run completed in 5s
        ```
    * also, VS Studio Code crashed :)
    * `$ dmesg`
        ```
        [56395.333419] [49251]  1000 49251   167127     8767     235        0             0 code
        [56395.333421] [49345]     0 49345    26988       19      11        0             0 sleep
        [56395.333423] [49348]  1000 49348     1827       26       8        0             0 stress
        [56395.333425] [49349]  1000 49349    67364    39885      87        0             0 stress
        [56395.333426] [49350]  1000 49350    67364    32645      73        0             0 stress
        [56395.333428] [49351]  1000 49351    67364    30695      69        0             0 stress
        [56395.333430] [49352]  1000 49352    67364    34984      78        0             0 stress
        [56395.333432] [49353]  1000 49353    67364    41125      90        0             0 stress
        [56395.333434] [49354]  1000 49354    67364    41998      91        0             0 stress
        [56395.333435] [49355]  1000 49355    67364    49621     106        0             0 stress
        [56395.333437] [49356]  1000 49356    67364    34589      77        0             0 stress
        [56395.333438] Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        [56395.333444] Killed process 33731 (Web Content) total-vm:1990252kB, anon-rss:106448kB, file-rss:0kB, shmem-rss:54348kB
        [56395.334581] Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        [56395.334586] Killed process 20646 (Web Content) total-vm:2099496kB, anon-rss:103156kB, file-rss:0kB, shmem-rss:16kB
        [56395.336087] Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        [56395.336092] Killed process 20660 (JS Watchdog) total-vm:2099496kB, anon-rss:104448kB, file-rss:0kB, shmem-rss:16kB
        [56395.336471] Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        [56395.336475] Killed process 19384 (firefox) total-vm:3014684kB, anon-rss:200460kB, file-rss:0kB, shmem-rss:75096kB
        [56396.798493] Out of memory: Kill process 49355 (stress) score 67 or sacrifice child
        [56396.798497] Killed process 49355 (stress) total-vm:269456kB, anon-rss:258384kB, file-rss:4kB, shmem-rss:0kB
        ```
    * `$ sudo tail -n /var/log/messages`
        ```
        25 13:25:03 centos kernel: [49348]  1000 49348     1827       26       8        0             0 stress
        May 25 13:25:03 centos kernel: [49349]  1000 49349    67364    39885      87        0             0 stress
        May 25 13:25:03 centos kernel: [49350]  1000 49350    67364    32645      73        0             0 stress
        May 25 13:25:03 centos kernel: [49351]  1000 49351    67364    30695      69        0             0 stress
        May 25 13:25:03 centos kernel: [49352]  1000 49352    67364    34984      78        0             0 stress
        May 25 13:25:03 centos kernel: [49353]  1000 49353    67364    41125      90        0             0 stress
        May 25 13:25:03 centos kernel: [49354]  1000 49354    67364    41998      91        0             0 stress
        May 25 13:25:03 centos kernel: [49355]  1000 49355    67364    49621     106        0             0 stress
        May 25 13:25:03 centos kernel: [49356]  1000 49356    67364    34589      77        0             0 stress
        May 25 13:25:03 centos kernel: Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        May 25 13:25:03 centos kernel: Killed process 33731 (Web Content) total-vm:1990252kB, anon-rss:106448kB, file-rss:0kB, shmem-rss:54348kB
        May 25 13:25:03 centos kernel: Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        May 25 13:25:03 centos kernel: Killed process 20646 (Web Content) total-vm:2099496kB, anon-rss:103156kB, file-rss:0kB, shmem-rss:16kB
        May 25 13:25:03 centos kernel: Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        May 25 13:25:03 centos kernel: Killed process 20660 (JS Watchdog) total-vm:2099496kB, anon-rss:104448kB, file-rss:0kB, shmem-rss:16kB
        May 25 13:25:03 centos kernel: Out of memory: Kill process 19384 (firefox) score 71 or sacrifice child
        May 25 13:25:03 centos kernel: Killed process 19384 (firefox) total-vm:3014684kB, anon-rss:200460kB, file-rss:0kB, shmem-rss:75096kB
        May 25 13:25:03 centos code.desktop: #033[91m[main 2019-05-25T20:25:03.706Z]#033[0m [VS Code]: render process crashed!
        May 25 13:25:04 centos kernel: Out of memory: Kill process 49355 (stress) score 67 or sacrifice child
        May 25 13:25:04 centos kernel: Killed process 49355 (stress) total-vm:269456kB, anon-rss:258384kB, file-rss:4kB, shmem-rss:0kB
        May 25 13:25:04 centos journal: GtkDialog mapped without a transient parent. This is discouraged.
        ```
    * Firefox and VS Studio Code crashed
    * I don't have `/var/log/syslog`

### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
monitoring, memory | `/proc/sys/vm` | contains tunable knobs to control the **Virtual Memory** system | LFS201 13.6
monitoring, memory | `Documentation/sysctl/vm.txt` | documentation in the kernel source for `/proc/sys/vm` | LFS201 13.6
monitoring, system | `/etc/sysctl.conf` | configuration for values set at boot time | LFS201 13.6
monitoring, system | `/proc/meminfo` | contains a summary of memory statistics | LFS201 13.8
monitoring, system | `/proc/sys/vm/overcommit_memory` | overcommit memory settings | LFS201 13.9
monitoring, system | `/proc/sys/vm/overcommit_ratio` | change percentage of RAM available before memory requests fail | LFS201 13.9
monitoring, system | `/proc/<pid>/oom_score` | contains **badness* value | LFS201 13.9
monitoring, system | `/proc/<pid>/oom_adj` | shows number of bits to adjust badness score if two entries are in same directory; deprecated | LFS201 13.9
monitoring, system | `/proc/<pid>/oom_score_adj` | used to directly adjust the badness point value | LFS201 13.9
monitoring, system | `/proc/swaps` | shows swap partitions and files on the system | LFS201 Lab 13.1
monitoring, system | `/var/log/messages` | contains system messages | LFS201 Lab 13.1
monitoring, system | `/var/log/syslog` | contains system messages | LFS201 Lab 13.1

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
monitoring, memory | `$ free -m` | shows summary of memory usage | LFS201 13.5
monitoring, memory | `$ vmstat <options> <delay> <count>` | show detailed information about memory, I/O, and other activities | LFS201 13.7
monitoring, memory | `$ vmstat -S m -a 2 4` | show report at a 2 s interval 4 times | LFS201 13.7
monitoring, memory | `$ vmstat -s -S m` | use -s option to see a table of memory statistics and certain event counters | LFS201 13.7
monitoring, memory | `$ vmstat -d` | use -d option to see a table of disk statistics | LFS201 13.7 
monitoring, memory | `$ vmstat -p /dev/sdb1 2 4` | use -p option to get quick stats on one partition (sda1 or sda2 in my case) | LFS201 13.7
monitoring, system | `$ sudo /sbin/swapoff -a` | turn off all swap | LFS201 Lab 13.1
monitoring, system | `$ sudo /sbin/swapon -a` | turn on all swap | LFS201 Lab 13.1
monitoring, system | `$ stress -m 8 -t 10s` | keep 2 GB memory busy for 10 seconds | LFS201 Lab 13.1
monitoring, system | `$ dmesg` | print or control the kernel ring buffer (view messages) | LFS201 Lab 13.1
