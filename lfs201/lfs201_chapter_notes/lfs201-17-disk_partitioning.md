Chapter 16: Linux Filesystems and the VFS
-----------------------------------------

[17.3: Learning Objectives](#173-learning-objectives)  
[17.4: Common Disk Types](#17.4: Common Disk Types)  
[17.5: Disk Geometry](#17.5: Disk Geometry)  
[17.6: Partition Organization](#17.6: Partition Organization)  
[17.7: Why Partition?](#17.7: Why Partition?)  
[17.8: MBR Partition Table](#17.8: MBR Partition Table)  
[17.9: GPT Partition Table](#17.9: GPT Partition Table)  
[17.10: Naming Disk Devices and Nodes](#17.10: Naming Disk Devices and Nodes)  
[17.11: More on SCSI Device Names](#17.11: More on SCSI Device Names)  
[17.12: blkid](#17.12: blkid)  
[17.13: lsblk](#17.13: lsblk)  
[17.14: Sizing Up Partitions](#17.14: Sizing Up Partitions)  
[17.15: Backing Up and Restoring Partition Tables](#17.15: Backing Up and Restoring Partition Tables)  
[17.16: Partition Table Editors](#17.16: Partition Table Editors)  
[17.17: Using fdisk](#17.17: Using fdisk)  
[17.18: Disk Partitioning Demo](#17.18: Disk Partitioning Demo)  
[Lab 17.1: Using a File as a Disk Partition Image](#Lab 17.1: Using a File as a Disk Partition Image)  
[Lab 17.2: Partitioning a Disk Image File](#Lab 17.2: Partitioning a Disk Image File)  
[Lab 17.3: Using losetup and parted](#Lab 17.3: Using losetup and parted)  
[Lab 17.4: Partitioning a Real Hard Disk](#Lab 17.4: Partitioning a Real Hard Disk)  
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
### 17.12: blkid
### 17.13: lsblk
### 17.14: Sizing Up Partitions
### 17.15: Backing Up and Restoring Partition Tables
### 17.16: Partition Table Editors
### 17.17: Using fdisk
### 17.18: Disk Partitioning Demo
### Lab 17.1: Using a File as a Disk Partition Image
### Lab 17.2: Partitioning a Disk Image File
### Lab 17.3: Using losetup and parted
### Lab 17.4: Partitioning a Real Hard Disk

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
 | `$ sudo fdisk -l /dev/sda` | list partition table | LFS201 17.5
 | `$ ls -l /dev` | show current available disk device nodes | LFS201 17.10
