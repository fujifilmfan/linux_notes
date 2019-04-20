Chapter 16: Linux Filesystems and the VFS
-----------------------------------------

[17.3: Learning Objectives](#173-learning-objectives)  
[17.4: Common Disk Types](#174-common-disk-types)  
[17.5: Disk Geometry](#175-disk-geometry)  
[17.6: Partition Organization](#176-partition-organization)  
[17.7: Why Partition?](#177-why-partition)  
[17.8: MBR Partition Table](#178-mbr-partition-table)  
[17.9: GPT Partition Table](#179-gpt-partition-table)  
[17.10: Naming Disk Devices and Nodes](#1710-naming-disk-devices-and-nodes)  
[17.11: More on SCSI Device Names](#1711-more-on-scsi-device-names)  
[17.12: blkid](#1712-blkid)  
[17.13: lsblk](#1713-lsblk)  
[17.14: Sizing Up Partitions](#1714-sizing-up-partitions)  
[17.15: Backing Up and Restoring Partition Tables](#1715-backing-up-and-restoring-partition-tables)  
[17.16: Partition Table Editors](#1716-partition-table-editors)  
[17.17: Using fdisk](#1717-using-fdisk)  
[17.18: Disk Partitioning Demo](#1718-disk-partitioning-demo)  
[17.9 Knowledge Check 17.1](#179-knowledge-check-171)  
[Lab 17.1: Using a File as a Disk Partition Image](#lab-171-using-a-file-as-a-disk-partition-image)  
[Lab 17.2: Partitioning a Disk Image File](#lab-172-partitioning-a-disk-image-file)  
[Lab 17.3: Using losetup and parted](#lab-173-using-losetup-and-parted)  
[Lab 17.4: Partitioning a Real Hard Disk](#lab-174-partitioning-a-real-hard-disk)  
[Paths and Commands](#paths-and-commands)  
  
### 17.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Describe and contrast the most common types of hard disks and data buses.
* Explain disk geometry and other partitioning concepts.
* Understand how disk devices are named and how to identify their associated device nodes.
* Distinguish among and select different partitioning strategies.
* Use utilities such as **fdisk**, **blkid** and **lsblk.**
* Back up and restore partition tables.
  
### 17.4: Common Disk Types
----
Different hard disk types are characterized by the type of **data bus** they are attached to, speed, capacity, etc.  Common types:  
* **SATA** (Serial Advanced Technology Attachment)  
    SATA disks were designed to replaced the old IDE drives. They offer a smaller cable size (7 pins), native hot swapping, and faster and more efficient data transfer. They are seen as SCSI devices.  
* **SCSI** (Small Computer Systems Interface)  
    SCSI disks range from narrow (8 bit bus) to wide (16 bit bus), with a transfer rate between 5 MB per second (narrow, standard SCSI) and 160 MB per second (Ultra-Wide SCSI-3). Most PCs use single-ended or differential SCSI drives. Unfortunately, the two types are not compatible with each other. Fortunately, the two types of devices may coexist on the same controller. Single-ended devices may host up to 7 devices, and use a maximum cable length of about 6 meters. Differential controllers may host up to 15 devices and have a maximum cable length of about 12 meters.  
* **SAS** (Serial Attached SCSI)  
    They use a newer point-to-point protocol, and have a better performance than SATA disks.  
* **USB** (Universal Serial Bus)
    These include flash drives and floppies and are seen as SCSI devices.  
* **SSD** (Solid State Drives)  
    Modern SSD drives have come down in price, have no moving parts, use less power than drives with rotational media, and have faster transfer speeds. Internal SSDs are even installed with the same form factor and in the same enclosures as conventional drives. SSDs still cost a bit more, but price is decreasing. It is common to have both SSDs and rotational drives in the same machines, with frequently accessed and performance critical data transfers taking place on the SSDs.  
* **IDE** and **EIDE** (Integrated Drive Electronics, Enhanced IDE)  
    These are obsolete.
  
### 17.5: Disk Geometry
----
* involves terms from rotational media like **heads**, **cylinders**, **tracks**, and **sectors**
* `$ sudo fdisk -l /dev/sda` list partition table
```
Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009aa49

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048    56641535    28319744   83  Linux
/dev/sda2        56641536    58593279      975872   82  Linux swap / Solaris
```
* **heads** read a circular **track** off a platter
* tracks are divided into **sectors**, usually 512 bytes in size
* a **cylinder** is a group which consists of the same track on all platters
* 4 KB sectors are becoming available; can lead to faster transfer speeds but lack mature OS support
  
### 17.6: Partition Organization
----
* **partitions** consist of physically contiguous groups of sectors or cylinders on the disk
* two partitioning layouts:
   * MBR (Master Boot Record)
      * dates back to MSDOS
      * can have four **primary** partitions, one of which can be designated as **extended** and contain numerous logical partitions
      * partitions can be 2 TB in size
   * GPT (GUID Partition Table)
      * modern, based on UEFI
      * can have 128 primary partitions
      * partitions can be up to 2^33 TB in size
* Linux doesn't require partitions to begin or end on cylinder boundaries but usually tries to for compatibility reasons
  
### 17.7: Why Partition?
----
Some reasons to divide data into multiple partitions:  
* Separation of user and application data from operating system files
* Sharing between operating systems and/or machines
* Security enhancement by imposing different quotas and permissions for different system parts
* Size concerns; keeping variable and volatile storage isolated from stable
* Performance enhancement of putting most frequently used data on faster storage media
* Swap space can be isolated from data and also used for hibernation storage.

A common partition layout contains a **boot** partition, a partition for the root filesystem `/`, a swap partition, and a partition for the `/home` directory tree.

### 17.8: MBR Partition Table
----
* the disk partition table is contained within the disk's Master Boot Record (MBR), and is the 64 bytes following the 446 byte boot record
* one partition on a disk may be marked active
* when the system boots, that partition is where the MBR looks for items to load
* the last 2 bytes are known as the magic number, signature word, or end of sector marker, which always have the value **0x55AA**
* each entry in the partition table is 16 bytes long and describes one of the four possible primary partitions:
   * Active bit
   * Beginning address in cylinder/head/sectors (CHS) format (ignored by Linux)
   * Partition type code, indicating: xfs, LVM, ntfs, ext4, swap, etc.  
   ? Is this easy to change as new types become available?
   * Ending address in CHS (also ignored by Linux)
   * Start sector, counting linearly from 0
   * Number of sectors in partition.
* Linux only uses the last two fields for addressing, using the linear block addressing (**LBA**) method
   
### 17.9: GPT Partition Table
----
* There are two copies of the GPT header, at the beginning and at the end of the disk, describing metadata:  
   * List of usable blocks on disk
   * Number of partitions
   * Size of partition entries; each partition entry has a minimum size of 128 bytes
* **blkid** shows partition information; for example:
```
ROOT@x7:/root>blkid /dev/sda6 /dev/sda6: LABEL="CENTOS7" UUID="77461ee7-c34a-4c5f-b0bc-29f4feecc743" TYPE="ext4" PARTUUID="1f361af4-81e6-4a81-82
ROOT@x7:/root>
```
   * **UUID** describes the filesystem on the partition, not the partition itself
   * **PARTUUID** describes the partition
* great quote from the slide: "If the hardware supports it, it is possible to migrate an MBR system to GPT, but it is not hard to brick the machine while doing so."
  
### 17.10: Naming Disk Devices and Nodes
----
* another great quote: "Normally, device nodes are accessed only through the infrastructure of the kernel's Virtual File System; *raw access through the device nodes is an extremely efficient way to destroy a filesystem*." (emphasis mine)
* for example, you do this when formatting a partition, as in: `$ sudo mkfs.ext4 /dev/sda9`
* device nodes for SCSI and SATA disks follow a simple **xxy**\[z\]** naming convention, where **xx** is the device type (usually **sd** (meaning SCSI or SATA - IDE disks would have been `/dev/hda3`)), **y** is the letter for the drive number (**a**, **b**, **c**, etc.), and **z** is the partition number:
   * the first hard disk is `/dev/sda`
   * the second hard disk is `/dev/sdb`
* partitions are also easily enumerated, as in:
   * `/dev/sdb1` is the first partition on the second disk
   * `/dev/sdc4` is the fourth partition on the third disk
* `$ ls -l /dev` show current available disk device nodes
  
### 17.11: More on SCSI Device Names
----
* drive designation is determined by the controller number/ID number combination
* drive designation (a, b, c, etc.) is primarily based on the ID number of the SCSI device, rather than its position on the bus itself
* for example, if we had two SCSI controllers with target ID number 1 and 3 on controller 0, and target ID number 2 and 5 on controller 1 (with ID 2 as the last drive):
   * ID 1 would be `/dev/sda`
   * ID 3 would be `/dev/sdb`
   * ID 2 (on controller 1) would be `/dev/sdc`
   * ID 5 would be `/dev/sdd`
  
### 17.12: blkid
----
* **blkid** is a utility to locate block devices and report on their attributes
   * it works with the **libblkid** library
   * example: `$ sudo blkid /dev/sda*`
      ```
     /dev/sda: PTTYPE="dos"  
     /dev/sda1: UUID="76e6adc1-cbce-4eb4-82b8-0f89f42f82de" TYPE="ext4"  
     /dev/sda2: UUID="829c9ec3-8178-4cae-abec-9e723755552e" TYPE="swap"  
      ```
      * without the argument `$ sudo blkid`, I just get `/dev/sda1` and `/dev/sda2`
   * devices must be finger-printable (need a block-identifying **UUID**?)
   * example 2: `$ sudo blkid -L root` (did nothing on my VM that I could see)
* main uses:
   * search for a device with a specific **NAME=value** pair
   * displaying **NAME=value** pairs for one or more devices
  
### 17.13: lsblk
----
* **lsblk** presents block device information in a tree format
* `$ lsblk`
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   40G  0 disk 
├─sda1   8:1    0   27G  0 part /
└─sda2   8:2    0  953M  0 part [SWAP]
sr0     11:0    1 1024M  0 rom  
```
  
### 17.14: Sizing Up Partitions
----
* *minimum* partitions:
   * **/(root)** for the entire logical filesystem
   * **swap** for **virtual memory**
      * thoughts on swap: [SwapFaq](https://help.ubuntu.com/community/SwapFaq) and [Is a swap partition required for SLES?](https://www.suse.com/support/kb/doc/?id=7010157)
      * rule of thumb: equal to physical memory (RAM) or more
      * the system can have multiple swap partitions and/or swap files
      * on a single disk system, try to center the swap partition (physically?)
      * on multiple disk systems, try to spread swap over disks
  
### 17.15: Backing Up and Restoring Partition Tables
----
* partitioning and re-partitioning disks are dangerous operations, so partition tables need to be backed up
* `$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1` backup **MBR** on the first disk, including the 64-bit partition table
* `$ sudo dd if=mbrbackup of=/dev/sda bs=512 count=1` restore the **MBR**
   * these commands only copy the primary partition table, not tables in other partitions like extended partitions
   * backup all data, also, as this could eliminate all data in all filesystems if something goes wrong!
* for GPT systems, it's best to use **sgdisk**, as in:  
    `x7:/tmp>sudo sgdisk --backup=/tmp/sda_backup /dev/sda`
    ```
    The operation has completed successfully.
    ```
    `x7:/tmp>sudo file sda_backup`
    ```
    sda_backup: x86 boot sector; partition 1: ID=0xee, starthead 0, startsector 1, 1000215215 sectors, extended partition table (last)\011, \
    code offset 0x63
    ```
    Note if run on a pure MBR system:
    `c7:/tmp>sudo sgdisk --backup=/tmp/sda_backup /dev/sda`
    ```
    ***************************************************************
    Found invalid GPT and valid MBR; converting MBR to GPT format in memory.
    ***************************************************************
    The operation has completed successfully.
    ```
    `c7:/tmp>file /tmp/sda_backup`
    ```
    /tmp/sda_backup: x86 boot sector; partition 1: ID=0xee, starthead 0, startsector 1, 3907029167 sectors, code offset 0xb8 
    ```
* `$ file` determines file type; examples:
   * `$ file sda1`  
   > sda1: block special  
   * `$ file dmesg.pdf`  
   > dmesg.pdf: PDF document, version 1.4  
   * `$ file hello.sh`  
   > hello.sh: Bourne-Again shell script, ASCII text executable  
   * `$ file myfile.txt`  
   > myfile.txt: ASCII text  
  
### 17.16: Partition Table Editors
----
Utilities for managing partition tables:  
* **fdisk** is a menu-driven partition table editor. It is the most standard and one of the most flexible of the partition table editors. As with any other partition table editor, make sure that you either write down the current partition table settings or make a copy of the current settings before making changes.  
* **sfdisk** is a non-interactive Linux-based partition editor program, making it useful for scripting. Use the sfdisk tool with care!  
* **parted** is the GNU partition manipulation program. It can create, remove, resize, and move partitions (including certain filesystems). The GUI interface to the parted command is **gparted**.  
* **gparted** is a widely-used graphical interface to parted.  
* **gdisk** is used for GPT systems, but can also operate on MBR systems.  
* **sgdisk** - script or command line interface.  
  
Many Linux distributions have a live/installation version which can be run off either a CDROM or USB stick so that **gparted** can be used on disks not being partitioned.  
  
### 17.17: Using fdisk
----
* **fdisk** will always be included, so good to know how to use it
* simple, text-menu driven interface
* `$ sudo fdisk /dev/sdb` start **fdisk** on `/dev/sdb`

**fdisk One-letter Commands**

command | function
------- | --------
m | Display the menu
p | List the partition table
n | Create a new partition
d | Delete a partition
t | Change a partition type
w | Write the new partition table information and exit
q | Quit without making changes

* usage notes:
   * nothing happens until entering **w**
   * verify with **p** first; what this showed for me:
   ```
   Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
   Units = sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk label type: dos
   Disk identifier: 0x0009aa49
   
   Device Boot      Start         End      Blocks   Id  System
   /dev/sda1   *        2048    56641535    28319744   83  Linux
   /dev/sda2        56641536    58593279      975872   82  Linux swap / Solaris
   ```
   * if something is wrong, exit with **q**
* new partition table will not be used until reboot
   * `sudo partprobe -s` try and read in the revised partition table (not recommended)
* `$ cat /proc/partitions` shows partitions the OS is aware of; for me:
   ```
   major minor  #blocks  name

   8        0   41943040 sda
   8        1   28319744 sda1
   8        2     975872 sda2
   11        0    1048575 sr0
   ```
  
### 17.18: Disk Partitioning Demo
----
See video.  
  
### 17.9 Knowledge Check 17.1
----
When adding a new internal disk to an existing server, in which best logical order should the following steps be executed to have the disk persistently mounted after rebooting the system?
1. Create a new partition
2. Format with a filesystem
3. Edit `/etc/fstab`
4. Reboot the system
I got it right. :)  
  
### Lab 17.1: Using a File as a Disk Partition Image
----

### Lab 17.2: Partitioning a Disk Image File
----

### Lab 17.3: Using losetup and parted
----

### Lab 17.4: Partitioning a Real Hard Disk
----

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
partitions | `$ sudo fdisk -l /dev/sda` | list partition table | LFS201 17.5
partitions | `$ ls -l /dev` | show current available disk device nodes | LFS201 17.10
partitions | `$ sudo blkid /dev/sda*` | report on block devices matching the pattern| LFS201 17.12
partitions | `$ sudo blkid` | ?? | LFS201 17.12
partitions | `$ sudo blkid -L root` | convert LABEL to 'root'(?)| LFS201 17.12
partitions | `$ lsblk` | presents block device information in a tree format | LFS201 17.13
partitions | `$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1` | backup **MBR** on the first disk | LFS201 17.15
partitions | `$ sudo dd if=mbrbackup of=/dev/sda bs=512 count=1` | restore the **MBR** | LFS201 17.15
partitions | `x7:/tmp>sudo sgdisk --backup=/tmp/sda_backup /dev/sda` | backup GPT partition | LFS201 17.16
files, partitions | `x7:/tmp>sudo file sda_backup` or `$ file myfile.txt` | determines file type | LFS201 17.16
 partitions| `$ sudo fdisk /dev/sdb` | start **fdisk** on `/dev/sdb` | LFS201 17.17
partitions | `$ sudo partprobe -s` | try and read in the revised partition table | LFS201 17.17
partitions | `$ cat /proc/partitions` | examine partitions the OS is aware of | LFS201 17.17
