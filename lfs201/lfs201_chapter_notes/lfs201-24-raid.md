Chapter 24: RAID
----------------

[24.3: Learning Objectives](#243-learning-objectives)  
[24.4: RAID](#24.4: RAID)   
[24.5: RAID levels](#24.5: RAID levels)  
[24.6: Software RAID Configuration](#24.6: Software RAID Configuration)  
[24.7: Monitoring RAIDs](#24.7: Monitoring RAIDs)  
[24.8: RAID Hot Spares](#24.8: RAID Hot Spares)  
[Lab 24.1: Creating a RAID Device](#Lab 24.1: Creating a RAID Device)  
[](#)  
[](#)  
[](#)  
[](#)  
[](#)  

[Paths and Commands](#paths-and-commands)  
  
### 24.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the concept of RAID.
* Summarize RAID levels.
* Configure a RAID device using the essential steps provided.
* Monitor RAID devices in multiple ways.
* Use **hot spares**.

### 24.4: RAID
----
* **RAID** (**R**edundant **A**rray of **I**ndependent **D**isks) spreads I/O over multiple disks
* can be implemented in software or hardware; in hardware case, OS is not directly aware of using raid
* three essential features:
    * **mirroring**: writing the same data to more than one disk
    * **striping**: splitting of data to more than one disk
    * **parity**: extra data is stored to allow problem detection and repair, yielding fault tolerance
* **mdadm** is used to create and manage RAID devices
* once created, the array name, `/dev/mdX` can be used like any other device

### 24.5: RAID levels
----
* **RAID 0** uses only striping; data is spread across multiple disks; no redundancy; no stability or recovery capabilities; performance can be improved significantly because of parallelization of I/O tasks
* **RAID 1** uses only mirroring; each disk has a duplicate; good for recovery; at least two disks are required
* **RAID 5** uses a rotating parity stripe; a single drive failure will cause no loss of data, only a performance drop; must be at least 3 disks
* **RAID 6** has striped disks with dual parity; it can handle loss of two disks, and requires at least 4 disks
* **RAID 10** is a mirrored and striped data set; at least 4 drives are needed

### 24.6: Software RAID Configuration
----
* essential steps:
    * create partitions on each disk (type `fd` in **fdisk**)
    * create RAID device with **mdadm**
    * format RAID device 
    * add device to `/etc/fstab`
    * mount RAID device
    * capture RAID details to ensure persistence
* `$ sudo mdadm -S` stop RAID
* example setup:
    * create two partitions of type **fd** on disks **sdb** and **sdc** (say `/dev/sdbX` and `/dev/sdcX`) by running **fdisk** on each:
        * `$ sudo fdisk /dev/sdb`
        * `$ sudo fdisk /dev/sdc`
    * then, set up the array, format it, add to the configuration, and mount it:
        * `$ sudo mdadm --create /dev/md0 --level=1 --raid-disks=2 /dev/sdbX /dev/sdcX`
        * `$ sudo mkfs.ext4 /dev/md0`
        * `$ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"`
        * `$ sudo mkdir /myraid`
        * `$ sudo mount /dev/md0 /myraid`
    * add a line in `/etc/fstab` for the mount point
        * `/dev/md0 /myraid ext4 defaults 0 2`
* `$ cat /proc/mdstat` check RAID status
    ```
        Personalities : [raid1]

        md0 : active raid1 sdb8[1] sdc7[0]

        ---------- 521984 blocks [2/2]

        unused devices: <none>
    ```
* `$ sudo mdadm -S /dev/md0` stop RAID device

### 24.7: Monitoring RAIDs
----
### 24.8: RAID Hot Spares
----
### Lab 24.1: Creating a RAID Device
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
raid | `/dev/mdX` | RAID device | LFS201 24.4

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
raid | `$ sudo mdadm -S` | stop RAID | LFS201 24.6
