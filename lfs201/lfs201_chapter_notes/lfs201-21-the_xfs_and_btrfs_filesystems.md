Chapter 21: The XFS and btrfs Filesystems
-----------------------------------------

[21.3: Learning Objectives](#213-learning-objectives)  
[21.4: XFS Features](#214-xfs-features)  
[21.5: XFS Details](#215-xfs-details)  
[21.6: The btrfs Filesystem](#216-the-btrfs-filesystem)  
[Lab 21.1: Finding out More about XFS](#lab-211-finding-out-more-about-xfs)  
[Lab 21.2: Finding out More about btrfs](#lab-212-finding-out-more-about-btrfs)  
[Paths and Commands](#paths-and-commands)  
  
### 21.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Describe the **XFS** filesystem.
* Maintain the **XFS** filesystem.
* Describe the **btrfs** filesystem.

### 21.4: XFS Features
----
* designed and created by Silicon Graphics, used in the IRIX OS, then ported to Linux
* engineered to deal with large data sets for SGI systems, as well as handle parallel I/O tasks very effectively
* can handle 16 EB for the total filesystem, up to 8 EB for an individual file
* implements methods for:
    * employing **DMA** (Direct Memory Access) I/O
    * guaranteeing an I/O rate
    * adjusting stripe size to match underlying **RAID** or **LVM** storage devices
* can journal quota information, leading to decreased recovery times when a quota-enabled filesystem is uncleanly unmounted
* journal can be on an external device
* as with other UNIX and Linux filesystems, **XFS** supports extended attributes
  
### 21.5: XFS Details
----
* most maintainence tasks can be done while the filesystem is fully mounted, including:
    * defragmenting
    * resizing (enlarge only)
    * dumping / restoring
* backup and restore can be done with **xfsdump** and **xfsrestore**
* **xfs_quota** can be used to set per-directory quotas
* **xfs_freeze** can be used to quiesce the filesystem to allow for a snapshot tool to work on the underlying device
  
### 21.6: The btrfs Filesystem
----
* **btrfs** stands for **B**-**tr**ee **f**ile**s**ystem
* btrfs is intended to address the lack of pooling, snapshots, checksums, and integral multi-device spanning in other Linux filesystems such as ext4
* it's the default root filesystem on SLES and OpenSUSE
* one of the main features is the ability to take frequent snapshots of entire filesystems, or sub-volumes of entire filesystems in virtually no time
    * because btrfs makes extensive use of COW techniques (Copy on Write), such a snapshot does not involve any more initial space for data blocks or any I/O activity except for some metadata updating
    * one can easily revert to the state described by earlier snapshots and even induce the kernel to reboot off an earlier root filesystem snapshot

### Lab 21.1: Finding out More about XFS
----
* `$ man -k xfs` shows **xfs**-related utilities
* that's it
  
### Lab 21.2: Finding out More about btrfs
----
* `$ btrfs`
* `$ btrfs --help`
* `$ man -k btrfs`
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem | `$ xfsdump ...` | backup **XFS** system | LFS201 21.5
filesystem | `$ xfsrestore ...` | restore **XFS** system | LFS201 21.5
filesystem | `$ xfs_quota` | can be used to set per-directory quotas | LFS201 21.5
filesystem | `$ xfs_freeze` | can be used to quiesce the filesystem to allow for a snapshot tool | LFS201 21.5
