Chapter 17: Disk Partitioning
-----------------------------

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
   * **partprobe** informs the OS of partition table changes  
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
  
### 17.19 Knowledge Check 17.1
----
When adding a new internal disk to an existing server, in which best logical order should the following steps be executed to have the disk persistently mounted after rebooting the system?
1. Create a new partition
2. Format with a filesystem
3. Edit `/etc/fstab`
4. Reboot the system
I got it right. :)  
  
### Lab 17.1: Using a File as a Disk Partition Image
----
* In this first exercise, we are going to create a file that will be used as a container for a full hard disk partition image, and for all intents and purposes can be used like a real hard partition. In the following exercise, we will show how to put more than one partition on it and have it behave as an entire disk.
1. Create a file full of zeros 1 GB in length: `$ dd if=/dev/zero of=imagefile bs=1M count=1024` (I did this in `~/`)
    ```
    1024+0 records in
    1024+0 records out
    1073741824 bytes (1.1 GB) copied, 4.45291 s, 241 MB/s
    ```
2. Put a filesystem on it: `$ mkfs.ext4 imagefile`
    ```
    mke2fs 1.42.9 (28-Dec-2013)
    imagefile is not a block special device.
    Proceed anyway? (y,n) y
    Discarding device blocks: done                            
    Filesystem label=
    OS type: Linux
    Block size=4096 (log=2)
    Fragment size=4096 (log=2)
    Stride=0 blocks, Stripe width=0 blocks
    65536 inodes, 262144 blocks
    13107 blocks (5.00%) reserved for the super user
    First data block=0
    Maximum filesystem blocks=268435456
    8 block groups
    32768 blocks per group, 32768 fragments per group
    8192 inodes per group
    Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376

    Allocating group tables: done                            
    Writing inode tables: done                            
    Creating journal (8192 blocks): done
    Writing superblocks and filesystem accounting information: done
    ```
3. Mount it somewhere:
    * `$ mkdir mntpoint`
    * `$ sudo mount -o loop imagefile mntpoint`
4. When you are done unmount it with: `$ sudo umount mntpoint`
* An alternative method to using the loop option to mount would be:
    * `$ sudo losetup /dev/loop2 imagefile`
    * `$ mount /dev/loop2 mntpoint`
    * `$ umount mntpoint`
    * `$ losetup -d /dev/loop2`
* We will discuss **losetup** in a subsequent exercise, and you can use `/dev/loop[0-7]` but you have to be careful they are not already in use.
* You should note that using a loop device file instead of a real partition can be useful, but it is pretty worthless for doing any kind of measurements or benchmarking. This is because you are placing one filesystem layer on top of another, which can only have a negative effect on performance, and mostly you just use the behavior of the underlying filesystem the image file is created on.
  
### Lab 17.2: Partitioning a Disk Image File
----
* The next level of complication is to divide the container file into multiple partitions, each of which can be used to hold a filesystem, or a swap area.
* You can reuse the image file created in the previous exercise or create a new one.
1. Run **fdisk** on your imagefile: `$ sudo fdisk -C 130 imagefile`
    ```
    Welcome to fdisk (util-linux 2.23.2).

    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.

    Device does not contain a recognized partition table
    Building a new DOS disklabel with disk identifier 0x638cc1bf.

    Command (m for help): m
    ```
    * The `-C 130` sets the number of phony cylinders in the drive, and is only necessary in old versions of **fdisk**, which unfortunately you will find on **RHEL 6**. However, it will do no harm on other distributions.
2. Type `m` to get a list of commands:
3. Create a new primary partition and make it 256 MB (or whatever size you would like:
    ```
    Command (m for help): n
    Partition type:
        p   primary (0 primary, 0 extended, 4 free)
        e   extended
    Select (default p): p
    Partition number (1-4, default 1): 1
    First sector (2048-2097151, default 2048): 
    Using default value 2048
    Last sector, +sectors or +size{K,M,G} (2048-2097151, default 2097151): 
    Using default value 2097151
    Partition 1 of type Linux and of size 1023 MiB is set
    ```
    * oops, too big
4. Add a second primary partition also of 256 MB in size
    Enter "+256M" for "Last sector, +sectors or +size{K,M,G} ..."
5. Write the partition table to disk and exit:
    ```
    Command (m for help): w
    The partition table has been altered!

    Syncing disks.
    ```
  
### Lab 17.3: Using losetup and parted
----
1. Associate the image file with a loop device:
    * `$ losetup -a` see already in-use loop devices
    * `$ sudo losetup -f` finds the first free loop device
    * `$ sudo losetup /dev/loop1 imagefile` associate 'imagefile' with the loop device
2. Create a disk partition label on the loop device (image file):
    * `$ sudo parted -s /dev/loop1 mklabel msdos` create a disk partition label on the loop device
3. Create three primary partitions on the loop device:
    * `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256` create a primary partition from 0-256 MB
    * `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512` create a primary partition from 256-512 MB
    * `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024` create a primary partition from 512-1024 MB
4. Check the partition table:
    * `$ sudo fdisk -l /dev/loop1`
    ```
    Disk /dev/loop0: 1073 MB, 1073741824 bytes, 2097152 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000f0a24

        Device Boot      Start         End      Blocks   Id  System
    /dev/loop0p1               1      500000      250000   83  Linux
    /dev/loop0p2          500001     1000000      250000   83  Linux
    /dev/loop0p3         1000001     2000000      500000   83  Linux
    ```
5. What happens next depends on what distribution you are on. For example, on RHEL 7 and Ubuntu 16.04 you will find
new device nodes have been created:
    * `$ ls -l /dev/loop1*` see device nodes on the loop device
    ```
    brw-rw---- 1 root disk   7, 0 May 28 20:21 /dev/loop0
    brw-rw---- 1 root disk 259, 0 May 28 20:21 /dev/loop0p1
    brw-rw---- 1 root disk 259, 1 May 28 20:21 /dev/loop0p2
    brw-rw---- 1 root disk 259, 2 May 28 20:21 /dev/loop0p3
    ```
    * the device nodes are blinking
6. Put filesystems on the partitions:
    * `$ sudo mkfs.ext3 /dev/loop1p1` put ext3 filesystem on partition
    * `$ sudo mkfs.ext4 /dev/loop1p2` put ext4 filesystem on partition
    * `$ sudo mkfs.vfat /dev/loop1p3` put vfat filesystem on partition; interesting message:
    ```
    kfs.fat 3.0.20 (12 Jun 2013)
    unable to get drive geometry, using default 255/63
    ```
7. Mount all three filesystems and show they are available:
    * `$ mkdir mnt1 mnt2 mnt3` create three directories for three partitions
    * `$ sudo mount /dev/loop1p1 mnt1` mount first loop device to `mnt1`
    * `$ sudo mount /dev/loop1p2 mnt2` mount second loop device to `mnt2`
    * `$ sudo mount /dev/loop1p3 mnt3` mount third loop device to `mnt3`
    * `$ df -Th` show availability of newly mounted filesystems (in this lab)
    ```
    Filesystem     Type      Size  Used Avail Use% Mounted on
    /dev/sda1      ext4       27G   12G   14G  46% /
    devtmpfs       devtmpfs  1.9G     0  1.9G   0% /dev
    tmpfs          tmpfs     1.9G   74M  1.8G   4% /dev/shm
    tmpfs          tmpfs     1.9G  154M  1.7G   9% /run
    tmpfs          tmpfs     1.9G     0  1.9G   0% /sys/fs/cgroup
    .host:/        vmhgfs    2.9T  2.0T  925G  69% /mnt/hgfs
    tmpfs          tmpfs     378M  4.0K  378M   1% /run/user/42
    tmpfs          tmpfs     378M   56K  378M   1% /run/user/1000
    none           tmpfs     1.9G     0  1.9G   0% /mnt/tmpfs
    tmpfs          tmpfs     378M     0  378M   0% /run/user/0
    /dev/loop0p1   ext3      233M  2.1M  219M   1% /home/student/mnt1
    /dev/loop0p2   ext4      233M  2.1M  215M   1% /home/student/mnt2
    /dev/loop0p3   vfat      489M     0  489M   0% /home/student/mnt3
    ```
8. After using the filesystems to your heart’s content you can unwind it all:
    * `$ sudo umount mnt1 mnt2 mnt3` unmount the three filesystems
    * `$ rmdir mnt1 mnt2 mnt3` delete the mount points
    * `$ sudo losetup -d /dev/loop1` kill the loop device
  
### Lab 17.4: Partitioning a Real Hard Disk
----
If you have real hard disk un-partitioned space available, experiment with **fdisk** to create new partitions, either primary or logical within an extended partition. Write the new partition table to disk and then format and mount the new partitions.
  
### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem, partitions | `/etc/fstab` | configuration file containing the necessary information to automate the process of mounting partitions | LFS201 17.19

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
apps, files | `$ dd if=/dev/zero of=imagefile bs=1M count=1024` | create a file full of zeros 1 GB in length | LFS201 Lab 17.1
partitions | `$ mkfs.ext4 imagefile` | put a filesystem on 'imagefile' | LFS201 Lab 17.1
partitions | `$ mkdir mntpoint` | create directory for mounting a filesystem | LFS201 Lab 17.1
partitions | `$ sudo mount -o loop imagefile mntpoint` | mount filesystem 'imagefile' on `mntpoint` | LFS201 Lab 17.1
partitions | `$ sudo umount mntpoint` | unmount the filesystem | LFS201 Lab 17.1
partitions | `$ sudo fdisk -C 130 imagefile` | format and partition 'imagefile' w/ a phony number of cylinders | LFS201 Lab 17.2
partitions | `$ losetup -a` | see already in-use loop devices | LFS201 Lab 17.3
partitions | `$ sudo losetup -f` | finds the first free loop device | LFS201 Lab 17.3
partitions | `$ sudo losetup /dev/loop1 imagefile` | associate 'imagefile' with the loop device | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 mklabel msdos` | create a disk partition label on the loop device | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256` | create a primary partition from 0-256 MB | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512` | create a primary partition from 256-512 MB | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024` | create a primary partition from 512-1024 MB | LFS201 Lab 17.3
partitions | `$ sudo fdisk -l /dev/loop1` | check the partition table | LFS201 Lab 17.3
partitions | `$ ls -l /dev/loop1*` | see device nodes on the loop device | LFS201 Lab 17.3
partitions | `$ sudo mkfs.ext3 /dev/loop1p1` | put ext3 filesystem on partition | LFS201 Lab 17.3
partitions | `$ sudo mkfs.ext4 /dev/loop1p2` | put ext4 filesystem on partition | LFS201 Lab 17.3
partitions | `$ sudo mkfs.vfat /dev/loop1p3` | put vfat filesystem on partition | LFS201 Lab 17.3
directories, partitions | `$ mkdir mnt1 mnt2 mnt3` | create three directories for three partitions | LFS201 Lab 17.3
partitions | `$ sudo mount /dev/loop1p1 mnt1` | mount first loop device to `mnt1` | LFS201 Lab 17.3
partitions | `$ sudo mount /dev/loop1p2 mnt2` | mount second loop device to `mnt2` | LFS201 Lab 17.3
partitions | `$ sudo mount /dev/loop1p3 mnt3` | mount third loop device to `mnt3` | LFS201 Lab 17.3
filesystem, partitions | `$ df -Th` | show availability of newly mounted filesystems (in this lab) | LFS201 Lab 17.3
partitions | `$ sudo umount mnt1 mnt2 mnt3` | unmount the three filesystems | LFS201 Lab 17.3
directories, partitions | `$ rmdir mnt1 mnt2 mnt3` | delete the mount points | LFS201 Lab 17.3
partitions | `$ sudo losetup -d /dev/loop1` | kill the loop device | LFS201 Lab 17.3
