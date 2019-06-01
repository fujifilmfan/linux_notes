Chapter 23: Logical Volume Management (LVM)
-------------------------------------------

[23.3: Learning Objectives](#233-learning-objectives)  
[23.4: LVM](#234-lvm)  
[23.5: LVM and RAID](#235-lvm-and-raid)  
[23.6: Volumes and Volume Groups](#236-volumes-and-volume-groups)  
[23.7: Logical Volumes Utilities](#237-logical-volumes-utilities)  
[23.8: Creating Logical Volumes](#238-creating-logical-volumes)  
[23.9: Displaying Logical Volumes](#239-displaying-logical-volumes)  
[23.10: Resizing Logical Volumes](#2310-resizing-logical-volumes)  
[23.11: Examples of Resizing](#2311-examples-of-resizing)  
[23.12: LVM Snapshots](#2312-lvm-snapshots)  
[23.13: LVM Demo](#2313-lvm-demo)  
[23.14: Knowledge Check 23.1](#2314-knowledge-check-231)
[23.14: Knowledge Check 23.2](#2314-knowledge-check-232)
[Lab 23.1: Logical Volumes](#lab-231-logical-volumes)  
[Paths and Commands](#paths-and-commands)  
  
### 23.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the concepts behind **LVM**.
* Create logical volumes.
* Display logical volumes.
* Resize logical volumes.
* Use **LVM** snapshots.

### 23.4: LVM
----
* **LVM** (**L**ogical **V**olume **M**anagement) breaks up one virtual partition into multiple chunks, each of which can be on different partitions and/or disks
* it's easy to change the size of logical partitions and filesystems, to add more storage space, rearrange things, etc.
* one or more **physical volumes** (disk partitions) are grouped together into a **volume group**
* then, the volume group is subdivided into **logical volumes**, which mimic to the system nominal physical disk partitions and can be formatted to contain mountable filesystems
* LVM has a performance cost, but **striping** (splitting of data to more than one disk), achieves some parallelization improvements

### 23.5: LVM and RAID
----
* the use of logical volumes is a mechanism for creating filesystems which can span more than one physical disk
* logical volumes are created by putting all the devices into a large pool of disk space (the volume group), and then allocating space from the pool to create a logical volume
* logical volumes have features similar to RAID devices; they can actually be built on top of a RAID device, giving the logical volume the redundancy of a RAID device with the scalability of LVM
* LVM has better scalability than RAID

### 23.6: Volumes and Volume Groups
----
* partitions converted to physical volumes --> physical volumes grouped into volume groups
* space on the group is divided into **extents** (usually 4 MB by default)
* volume group (vg) command line utilities:
    * **vgcreate**: creates volume groups
    * **vgextend**: adds to volume groups
    * **vgreduce**: shrinks volume groups
* utilities that manipulate what physical partitions enter or leave volume groups (pv == physical volume):
    * **pvcreate**: converts a partition to a physical volume
    * **pvdisplay**: shows the physical volumes being used
    * **pvmove**: moves the data from one physical volume within the volume group to others; this might be required if a disk or partition is being removed for some reason. It would then be followed by:
    * **pvremove**: remove a partition from a physical volume
* `$ man lvm` shows a full list of LVM utilities

### 23.7: Logical Volumes Utilities
----
* `$ ls -lf /sbin/lv*` shows logical volume utitities (lv == logical volume)
* most are symbolically linked to **lvm**, which figures out what to do based on the invoked name

### 23.8: Creating Logical Volumes
----
* `$ lvcreate ...` creates logical volumes from within volume groups
* `$ lvdisplay ...` reports on available logical volumes
* steps involved in setting up and using a new logical volume:
    1. Create partitions on disk drives (type **8e** in **fdisk**)
    2. Create physical volumes from the partitions
    3. Create the volume group
    4. Allocate logical volumes from the volume group
    5. Format the logical volumes
    6. Mount the logical volumes (also update `/etc/fstab` as needed)
* for example, assuming you have already created partitions `/dev/sdb1` and `/dev/sdc1` and given them type **8e**:
    * `$ sudo pvcreate /dev/sdb1` initializes a PV so that it is recognized as belonging to LVM
    * `$ sudo pvcreate /dev/sdc1` initializes a PV so that it is recognized as belonging to LVM
    * `$ sudo vgcreate -s 16M vg /dev/sdb1` creates a new VG on block devices
    * `$ sudo vgextend vg /dev/sdc1` adds one or more PVs to a VG
    * `$ sudo lvcreate -L 50G -n mylvm vg` creates a logical volume
    * `$ sudo mkfs -t ext4 /dev/vg/mylvm`
    * `$ sudo mkdir /mylvm`
    * `$ sudo mount /dev/vg/mylvm /mylvm`
    * add `/dev/vg/mylvm /mylvm ext4 defaults 1 2` to `/etc/fstab` to make this a persistent mount

### 23.9: Displaying Logical Volumes
----
* `$ pvdisplay /dev/sda5` shows physical volumes; leave off physical volume name to list all
* `*$ vgdisplay /dev/vg0` shows volume groups; leave off volume group name to list all
* `*$ lvdisplay /dev/vg0/lvm1` shows logical volumes; leave off logical volume name to list all

### 23.10: Resizing Logical Volumes
----
* if the volume contains a filesystem, then the filesystem needs to be shrunk or expanded before shrinking or resizing the volume, respectively
* `$ sudo lvresive -r -L 20 GB /dev/VG/mylvm` resizes the volume
    * **-r** causes resizing of the filesystem at the same time
    * from the man page: `-L|--size [+|-]Size[m|UNIT]`
    * the filesystem cannot be mounted while being shrunk, but some can be expanded while mounted
* the exact utilities to change filesystem size are filesystem dependents; examples: **lvresize**, **lvextend**, and **lvreduce** with **resize2fs**

### 23.11: Examples of Resizing
----
* grow a logical volume with ext4 filesytem:
    * `$ sudo lvextend -L +500M /dev/vg/mylvm`
    * `$ sudo resize2fs /dev/vg/mylvm`
* shrink the filesystem:
    * `$ sudo umount /mylvm`
    * `$ sudo fsck -f /dev/vg/mylvm`
    * `$ sudo resize2fs /dev/vg/mylvm 200M`
    * `$ sudo lvreduce -L 200M /dev/vg/mylvm`
    * `$ sudo mount /dev/vg/mylvm`
* recent versions of the **lvm** utilities allow one to expand or shrink with one step instead of two (instead of using resize2fs)
    * `$ sudo lvextend -r -L +100M /dev/vg/mylvm`
    * `$ sudo lvreduce -r -L -100M /dev/vg/mylvm`
    * these use the underlying **fsadm** utility
* reduce a volume group:
    * `$ sudo pvmove /dev/sdc1`
    * `$ sudo vgreduce vg /dev/sdc1`

### 23.12: LVM Snapshots
----
* LVM snapshots create an exact copy of an existing logical volume
    * they are useful for backups, application testing, and deploying VMs (Virtual Machines)
    * the original state of the snapshot is kept as the block map
    * snapshots only use space for storing deltas
* example use:
    * `$ sudo lvcreate -l 128 -s -n mysnap /dev/vg/mylvm` create a snapshot of an existing logical volume
    * `$ mkdir /mysnap` make a mount point
    * `$ mount -o ro /dev/vg/mysnap /mysnap` mount the snapshot
    * `$ sudo umount /mysnap` unmount the snapshot
    * `$ sudo lvremove /dev/vg/mysnap` remove the snapshot
* always be sure to remove the snapshot when you are through with it
    * if you do not remove the snapshot and it fills up because of changes, it will be automatically disabled
    * a snapshot with the size of the original will never overflow

### 23.13: LVM Demo
----

### 23.14: Knowledge Check 23.1
----
PVs are grouped into VGs. VGs are split into LVs.

### 23.14: Knowledge Check 23.2
----
What is the best logical order of actions needed to make a new disk available using **LVM**?
1. Partition the disk
2. Create a physical volume
3. Create a volume group
4. Allocate a logical volume
5. Format the logical volume
6. Mount the logical volume

### Lab 23.1: Logical Volumes
----
* You need real partitionable disk space available.
1. Create two 250 MB partitions of type logical volume (`8e`).
    * **fdisk**
2. Convert the partitions to physical volumes.
    * `$ sudo pvcreate /dev/sda3`
        ```
        WARNING: ext4 signature detected on /dev/sda3 at offset 1080. Wipe it? [y/n]: y
        Wiping ext4 signature on /dev/sda3.
        Physical volume "/dev/sda3" successfully created.
        ```
    * `$ sudo pvcreate /dev/sda4`
        ```
        WARNING: crypto_LUKS signature detected on /dev/sda4 at offset 0. Wipe it? [y/n]: y
        Wiping crypto_LUKS signature on /dev/sda4.
        Physical volume "/dev/sda4" successfully created.
        ```
3. Create a volume group named myvg and add the two physical volumes to it. Use the default extent size.
    * `$ sudo vgcreate myvg /dev/sda3 /dev/sda4`
4. Allocate a 300 MB logical volume named mylvm from volume group `myvg`.
    * `$ sudo lvcreate -L 300M -n mylvm myvg`
5. Format and mount the logical volume mylvm at `/mylvm`
    * `$ sudo mkfs.ext4 /dev/myvg/mylvm`
    * `$ sudo mkdir /mylvm`
    * `$ sudo mount /dev/myvg/mylvm /mylvm`
6. Use **lvdisplay** to view information about the logical volume.
    ```
      --- Logical volume ---
      LV Path                /dev/myvg/mylvm
      LV Name                mylvm
      VG Name                myvg
      LV UUID                Lfltqj-Us1l-L7tk-wOrO-nCVq-HYYh-l0oGpU
      LV Write Access        read/write
      LV Creation host, time centos, 2019-05-31 21:41:40 -0700
      LV Status              available
      # open                 1
      LV Size                300.00 MiB
      Current LE             75
      Segments               2
      Allocation             inherit
      Read ahead sectors     auto
      - currently set to     8192
      Block device           253:0
    ```
7. Grow the logical volume and corresponding filesystem to 350 MB.
    * `$ sudo lvresize -r -L 350M /dev/myvg/mylvm` or
    * `$ sudo lvresize -r -L +50M /dev/myvg/mylvm`
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
lvm | `/sbin/lv*` | logical volume utilties | LFS201 23.7

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
lvm | `$ vgcreate ...` | creates volume groups | LFS201 23.6
lvm | `$ vgextend ...` | adds to volume groups | LFS201 23.6
lvm | `$ vgreduce ...` | shrinks volume groups | LFS201 23.6
lvm | `$ pvcreate ...` | converts a partition to a physical volume | LFS201 23.6
lvm | `$ pvdisplay ...` | shows the physical volumes being used | LFS201 23.6
lvm | `$ pvmove ...` | moves the data from one physical volume within the volume group to others | LFS201 23.6
lvm | `$ pvremove ...` | remove a partition from a physical volume | LFS201 23.6
documentation, lvm | `$ man lvm` | show a full list of LVM utilities | LFS201 23.6
lvm | `$ ls -lF /sbin/lv*` | show logical volume utilities | LFS201 23.7
lvm | `$ lvcreate ...` | creates logical volumes from within volume groups | LFS201 23.8
lvm | `$ lvdisplay ...` | reports on available logical volumes | LFS201 23.8
lvm | `$ sudo pvcreate /dev/sdb1` | initializes a PV so that it is recognized as belonging to LVM | LFS201 23.8
lvm | `$ sudo pvcreate /dev/sdc1` | initializes a PV so that it is recognized as belonging to LVM | LFS201 23.8
lvm | `$ sudo vgcreate -s 16M vg /dev/sdb1` | creates a new VG on block devices | LFS201 23.8
lvm | `$ sudo vgextend vg /dev/sdc1` | adds one or more PVs to a VG | LFS201 23.8
lvm | `$ sudo lvcreate -L 50G -n mylvm vg` | creates a logical volume | LFS201 23.8
lvm | `$ pvdisplay /dev/sda5` | shows physical volumes; leave off physical volume name to list all | LFS201 23.9
lvm | `*$ vgdisplay /dev/vg0` | shows volume groups; leave off volume group name to list all | LFS201 23.9
lvm | `*$ lvdisplay /dev/vg0/lvm1` | shows logical volumes; leave off logical volume name to list all | LFS201 23.9
lvm | `$ sudo lvresive -r -L 20 GB /dev/VG/mylvm` | resizes the volume, where **-r** also resizes the filesystem | LFS201 23.10
lvm | 1 `$ sudo lvextend -L +500M /dev/vg/mylvm` | grow a logical volume with ext4 filesytem | LFS201 23.11
lvm | 2 `$ sudo resize2fs /dev/vg/mylvm` | grow a logical volume with ext4 filesytem | LFS201 23.11
lvm | 1 `$ sudo umount /mylvm` | shrink the filesystem | LFS201 23.11
lvm | 2 `$ sudo fsck -f /dev/vg/mylvm` | shrink the filesystem | LFS201 23.11
lvm | 3 `$ sudo resize2fs /dev/vg/mylvm 200M` | shrink the filesystem | LFS201 23.11
lvm | 4 `$ sudo lvreduce -L 200M /dev/vg/mylvm` | shrink the filesystem | LFS201 23.11
lvm | 5 `$ sudo mount /dev/vg/mylvm` | shrink the filesystem | LFS201 23.11
lvm | `$ sudo lvextend -r -L +100M /dev/vg/mylvm` | expand in one step (without **resize2fs** directly) | LFS201 23.11
lvm | `$ sudo lvreduce -r -L -100M /dev/vg/mylvm` | shrink in one step (without **resize2fs** directly) | LFS201 23.11
lvm | 1 `$ sudo pvmove /dev/sdc1` | reduce a volume group | LFS201 23.11
lvm | 2 `$ sudo vgreduce vg /dev/sdc1` | reduce a volume group | LFS201 23.11
lvm | 1 `$ sudo lvcreate -l 128 -s -n mysnap /dev/vg/mylvm` | create a snapshot of an existing logical volume | LFS201 23.12
lvm | 2 `$ mkdir /mysnap` | make a mount point | LFS201 23.12
lvm | 3 `$ mount -o ro /dev/vg/mysnap /mysnap` | mount the snapshot | LFS201 23.12
lvm | 4 `$ sudo umount /mysnap` | unmount the snapshot | LFS201 23.12
lvm | 5 `$ sudo lvremove /dev/vg/mysnap` | remove the snapshot | LFS201 23.12
