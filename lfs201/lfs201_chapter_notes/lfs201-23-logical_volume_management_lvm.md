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
### 23.10: Resizing Logical Volumes
----
### 23.11: Examples of Resizing
----
### 23.12: LVM Snapshots
----
### 23.13: LVM Demo
----
### Lab 23.1: Logical Volumes
----
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

