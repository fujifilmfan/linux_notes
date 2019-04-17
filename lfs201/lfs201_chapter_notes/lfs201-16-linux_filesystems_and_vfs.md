Chapter 16: Linux Filesystems and the VFS
-----------------------------------------

[](#)


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

### 16.11: Filesystems Demo
----

### 16.12: Journalling Filesystems
----

### 16.13: Special Filesystems
----

### Lab 16.1: The tmpfs Special Filesystem
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
