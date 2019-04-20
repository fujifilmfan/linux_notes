Chapter 18: Filesystem Features I: Attributes, Creating, Checking, Mounting
---------------------------------------------------------------------------

[18.3: Learning Objectives](#183-learning-objectives)  
[18.4: Extended Attributes and lsattr/chattr](#184-extended-attributes-and-lsattrchattr)  
[18.5: Creating and Formatting Filesystems](#185-creating-and-formatting-filesystems)  
[18.6: Checking and Repairing Filesystems](#186-checking-and-repairing-filesystems)  
[18.7: Mounting Filesystems](#187-mounting-filesystems)  
[18.10: mount](#1810-mount)  
[18.11: mount Options](#1811-mount-options)  
[18.12: unmount](#1812-unmount)  
[18.13: Network Shares (NFS)](#1813-network-shares-nfs)  
[18.14: Mounting at Boot](#18.14: Mounting at Boot)  
[18.15: Automatic Filesystem Mounting](#18.15: Automatic Filesystem Mounting)  
[18.16: automount Example](#18.16: automount Example)  
[18.17: Listing Currently Mounted Filesystems](#18.17: Listing Currently Mounted Filesystems)  
[18.18: Filesystems Demo](#18.18: Filesystems Demo)  
[Lab 18.1: Working with File Attributes](#Lab 18.1: Working with File Attributes)  
[Lab 18.2: Mounting Options](#Lab 18.2: Mounting Options)  
[Paths and Commands](#paths-and-commands)  
  
### 18.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain concepts such as inodes, directory files and extended attributes.
* Create and format filesystems.
* Check and fix errors on filesystems.
* Mount and unmount filesystems.​
* Use network file systems.
* Understand how to automount filesystems and make sure they mount at boot.
  
### 18.4: Extended Attributes and lsattr/chattr
----
* **Extended Attributes** associate metadata not interpreted directly by the filesystem with files.
* Four namespaces exist: user, trusted, security, and system.
* The system namespace is used for **Access Control Lists** (**ACLs**), and the security namespace is used by SELinux.
* Flag values are stored in the file inode and may be modified and set only by the root user.
* They are viewed with **lsattr** and set with **chattr**.
* File flags:
   * **i**: Immutable  
    A file with the immutable attribute cannot be modified (not even by root). It cannot be deleted or renamed. No hard link can be created to it, and no data can be written to the file. Only the superuser can set or clear this attribute.  
    * **a**: Append-only  
    A file with the append-only attribute set can only be opened in append mode for writing. Only the superuser can set or clear this attribute.  
    * **d**: No-dump  
    A file with the no-dump attribute set is ignored when the dump program is run. This is useful for swap and cache files that you don't want to waste time backing up.  
    * **A**: No **atime** update  
    A file with the no-atime-update attribute set will not modify its atime (access time) record when the file is accessed but not otherwise modified. This can increase the performance on some systems because it reduces the amount of disk I/O on the system.  
* `$ chatter [+|-|=mode] filename` generic **chattr** format
* `$ lsattr <filename>` display attributes for a file
  
### 18.5: Creating and Formatting Filesystems
----
* **mkfs** is a generic frontend for filesystem formatting utilities which are fs-specific
* `$ mkfs [-t fstype] [options] [device-file]` generic **mkfs** format where **\[device-file\]** is usually a device name like `/dev/sda3` or `/dev/vg/1vm1`
* equivalent examples:
   * `$ sudo mkfs -t ext4 /dev/sda10`
   * `$ sudo mkfs.ext4 /dev/sda10`
* different filesystems have different options that can be set when formatting

### 18.6: Checking and Repairing Filesystems
----
* **fsck** is a generic name for filesystem checking (and repairing) utilities
* `$ fsck [-t fstype] [options] [device-file]` generic **fsck** format where **\[device-file\]** is usually a device name (see 18.5)
   * usually you do not need to specify the filesystem type
   * found errors fixed one by one manually with **-r** option
   * found errors fixed automatically with **-a** option
* equivalent examples:
   * `$ sudo fsck -t ext4 /dev/sda10`
   * `$ sudo fsck.ext4 /dev/sda10`
* `$ sudo fsck /dev/sda10` works if the FS is understood by the OS
* **fsck** is run automatically after a set number of mounts or a set interval since the last time it was run or after an abnormal shutdown
* it should only be run on unmounted filesystems
* force a check of mounted filesystems at boot, which allows **fsck** to check the root FS:
   * `$ sudo touch /forcefsck`; the file `/forcefsck` will disappear after the check
   * `$ sudo reboot`
* **journalling** filesystems are much faster to check for two reasons:
   * rarely need to scan the entire partition, as all but the last transaction have been logged and confirmed
   * even if you do, newer filesystems have been designed with **fast fsck** in mind

### 18.7: Mounting Filesystems
----

18.10: mount
18.11: mount Options
18.12: unmount
18.13: Network Shares (NFS)
18.14: Mounting at Boot
18.15: Automatic Filesystem Mounting
18.16: automount Example
18.17: Listing Currently Mounted Filesystems
18.18: Filesystems Demo
Lab 18.1: Working with File Attributes
Lab 18.2: Mounting Options

### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem | `/proc/filesystems` | currently registered filesystems | LFS201 16.10
filesystem | `/dev/zero` | special file in Unix-like operating systems that provides null characters | LFS201 16.10
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
`$ chatter [+|-|=mode] filename` | generic **chattr** format | LFS201 18.4
`$ lsattr <filename>` | display attributes for a file | LFS201 18.4
`$ mkfs [-t fstype] [options] [device-file]` | generic **mkfs** format | LFS201 18.5
`$ sudo mkfs -t ext4 /dev/sda10` | make an ext4 fs on `/dev/sda10` | LFS201 18.5
`$ sudo mkfs.ext4 /dev/sda10` | make an ext4 fs on `/dev/sda10` | LFS201 18.5
`$ fsck [-t fstype] [options] [device-file]` | generic **fsck** format 
`$ sudo fsck -t ext4 /dev/sda10` | check ext4 filesystem on `/dev/sda10` | LFS201 18.6
`$ sudo fsck.ext4 /dev/sda10` | check ext4 filesystem on `/dev/sda10` | LFS201 18.6
`$ sudo fsck /dev/sda10`  | check filesystem on `/dev/sda10` | LFS201 18.6
 `$ sudo touch /forcefsck` then `$ sudo reboot` | force a check of mounted filesystems at boot, which allows **fsck** to check the root FS | LFS201 18.6
