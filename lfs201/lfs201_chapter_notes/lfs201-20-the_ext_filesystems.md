Chapter 20: The ext2/ext3/ext4 Filesystems
------------------------------------------

[20.3: Learning Objectives](#203-learning-objectives)  
[20.4: Ext4 History and Basics](#204-ext4-history-and-basics)  
[20.5: Ext4 Filesystem Features](#205-ext4-filesystem-features)  
[20.6: Ext4 Filesystem Enhancements](#206-ext4-filesystem-enhancements)  
[20.7: Ext4 Layout](#207-ext4-layout)  
[20.8: Superblock Information](#208-superblock-information)  
[20.9: Block Groups](#209-block-groups)  
[20.10: Data Blocks and Inodes](#2010-data-blocks-and-inodes)  
[20.11: dumpe2fs](#2011-dumpe2fs)  
[20.12: tune2fs](#2012-tune2fs)  
[Lab 20.1: Defragmentation](#lab-201-defragmentation)  
[Lab 20.2: Modifying Filesystem Parameters with tune2fs](#lab-202-modifying-filesystem-parameters-with-tune2fs)  
[Paths and Commands](#paths-and-commands)  
  
### 20.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Describe the main features of the **ext4** filesystem and how it is laid out on disk.
* Explain the concepts of **block groups**, **superblock**, **data blocks**, and **inodes**.
* Use the **dumpe2fs** and **tune2fs** utilities.
* List the **ext4** filesystem enhancements.

### 20.4: Ext4 History and Basics
----
* **ext2** was Linux's original native variety and is still available on all Linux systems
* **ext3** was the first **journalling** extension of **ext2**
* **ext4** had enhancements including the use of **extents** (a group of contiguous blocks) for large files
* **ext4** is the default on most enterprise Linux distributions, but RHEL 7 uses **XFS** as the default
  
### 20.5: Ext4 Filesystem Features
----
* VFS and ext2/3/4 filesystems have each been designed with the other in mind
* **block size** is selected when the filesystem is built, usually 4096 bytes
* number of **inodes** can also be tuned
* **inode reservation** consists of reserving several inodes when a directory is created, expecting them to be used in the future; this improves performance b/c inodes are already allocated when new files are created
* if the pathname of a symbolic link is less than 60 characters, a so-called fast symbolic link is created, which is stored directly in the inode, obviating the need to read a data block
  
### 20.6: Ext4 Filesystem Enhancements
----
The **ext4** filesystem:
* is backwards-compatible with ext3 and ext2
* increases max filesystem size to 1 EB, max file size to 16 TB
* has no limit to number of subdirectories (32K in ext3)
* splits large files into the largest possible extents
* uses **multiblock allocation**
* can **pre-allocate** disk space for a file; usually guaranteed and contiguous
* uses **allocate-on-flush** which delay block allocation until data is written to disk
* uses fast **fsck**
* uses **checksums** for the journal
* uses improved timestamps measured in nanoseconds
  
### 20.7: Ext4 Layout
----
* fields written to disk in **little-endian** order
* disk blocks partitioned into **block groups** with inode and data blocks stored adjacently
* other terms in this section: boot block, **GDT** (Group Descriptor Table) blocks, data block bitmap, inode bitmap, inode table, data blocks, superblock
* filesystem **superblock** contains bit-fields which are used to ascertain whether or not the filesystem requires checking when it is first mounted during system initialization; status can be:
   * **clean**: filesystem cleanly unmounted previously
   * **dirty**: usually means mounted
   * **unknown**: not cleanly unmounted, such as when there is a system crash
* **fsck** will be run in the latter two cases
  
### 20.8: Superblock Information
----
The **superblock** (redundantly stored in several block groups) contains global information about the filesystem, including:
* Mount count and maximum mount count (the mount count is incremented every time the disk is successfully mounted, and its value is the number of times this has been done since the last **fsck**)
* Block size (Cannot be greater than a page of memory, set by **mkfs**.)​
* Blocks per group
* Free block count
* Free inode count
* Operating System ID
  
### 20.9: Block Groups
----
* after the boot block, there is a series of **block groups**, each of which is the same size (see "20.9 - ext2 filesystem.png")
* order:
   1. superblock
   1. group descriptors
   1. data block bitmap
   1. inode bitmap
   1. inode table (n blocks)
   1. data blocks (n blocks)
* **Superblock** and **Group Descriptors** are always first and second, respectively
* "This \[Superblock\] redundancy makes it very difficult to thoroughly fry an ext2/3/4 filesystem, as long as the filesystem checks are run periodically."
* early **ext** filesystems contained the group descriptors for every block group, as well as a copy of the superblock, but not today for better performance (**sparse_super** option)
* `$ sudo dumpe2fs /dev/sda1 | grep superblock` shows superblock locations
* on my machine for `/dev/sda1`:
```
dumpe2fs 1.42.9 (28-Dec-2013)
  Primary superblock at 0, Group descriptors at 1-4
  Backup superblock at 32768, Group descriptors at 32769-32772
  Backup superblock at 98304, Group descriptors at 98305-98308
  Backup superblock at 163840, Group descriptors at 163841-163844
  Backup superblock at 229376, Group descriptors at 229377-229380
  Backup superblock at 294912, Group descriptors at 294913-294916
  Backup superblock at 819200, Group descriptors at 819201-819204
  Backup superblock at 884736, Group descriptors at 884737-884740
  Backup superblock at 1605632, Group descriptors at 1605633-1605636
  Backup superblock at 2654208, Group descriptors at 2654209-2654212
  Backup superblock at 4096000, Group descriptors at 4096001-4096004
```
* number of block groups is constrained by the fact that the **block bitmap** as to fit in a single block
* the **block bitmap** identifies used and free blocks within the group
   * thus, if a block is 4096 bytes in size, a block group can contain no more than 32 K blocks, or 128 MB
   * if we take the largest possible block group size, a 10 GB partition would thus have to have at least 80 block groups
* the block allocator tries to keep each file's blocks within the same block group to reduce seek times
  
### 20.10: Data Blocks and Inodes
----
* **data block** and **inode bitmaps** are blocks whose bits contain 0 for each free block or inode and 1 for each used one; only one of each of these bitmaps per block group
* **inode table** contains as many consecutive blocks as are necessary to cover the number of inodes in the block group; each inode requires 128 bytes; thus, a 4 KB block can contain 32 inodes
* note that there is space reserved for some operating system dependent information; different OSs could possibly mount an ext2/3/4 filesystem, much as Linux can mount many kinds of non-native filesystems
* note that the inode number itself is not stored on the disk in this structure; its value can be quickly calculated from the block group number and offset within the inode table
* the ext2 and ext3 filesystems did not yet incorporate the use of extents to organize larger files. Instead, the **i_block[]** array of pointers to data blocks, of length **EXT2_N_BLOCKS=15**, is described by the inode. The way this is handled is somewhat complex:
   * the first 12 elements in this array simply point to the first 12 data blocks in the file
   * the 13th element points to a block that represents a second-order array of block numbers; the 14th to a third-order array, and the 15th to a fourth-order array
* this algorithm makes addressing small files go the fastest, as it should. For instance, with a 4 KB block size, a 48 KB file can be directly addressed, a 2 MB file requires a second-order process, a 1 GB a third-order, a 4 GB file a fourth-order

### 20.11: dumpe2fs
----
* **dumpe2fs** is used to scan filesystem information

### 20.12: tune2fs
----
* **tune2fs** is used to change filesystem parameters
* `$ sudo tune2fs -c 25 /dev/sda1`: change the maximum number of mounts between filesystem checks (max-mount-count)
* `$ sudo tune2fs -i 10 /dev/sda1`: change the time interval between checks (interval-between-checks)
* `$ sudo tune2fs -l /dev/sda1`: list the contents of the superblock, including the current values of parameters which can be changed

### Lab 20.1: Defragmentation
----
* defragmentation is largely unnecessary on modern hardware in general and with Linux in particular
* `$ sudo e4defrag`
* `$ sudo e4defrag -c /var/log` analyze and report on fragmentation of `/var/log`
* example on `.` when at `/`:
    ```
    <Fragmented files>                             now/best       size/ext
    1. /var/log/wtmp                                30/1              4 KB
    2. /var/log/vmware-vgauthsvc.log.0              13/1              4 KB
    3. /var/log/tuned/tuned.log                     13/1              4 KB
    4. /var/log/cron-20190526                        9/1              4 KB
    5. /home/student/.config/Code/logs/20190522T214849/renderer1.log
                                                    9/1              4 KB

    Total/best extents				307499/306011
    Average size per extent			47 KB
    Fragmentation score				0
    [0-30 no problem: 31-55 a little bit fragmented: 56- needs defrag]
    This directory (.) does not need defragmentation.
    Done.
    ```
* `$ sudo e4defrag /` whoops, that took a while
    ```
    	Success:			[ 305878/383105 ]
	    Failure:			[ 77227/383105 ]
    ```
### Lab 20.2: Modifying Filesystem Parameters with tune2fs
----
* setup:
    * `$ dd if=/dev/zero of=imagefile bs=1M count=1024`
    * `$ mkfs.ext4 imagefile`
1. Using **dumpe2fs**, obtain information about the filesystem whose properties you want to adjust. Display:
    * maximum mount count setting (after which a filesystem check will be forced): `$ dumpe2fs imagefile | grep Max`
        ```
        dumpe2fs 1.42.9 (28-Dec-2013)
        Maximum mount count:      -1
        ```
    * Check interval (the amount of time after which a filesystem check is forced): `$ dumpe2fs imagefile | grep 'Check interval'`
        ```
        dumpe2fs 1.42.9 (28-Dec-2013)
        Check interval:           0 (<none>)
        ```
    * number of blocks reserved, and the total number of blocks: `$ dumpe2fs imagefile | grep -i block`; partial output only:
        ```
        Block count:              262144
        Reserved block count:     13107
        ```
2. Change:
    * maximum mount count to 30: `$ tune2fs -C 30 imagefile`
        ```
        tune2fs 1.42.9 (28-Dec-2013)
        Setting current mount count to 30
        ```
    * Check interval to three weeks: `$ tune2fs -i 3w imagefile`
        ```
        tune2fs 1.42.9 (28-Dec-2013)
        Setting interval between checks to 1814400 seconds
        ```
    * percentage of blocks reserved to 10 percent: `$ tune2fs -m 10 imagefile`
        ```
        tune2fs 1.42.9 (28-Dec-2013)
        Setting reserved blocks percentage to 10% (26214 blocks)
        ```
3. Using **dumpe2fs**, once again obtain information about the filesystem and compare with the original output.
    * done

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem | `$ sudo dumpe2fs /dev/sda1 | grep superblock` | shows superblock locations | LFS201 20.9
filesystem | `$ dumpe2fs ...` | scan filesystem information | LFS201 20.11
filesystem | `$ tune2fs ...` | change filesystem parameters | LFS201 20.12
filesystem | `$ sudo tune2fs -c 25 /dev/sda1` | change the maximum number of mounts between filesystem checks (max-mount-count) | LFS201 20.12
filesystem | `$ sudo tune2fs -i 10 /dev/sda1` | change the time interval between checks (interval-between-checks) to 10 days | LFS201 20.12
filesystem | `$ sudo tune2fs -l /dev/sda1` | list the contents of the superblock, including the current values of parameters which can be changed | LFS201 20.12
filesystem | `$ sudo e4defrag TARGET` | generic syntax | LFS201 Lab 20.1
filesystem | `$ sudo e4defrag -c /var/log` | analyze and report on fragmentation of `/var/log` | LFS201 Lab 20.1
filesystem | `$ tune2fs -C 30 imagefile` | change `maximum mount count` to 30 | LFS201 Lab 20.2
filesystem | `$ tune2fs -i 3w imagefile` | change `Check interval` to three weeks | LFS201 Lab 20.2
filesystem | `$ tune2fs -m 10 imagefile` | change percentage of blocks reserved to 10% | LFS201 Lab 20.2
