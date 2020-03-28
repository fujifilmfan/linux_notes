Chapter 16: Linux Filesystems and the VFS
-----------------------------------------

[16.3: Learning Objectives](#163-learning-objectives)  
[16.4: Filesystem Basics](#164-filesystem-basics)  
[16.5: Inodes](#165-inodes)  
[16.6: Hard and Soft Links](#166-hard-and-soft-links)  
[16.7: Filesystem Tree Organization](#167-filesystem-tree-rganization)  
[16.8: Virtual File System (VFS)](#168-virtual-file-system-vfs)  
[16.9: Available Filesystems](#169-available-filesystems)  
[16.10: Current Filesystem Types](#1610-current-filesystem-types)  
[16.11: Filesystems Demo](#1611-filesystems-demo)  
[16.12: Journalling Filesystems](#1612-journalling-filesystems)  
[16.13: Special Filesystems](#1613-special-filesystems)  
[Lab 16.1: The tmpfs Special Filesystem](#lab-161-the-tmpfs-special-filesystem)  
[Paths and Commands](#paths-and-commands)  
  
### 16.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the basic filesystem organization.
* Understand the role of the VFS.
* Know what filesystems are available in Linux and which ones can be used on your actual system.
* Grasp why journalling filesystems represent significant advances.
* Discuss the use of special filesystems in Linux.

### 16.4: Filesystem Basics
----
* applications read and write files and do not deal with physical locations on hardware
* files and their names are an abstraction camouflaging the physical I/O layer
* local filesystems are usually on a disk partition
   * physical partition
   * logical partition controlled by **Logical Volume Manager** (LVM)
   * network
  
### 16.5: Inodes
----
* an inode is a data structure on disk that describes and stores file attributes, including location
   * permissions
   * user and group ownership
   * size
   * timestamps (nanosecond): last access, last modification, change
   * **not** file names (those are in the **directory** file)
* every file has its own inode
  
### 16.6: Hard and Soft Links
----
* **directory files** associate inodes and file names; eash association is known as a **link**
* two types:
    * **hard** links point to an inode
    * **soft** (or **symbolic**) links point to a file name which has an associated inode
* because two or more directory entries can point to the same inode, a file can be known by multiple names, but it only has one indoe
  
### 16.7: Filesystem Tree Organization
----
* filesystems are joined at **mount points**, including **virtual pseudo filesystems** like `/proc`, `/sys`, and `/dev`
  
### 16.8: Virtual File System (VFS)
----
* applications interact with the VFS, which then translates all the I/O system calls into code relevant to the particular actual filesystem
* this way, applications do not need to know about the filesystem and allow Linux to work with more varieties of filesystems than any other OS
* **vfat** does not have distinct read/write/execute permissions for the owner/group/world fields, so the translation is trickier
  
### 16.9: Available Filesystems
----
example filesystems supported by Linux:
* ext4: Linux native filesystem (and earlier ext2 and ext3)
* XFS: A high-performance filesystem originally created by SGI
* JFS: A high-performance filesystem originally created by IBM
* Windows-natives: FAT12, FAT16, FAT32, VFAT, NTFS
* Pseudo-filesystems resident only in memory, including proc, sysfs, devfs, debugfs
* Network filesystems such as NFS, coda, afs
  
### 16.10: Current Filesystem Types
----
* `$ cat /proc/filesystems` shows currently registered filesystem types
* the 'nodev' ones are pseudo-filesystems (see Demo video)

### 16.11: Filesystems Demo
----
* mount an xfs filesystem using the loopback mechanism and create a container for the filesystem:
   * `$ dd if=/dev/zero of=junk bs=1M count=512` ('if' seems to be input file and 'of' output file)
   * makes a 512MB file of zeros
   * `$ sudo /sbin/mkfs.xfs junk` (maybe 'mkfs' stands for MaKe FileSystem)
   * `$ sudo mount junk /mnt` no need for '-o loop'
   * `$ df -h`
   * `$ cat /proc/filesystems` has xfs
   * `$ lsmod | less` shows the xfs module loaded
* `/dev/zero` is a special file in Unix-like operating systems that provides as many null characters (ASCII NUL, 0x00) as are read from it

### 16.12: Journalling Filesystems
----
* journalling filesystems recover from system crashes or ungraceful shutdowns with little or no corruption and do so rapidly
* operations are grouped into **transactions**
* transactions must be completed without error, **atomically**; otherwise, filesystem is not changed
* transactions are logged to a log file; examine last transaction when an error occurs
* freely available, journalling filesystems under Linux:
   * ext3 was an extension of the earlier non-journalling ext2 filesystem
   * ext4 is a vastly enhanced outgrowth of ext3. Features include extents, 48-bit block numbers, and up to 16TB size. Most Linux distributions have used ext4 as the default filesystem for quite a few years
   * reiserfs was the first journalling implementation used in Linux, but lost its leadership and further development was abandoned
   * JFS was originally a product of IBM and was ported from IBM's AIX operating system
   * XFS was originally a product of SGI, and was ported from SGI's IRIX operating systems; RHEL 7 has adopted XFS as its default filesystem
   * btrfs is the newest of the journalling filesystems and is still under rapid development
  
### 16.13: Special Filesystems
----
* some special filesystems interact with the kernel without a mount point; user applications don't interact with them

**Special Filesystems**

Filesystem  | Mount Point                      | Purpose
----------  | -----------                      | -------
rootfs      | None                             | During kernel load, provides an empty root directory
hugetlbfs   | Anywhere                         | Provides extended page access (2 or 4 MB on X86)
bdev        | None                             | Used for block devices
proc        | /proc                            | Pseudo filesystem access to many kernel structures and subsystems
sockfs      | None                             | Used by BSD Sockets
tmpfs       | Anywhere                         | RAM disk with swapping, re-sizing
shm         | None                             | Used by System V IPC Shared Memory
pipefs      | None                             | Used for pipes
binfmt_misc | Anywhere                         | Used by various executable formats
devpts      | /dev/pts                         | Used by Unix98 pseudo-terminals
usbfs       | /proc/bus/usb                    | Used by USB sub-system for dynamical devices
sysfs       | /sys (or elsewhere)              | Used as a device tree
debugfs     | /sys/kernel/debug (or elsewhere) | Used for simple debugging file access
  
### Lab 16.1: The tmpfs Special Filesystem
----
* **tmpfs** is one of many special filesystems used under Linux. Some of these are not really used as filesystems, but just take advantage of the filesystem abstraction. However, **tmpfs** is a real filesystem that applications can do I/O on.
* Essentially, **tmpfs** functions as a **ramdisk**; it resides purely in memory. But it has some nice properties that old-fashioned conventional ramdisk implementations did not have:
1. The filesystem adjusts its size (and thus the memory that is used) dynamically; it starts at zero and expands as necessary up to the maximum size it was mounted with.
2. If your RAM gets exhausted, **tmpfs** can utilize swap space. (You still can’t try to put more in the filesystem than its maximum capacity allows, however.)
3. **tmpfs** does not require having a normal filesystem placed in it, such as **ext3** or **vfat**; it has its own methods for dealing with files and I/O that are aware that it is really just space in memory (it is not actually a block device), and as such are optimized for speed.
* Thus there is no need to pre-format the filesystem with a **mkfs** command; you merely just have to mount it and use it.
* Mount a new instance of **tmpfs** anywhere on your directory structure with a command like:
    * `$ sudo mkdir /mnt/tmpfs`
    * `$ sudo mount -t tmpfs none /mnt/tmpfs` **-t** for the filesystem type
* See how much space the filesystem has been given and how much it is using `$ df -h /mnt/tmpfs`
    > Filesystem      Size  Used Avail Use% Mounted on  
    > none            1.9G     0  1.9G   0% /mnt/tmpfs  
* You should see it has been allotted a default value of half of your RAM; however, the usage is zero, and will only start to grow as you place files on `/mnt/tmpfs`.
* You could change the allotted size as a mount option as in: `$ sudo mount -t tmpfs -o size=1G none /mnt/tmpfs`
    > Filesystem      Size  Used Avail Use% Mounted on  
    > none            1.0G     0  1.0G   0% /mnt/tmpfs  
* You might try filling it up until you reach full capacity and see what happens. Do not forget to unmount when you are done with `$ sudo umount /mnt/tmpfs`
* Virtually all modern Linux distributions mount an instance of **tmpfs** at `/dev/shm`: `$ df -h /dev/shm`
    > Filesystem      Size  Used Avail Use% Mounted on  
    > tmpfs           1.9G   70M  1.8G   4% /dev/shm  
    * Many applications use this such as when they are using POSIX shared memory as an inter-process communication mechanism. Any user can create, read and write files in `/dev/shm`, so it is a good place to create temporary files in memory.
    * Create some files in `/dev/shm` and note how the filesystem is filling up with **df**.
* In addition, many distributions mount multiple instances of **tmpfs**; for example, on a RHEL 7 system: `$ df -h | grep tmpfs`
    ```
    devtmpfs        1.9G     0  1.9G   0% /dev
    tmpfs           1.9G   70M  1.8G   4% /dev/shm
    tmpfs           1.9G  131M  1.8G   7% /run
    tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
    tmpfs           378M  4.0K  378M   1% /run/user/42
    tmpfs           378M   52K  378M   1% /run/user/1000
    none            1.9G     0  1.9G   0% /mnt/tmpfs
    ```
    * "Notice this was run on a system with 8 GB of ram, so clearly you can’t have all these tmpfs filesystems actually using the 4 GB they have each been allotted!"
  
### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem | `/proc/filesystems` | currently registered filesystems | LFS201 16.10
filesystem | `/dev/zero` | special file in Unix-like operating systems that provides null characters | LFS201 16.10
filesystem | `/dev/shm` | most modern Linux distributions mount an instance of **tmpfs** here | LFS201 Lab 16.1
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem | `$ cat /proc/filesystems` | shows currently registered filessytem types | LFS201 16.10
filesystem | `$ dd if=/dev/zero of=junk bs=1M count=512` | create 512MB file of zeros | LFS201 16.10
filesystem | `$ sudo /sbin/mkfs.xfs junk` | create xfs filesystem | LFS201 16.10
filesystem | `$ sudo mount junk /mnt` | mount filesystem | LFS201 16.10
filesystem | `$ lsmod | less` | shows mounted filesystems | LFS201 16.10
filesystem | `$ df -h` | display filesystem disk space usage in human-readable form | LFS201 16.10
filesystem | `$ sudo mkdir /mnt/tmpfs` | create a **tmpfs** filesystem | LFS201 Lab 16.1
filesystem | `$ sudo mount -t tmpfs none /mnt/tmpfs` | mount a new **tmpfs** filesystem | LFS201 Lab 16.1
filesystem | `$ df -h /mnt/tmpfs` | see how much space the filesystem has been given and how much it is using
filesystem | `$ sudo mount -t tmpfs -o size=1G none /mnt/tmpfs` | change the allotted size of **tmpfs** as a mount option | LFS201 Lab 16.1
filesystem | `$ sudo umount /mnt/tmpfs` | unmount the **tmpfs** filesystem | LFS201 Lab 16.1
filesystem | `$ df -h /dev/shm` | see how much space the filesystem has been given and how much it is using | LFS201 Lab 16.1
filesystem | `$ df -h | grep tmpfs` | many distributions mount multiple instances of **tmpfs** | LFS201 Lab 16.1
