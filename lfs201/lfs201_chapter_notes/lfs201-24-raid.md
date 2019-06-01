Chapter 24: RAID
----------------

[24.3: Learning Objectives](#243-learning-objectives)  
[24.4: RAID](#244-raid)   
[24.5: RAID Levels](#245-raid-levels)  
[24.6: Software RAID Configuration](#246-software-raid-configuration)  
[24.7: Monitoring RAIDs](#247-monitoring-raids)  
[24.8: RAID Hot Spares](#248-raid-hot-spares)  
[24.9: Knowldege Check 24.1](#249-knowldege-check-241)  
[Lab 24.1: Creating a RAID Device](#lab-241-creating-a-raid-device)  
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

### 24.5: RAID Levels
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
* `$ sudo mdadm --detail /dev/md0` monitor RAID device
* `$ cat /pro/mdstat` monitor RAID device
* `$ sudo systemctl start mdmonitor` start monitoring RAID device with **mdmonitor**
* `$ sudo systemctl enable mdmonitor` ensure **mdmonitor** starts at boot

### 24.8: RAID Hot Spares
----
* **hot spares** help ensure reduction in redundancy is fixed quickly
* `$ sudo mdadm --create /dev/md0 -l 5 -n3 -x 1 /dev/sda8 /dev/sda9 /dev/sda10 /dev/sda11` create a hot spare when creating the RAID array (** -x 1** switch)
* `$ sudo mdadm --fail /dev/md0 /dev/sdb2` test redundancy and hot spare of the array
* `$ sudo mdadm --remove /dev/md0 /dev/sdb2` remove faulty drive in test or failure situation
* `$ sudo mdadm --add /dev/md0 /dev/sde2` add new drive in test or failure situation

### 24.9: Knowldege Check 24.1
----
You added two new disks to a server. In which order would you use the following commands to create a RAID device with `/dev/sdb1` and `/dev/sdc1`, and then make the RAID device available using an ext4 filesystem?
1. `$ fdisk /dev/sdb; fdisk /dev/sdc`
2. `$ mdadm --create /dev/md0 ... /dev/sdb1 /dev/sdc1`
3. `$ mkfs.ext4 /dev/md0`
4. `$ mdadm --detail --scan >> /etc/mdadm.conf`
5. `$ reboot`

### Lab 24.1: Creating a RAID Device
----
1. Create two 200 MB partitions of type raid (`fd`) either on your hard disk using **fdisk**, or using **LVM**.
    * done (well, they're 250 MB out of habit)
2. Create a **RAID 1** device named `/dev/md0` using the two partitions.
    * `$ sudo mdadm --create /dev/md0 --level=1 --raid-disks=2 /dev/sda3 /dev/sda4`
        ```
        mdadm: Note: this array has metadata at the start and
            may not be suitable as a boot device.  If you plan to
            store '/boot' on this device please ensure that
            your boot-loader understands md/v1.x metadata, or use
            --metadata=0.90
        Continue creating array? y
        mdadm: Defaulting to version 1.2 metadata
        mdadm: array /dev/md0 started.
        ```
3. Format the **RAID** device as an **ext4** filesystem. Then mount it at `/myraid` and make the mount persistent.
    * `$ sudo mkfs.ext4 /dev/md0`
    * solution: I neglected to create a mount point and mount the filesystem
4. Place the information about `/dev/md0` in `/etc/mdadm.conf` file using **mdadm**. (Depending on your distribution, this file may not previously exist.)
    * `$ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"`
5. Examine `/proc/mdstat` to see the status of your **RAID** device.
    * `$ cat /proc/mdstat`
        ```
        Personalities : [raid1] 
        md0 : active raid1 sda4[1] sda3[0]
            254976 blocks super 1.2 [2/2] [UU]
            
        unused devices: <none>
        ```
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
raid | `/dev/mdX` | RAID device | LFS201 24.4
raid | `/proc/mdstat` | RAID status | LFS201 24.6

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
raid | `$ sudo mdadm -S` | stop RAID | LFS201 24.6
raid | 1 `$ sudo fdisk /dev/sdb; sudo fdisk /dev/sdc` | create partitions | LFS201 24.6
raid | 2 `$ sudo mdadm --create /dev/md0 --level=1 --raid-disks=2 /dev/sdbX /dev/sdcX` or `$ mdadm --create /dev/md0 ... /dev/sdb1 /dev/sdc1` | set up array | LFS201 24.6
raid | 3 `$ sudo mkfs.ext4 /dev/md0` | format array | LFS201 24.6
raid | 4 `$ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"` | add array to configuration | LFS201 24.6
raid | 5 `$ sudo mkdir /myraid` | create RAID directory | LFS201 24.6
raid | 6 `$ sudo mount /dev/md0 /myraid` | mount RAID directory | LFS201 24.6
raid | 7 `$ sudo vim /etc/fstab` | add a line (`/dev/md0 /myraid ext4 defaults 0 2`) for the mount point | LFS201 24.6
raid | `$ cat /proc/mdstat` | check RAID status | LFS201 24.6
raid | `$ sudo mdadm -S /dev/md0` | stop RAID device | LFS201 24.6
raid | `$ sudo mdadm --detail /dev/md0` | monitor RAID device | LFS201 24.7
raid | `$ cat /pro/mdstat` | monitor RAID device | LFS201 24.7
raid | `$ sudo systemctl start mdmonitor` | start monitoring RAID device with **mdmonitor** | LFS201 24.7
raid | `$ sudo systemctl enable mdmonitor` | ensure **mdmonitor** starts at boot | LFS201 24.7
raid | `$ sudo mdadm --create /dev/md0 -l 5 -n3 -x 1 /dev/sda8 /dev/sda9 /dev/sda10 /dev/sda11` | create a hot spare when creating the RAID array (** -x 1** switch) | LFS201 24.8
raid | `$ sudo mdadm --fail /dev/md0 /dev/sdb2` | test redundancy and hot spare of the array | LFS201 24.8
raid | `$ sudo mdadm --remove /dev/md0 /dev/sdb2` | remove faulty drive in test or failure situation | LFS201 24.8
raid | `$ sudo mdadm --add /dev/md0 /dev/sde2` | add new drive in test or failure situation | LFS201 24.8
