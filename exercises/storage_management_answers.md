Storage Management
------------------

### Chapters: LFS201: 16

### Ch.16
* list available filesystems - `$ cat /proc/filesystems`
* mount an xfs filesystem using the loopback mechanism and create a container for the filesystem - 
    * `$ dd if=/dev/zero of=junk bs=1M count=512`
    * `$ sudo /sbin/mkfs.xfs junk`
    * `$ sudo mount junk /mnt`
    * `$ df -h`
    * `$ cat /proc/filesystems`
    * `$ lsmod | less`

### Lab 16.1
* mount a new instance of tmpfs anywhere on your directory structure - `$ sudo mkdir /mnt/tmpfs`, `$ sudo mount -t tmpfs none /mnt/tmpfs`
* see how much space the filesystem has been given and how much it is using - `$ df -h /mnt/tmpfs`
* change the allotted size as a mount option - `$ sudo mount -t tmpfs -o size=1G none /mnt/tmpfs`
* unmount when you are done - `$ sudo umount /mnt/tmpfs`
* view the instance of tmpfs at /dev/shm - `$ df -h /dev/shm`
* create some files in `/dev/shm` and note how the filesystem is filling up with df
* view multiple instances of tmpfs - `$ df -h | grep tmpfs`

### Ch.17
* list partition table - `$ sudo fdisk -l /dev/sda`
* locate block devices and report on their attributes - `$ sudo blkid /dev/sda*`
* show block device information in a tree format - `$ lsblk`
* backup **MBR** on the first disk, including the 64-bit partition table - `$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1` 
* restore the partition table (not really) - `$ sudo dd if=mbrbackup of=/dev/sda bs=512 count=1`
* determine the type of a file - `$ file <filename>`
* try and read in a revised partition table - `sudo partprobe -s`
* show partitions the OS is aware of - `$ cat /proc/partitions`

When adding a new internal disk to an existing server, in which best logical order should the following steps be executed to have the disk persistently mounted after rebooting the system?
1. Create a new partition
2. Format with a filesystem
3. Edit `/etc/fstab`
4. Reboot the system
I got it right. :)  


### Lab 17.1
* 1 create a file full of zeros 1 GB in length - `$ dd if=/dev/zero of=imagefile bs=1M count=1024`
* 1 put a filesystem on it - `$ mkfs.ext4 imagefile`
* 1 mount it somewhere - `$ mkdir mntpoint`, `$ sudo mount -o loop imagefile mntpoint`
* 1 unmount it - `$ sudo umount mntpoint`
* 2  - `$ sudo losetup /dev/loop2 imagefile`
* 2  - `$ sudo mount /dev/loop2 mntpoint`
* 2  - `$ sudo umount mntpoint`
* 2  - `$ sudo losetup -d /dev/loop2`

### Lab 17.2 and 17.3
* run fdisk on your imagefile
* type m to get a list of commands
* create a new primary partition and make it 256 MB (or whatever size you would like
* add a second primary partition also of 256 MB in size
* write the partition table to disk and exit
* associate the image file with a loop device - `$ losetup -a`, `$ sudo losetup -f`, `$ sudo losetup /dev/loop1 imagefile`
* create a disk partition label on the loop device (image file) - `$ sudo parted -s /dev/loop1 mklabel msdos`
* create three primary partitions on the loop device using parted - `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256`, `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512`, `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024`
* check the partition table - `$ fdisk -l /dev/loop1`
* list new device nodes - `$ ls -l /dev/loop1*`
* put filesystems on the partitions - `$ sudo mkfs.ext3 /dev/loop1p1`, `$ sudo mkfs.ext4 /dev/loop1p2`, `$ sudo mkfs.vfat /dev/loop1p3`
* mount all three filesystems and show they are available - `$ mkdir mnt1 mnt2 mnt3`, `$ sudo mount /dev/loop1p1 mnt1`, `$ sudo mount /dev/loop1p2 mnt2`, `$ sudo mount /dev/loop1p3 mnt3`, `$ df -Th`
* after using the filesystems to your heart’s content you can unwind it all - `$ sudo umount mnt1 mnt2 mnt3`, `$ rmdir mnt1 mnt2 mnt3`, `$ sudo losetup -d /dev/loop1`

### Ch.18
* File flags:
   * **i**: Immutable  
    A file with the immutable attribute cannot be modified (not even by root). It cannot be deleted or renamed. No hard link can be created to it, and no data can be written to the file. Only the superuser can set or clear this attribute.  
    * **a**: Append-only  
    A file with the append-only attribute set can only be opened in append mode for writing. Only the superuser can set or clear this attribute.  
    * **d**: No-dump  
    A file with the no-dump attribute set is ignored when the dump program is run. This is useful for swap and cache files that you don't want to waste time backing up.  
    * **A**: No **atime** update  
    A file with the no-atime-update attribute set will not modify its atime (access time) record when the file is accessed but not otherwise modified. This can increase the performance on some systems because it reduces the amount of disk I/O on the system.  
* `$ chattr [+|-|=mode] filename` generic **chattr** format
* `$ lsattr <filename>` display attributes for a file


* make an ext4 filesystem on a partition or imagefile - `$ sudo mkfs -t ext4 /dev/sda10` or `$ sudo mkfs.ext4 /dev/sda10`
* different filesystems have different options that can be set when formatting
* check health of unmounted filesystem - `$ sudo fsck -t ext4 /dev/sda10` or `$ sudo fsck.ext4 /dev/sda10`
* automatically repair a filesystem - `$ sudo fsck -a /dev/sda10`
* interactively repair a filesystem - `$ sudo fsck -r /dev/sda10`
* list currently mounted filesystems - `$ mount`
* mount an ext filesystem at `/home` - `$ sudo mount -t ext /dev/sdb4 /home`
* mount a filesystem with a label - `$ sudo mount LABEL=home /home` or `$ sudo mount -L home /home`
* remount a filesystem with the read-only attribute - `$ sudo mount -o remount,ro /myfs`
* view partitions by-id, by-path, by-uuid - `ls -al /dev/disk/...`
* mount all filesystems in `/etc/fstab` - `$ mount -a`
* unmount the `/dev/sda3` device - `$ sudo umount /dev/sda3`
* list open files - `$ lsof`

### Lab 18.1
* with your normal user account use touch to create an empty file named `/tmp/appendit` - `$ cd /tmp`, `$ touch appendit $ ls -l appendit`
* use cat to append the contents of `/etc/hosts` to `/tmp/appendit` - `$ cat /etc/hosts > appendit`
* compare the contents of /tmp/appendit with /etc/hosts; there should not be any differences - `$ diff /etc/hosts appendit`
* try to add the append-only attribute to /tmp/appendit by using chattr. You should see an error here. Why? - `$ chattr +a appendit`
* as root, retry adding the append-only attribute; this time it should work. Look at the file’s extended attributes by using lsattr - `$ sudo chattr +a appendit $ lsattr appendit`
* as a normal user, try and use cat to copy over the contents of /etc/passwd to /tmp/appendit. You should get an error. Why? - `$ cat /etc/passwd > appendit`
* try the same thing again as root. You should also get an error. Why? - `$ sudo su`, `$ cat /etc/passwd > appendit`, `$ exit`
* as the normal user, again use the append redirection operator (>>) and try appending the `/etc/passwd` file to `/tmp/ appendit`. This should work, verify - `$ cat /etc/passwd >> /tmp/appendit`, `$ cat appendit`
* as root, set the immutable attribute on /tmp/appendit, and look at the extended attributes again - `$ sudo chattr +i appendit`, `$ lsattr appendit`
* try appending output to `/tmp/appendit`, try renaming the file, creating a hard link to the file, and deleting the file as both the normal user and as root - `$ echo hello >> appendit`, `$ mv appendit appendit.rename`, `$ ln appendit appendit.hardlink`, `$ rm -f appendit`, `$ sudo su`, `$ echo hello >> appendit`, `$ mv appendit appendit.rename`, `$ ln appendit appendit.hardlink`, `$ rm -f appendit`, `$ exit`
* remove this file by removing the extended attributes - `$ sudo su`, `$ lsattr appendit`, `$ ls appendit`

### Lab 18.2
* use fdisk to create a new 250 MB partition on your system, probably on `/dev/sda` or create a file full of zeros to use as a loopback file to simulate a new partition
* use mkfs to format a new filesystem on the partition or loopback file just created; do this three times, changing the block size each time. Note the locations of the superblocks, the number of block groups and any other pertinent information, for each case.
* create a new subdirectory (say /mnt/tempdir) and mount the new filesystem at this location. Verify it has been mounted.
* unmount the new filesystem, and then remount it as read-only.
* try to create a file in the mounted directory. You should get an error here, why?
* unmount the filesystem again.
* add a line to your `/etc/fstab` file so that the filesystem will be mounted at boot time.
* mount the filesystem
* modify the configuration for the new filesystem so that binary files may not be executed from the filesystem (change defaults to noexec in the /mnt/tempdir entry). Then remount the filesystem and copy an executable file (such as /bin/ls) to /mnt/tempdir and try to run it. You should get an error: why?
    * Physical Partition Solution:
    ```
    1. We won’t show the detailed steps in fdisk, as it is all ground covered earlier. We will assume the partition created is /dev/sda11, just to have something to show.
        $ sudo fdisk /dev/sda
        .....
        w
        $ partprobe -s
    Sometimes the partprobe won’t work, and to be sure the system knows about the new partition you have to reboot.
    2. $ sudo mkfs -t ext4 -v /dev/sda11 $ sudo mkfs -t ext4 -b 2048 -v /dev/sda11 $ sudo mkfs -t ext4 -b 4096 -v /dev/sda11
    Note the -v flag (verbose) will give the requested information; you will see that for a small partition like this the default is 1024 byte blocks.
    3. $ sudo mkdir /mnt/tempdir
    $ sudo mount /dev/sda11 /mnt/tempdir
    $ mount | grep tempdir
    4. $ sudo umount /mnt/tempdir
    $ sudo mount -o ro /dev/sda11 /mnt/tempdir
    If you get an error while unmounting, make sure you are not currently in the directory.
    5. $ sudo touch /mnt/tempdir/afile
    6. $ sudo umount /mnt/tempdir
    7. Put this line in /etc/fstab:
    /dev/sda11 /mnt/tempdir ext4 defaults 1 2
    8. $ sudo mount /mnt/tempdir
    $ sudo mount | grep tempdir
    9. Change the line in /etc/fstab to: /dev/sda11 /mnt/tempdir ext4 noexec 1 2 Then do:
    $ sudo mount -o remount /mnt/tempdir
    $ sudo cp /bin/ls /mnt/tempdir
    $ /mnt/tempdir/ls
    You should get an error here, why?
    ```
    * Loopback File Solution
    ```
    1. $ sudo dd if=/dev/zero of=/imagefile bs=1M count=250
    2. $ sudo mkfs -t ext4 -v
    $ sudo mkfs -t ext4 -b 2048 -v /imagefile $ sudo mkfs -t ext4 -b 4096 -v /imagefile
    You will get warned that this is a file and not a partition, just proceed.
    Note the -v flag (verbose) will give the requested information; you will see that for a small partition like this the default is 1024 byte blocks.
    3. $ sudo mkdir /mnt/tempdir
    $ sudo mount -o loop /imagefile /mnt/tempdir $ mount | grep tempdir
    4. $ sudo umount /mnt/tempdir
    $ sudo mount -o ro,loop /imagefile /mnt/tempdir
    If you get an error while unmounting, make sure you are not currently in the directory.
    5. $ sudo touch /mnt/tempdir/afile
    6. $ sudo umount /mnt/tempdir
    7. Put this line in /etc/fstab:
    8. $ sudo mount /mnt/tempdir
    $ sudo mount | grep tempdir
    9. Change the line in /etc/fstab to:
    Then do:
    $ sudo mount -o remount /mnt/tempdir
    $ sudo cp /bin/ls /mnt/tempdir
    $ /mnt/tempdir/ls
    You should get an error here, why?
    ```

### Ch.2
* Lab 2.1
    * Use the du utility to calculate the overall size of each of your system’s top-level directories.
> $ sudo du --max-depth=1 -hx / (-x ignores /dev, /proc, /run, /sys, /bin, /sbin)  
> mine: $ sudo du -cshx --exclude=/proc *  
```
-c total  
-h human readable  
-s summarize (otherwise every file on the system!  
-x skip directories on different file systems  
-X exclude  
```

### Ch.19
### 19.4: Using swap
----
* check on swap areas - `$ cat /proc/swaps`
* get basic memory statistics - `$ free -m`

* commands involving swap:
   * **mkswap**: format a swap partition or file
   * **swapon**: activate a swap partition or file
   * **swapoff**: deactivate a swap partition or file
* most in-use memory is for caching file contents to prevent going to disk; such pages of memory are never swapped out as the backing store is the files themselves, so writing out a swap would be pointless; instead **dirty** pages (memory containing updated file contents that no longer reflect the stored data) are flushed out to disk
* memory used by the kernel is never **swapped** out
  
### 19.5: Filesystem Quotas
----
* disk quotoa allow admins to control the maximum space users or groups are allowed
   * can be set based on user and group IDs
* utilities that help manage quotas:
   * **quotacheck**: generates and updates quota accounting files
   * **quotaon**: enables quota accounting
   * **quotaoff**: disables quota accounting
   * **edquota**: used for editing user or group quotas
   * **quota**: reports on usage and limits
   * **xfs_quota**: ??
* the files `aquoata.user` and `aquota.group` are required in the root directory of the filesystem using quotas
  
### 19.6: Setting up Quotas
----
* to create a filesystem quota, you must first make sure you have mounted the filesystem with the user and/or group quota mount options; basic steps:
   * mount the filesystem with user and/or group quota options:
       1. add the **usrquota** and/or **grpquota** options to the filesystems entry in `/etc/fstab`; example assuming `/home` is on a dedicated partition: `/dev/sda5 /home ext4 defaults,usrquota 1 2 `
          * test with the following:
             * `$ sudo mount -o remount /home`
             * `$ sudo quotacheck -vu /home`
             * `$ sudo quotaon -vu /home`
             * `$ sudo edquota someusername`
       1. remount the filesystem (or mount it if new)
    * run **quotacheck** on the filesystem to set up quotas
    * enable quotas on the filesystem
    * set quotas with the **edquota** program
       * **grace periods** can be used
  
### 19.7: quotacheck
----
* **quotacheck** creates and updates the quota accounting files (**aquota.user** and **aquota.group**)
   * use **-v** option to get more verbose output
   * generally only run when quotes are initially turned on or need to be updated
   * can also be run when **fsck** reports FS errors during boot
* `$ sudo quotacheck -ua` update user files for all filesystems in `/etc/fstab` with user quota options
* `$ sudo quotacheck -ga` update group files for all filesystems in `/etc/fstab` with group quota options
* `$ sudo quotacheck -u [somefilesystem]` update the user file for a particular filesystem
* `$ sudo quotacheck -g [somefilesystem]` update the group file for a particular filesystem
  
### 19.8: Turning Quotas On and Off
----
* **quotaon** and **quotaoff** are really the same program
* **quotaon** is used to turn filesystem quotas on
   * `$ sudo quotaon [flags] [filesystem]` generic quotaon syntax
* **quotaoff** is used to turn them off
   * `$ sudo quotaoff [flags] [filesystem]` generic quotaoff syntax

**quotaon and quotaoff Flags**

Flag | Function
---- | -------
-a, --all               | turn quotas off (on and off??) for all filesystems
-f, --off               | turn quotas off
-u, --user              | operate on user quotas
-g, --group             | operate on group quotas
-p, --print-state       | print whether quotas are on or off
-x, --xfs-command=cmd   | perform XFS quota command
-F, --format=formatname | operate on specific quota format
-v, --verbose           | print more messages
-h, --help              | display this help text and exit
-V, --version           | display version information and exit

* example usages:
   * `$ sudo quotaon -av`
      > /dev/sda6 [/]: group quotas turned on
      > /dev/sda5 [/home]: user quotas turned on

   * `$ sudo quotaoff -av`
      > /dev/sda6 [/]: group quotas turned off
      > /dev/sda5 [/home]: user quotas turned off

   * `$ sudo quotaon -avu`
      > /dev/sda5 [/home]: user quotas turned on

   * `$ sudo quotaoff -avu`
      > /dev/sda5 [/home]: user quotas turned off

   * `$ sudo quotaon -avg`
      > /dev/sda6 [/]: group quotas turned on

   * `$ sudo quotaoff -avg`
      > /dev/sda6 [/]: group quotas turned off
  
### 19.9: Examining Quotas
----
* **quota** generates reports on quotas
* `$ quota` or `$ quota -u` show your current user quota
* `$ quota -g` shows your current group quota
* `$ sudo quota <username>` to see quota for any particular user or group
  
### 19.10: Setting Quotas
----
* **edquota** bring up the quota editor
* the only fields which can be edited in the quota are the soft and hard limits. The other fields are informational only
* example usage:
   * `$ edquota -u [username]` edits limits for username
   * `$ edquota -g [groupname]` edits limits for groupname
   * `$ edquota -u -p [userproto] [username]` copies userproto's user quota values to username
   * `$ edquota -g -p [groupproto] [groupname]` copies groupproto's group quota values to groupname
   * `$ edquota -t` to set grace periods
* the last two are useful in scripts used in creating new accounts and setting quotas for them
* quotas for users and groups may be set for disk blocks and/or inodes
* soft limits may be exceeded for a grace period, set on a per-filesystem basis
* hard limits may never be exceeded
   * `$ sudo edquota gracie`
   * `$ sudo edquota -t`
  
### 19.11: df: Filesystem Usage
----
* **df** (**d**isk **f**ree) utility examines filesystem capacity and usage
   * use **-h** to make human-readable
   * use **-T** to show filesystem type
   * use **-i** to show inode information instead of bytes
  
* display disk usage for current directory - `$ du` 
* list all files, not just directories - `$ du -a` 
* display disk usage for a specific directory - `$ du -h somedir`
  
### 19.13 Knowledge Check 19.3
----
Organize the steps below in the best logical order to enable quota on an existing filesystem:
1. Edit `/etc/fstab` to add **usrquota** mount option.
2. Remount the filesystem.
3. Run **quotaon** to enable quota.
4. Edit individual user or group quota with **edquota**.


### Lab 19.1
* examine your current swap space - `$ cat /proc/swaps`
* add more swap space by adding either a new partition or a file - `$ dd if=/dev/zero of=swpfile bs=1M count=1024`, `$ mkswap swpfile` or `$ mkswap <partition name>`
* activate the new swap space - `$ sudo swapon swpfile`
* remove the swap file from use and delete it to save space - `$ sudo swapoff swpfile`, `$ sudo rm swpfile`

### Lab 19.2
* change the entry in `/etc/fstab` for your new filesystem to use user quotas (change noexec to usrquota in the entry for /mnt/tempdir). Then remount the filesystem - `/dev/sda11 /mnt/tempdir ext4 usrquota 1 2` or `/imagefile  /mnt/tempdir ext4 loop,usrquota 1 2` then `$ sudo mount -o remount /mnt/tempdir`
* initialize quotas on the new filesystem, and then turn the quota checking system on - `$ sudo quotacheck -u /mnt/tempdir`, `$ sudo quotaon -u /mnt/tempdir`, `$ sudo chown student.student /mnt/tempdir`
* now set some quota limits for the normal user account: a soft limit of 500 blocks and a hard limit of 1000 blocks - `$ sudo edquota -u student`
* as the normal user, attempt to use dd to create some files to exceed the quota limits. Create bigfile1 (200 blocks) and bigfile2 (400 blocks). You should get a warning. Why? - `$ cd /mnt/tempdir`, `$ dd if=/dev/zero of=bigfile1 bs=1024 count=200`, `$ quota`, `$ dd if=/dev/zero of=bigfile2 bs=1024 count=400`
* create bigfile3 (600 blocks) - `$ quota`, `$ dd if=/dev/zero of=bigfile3 bs=1024 count=600`, `$ quota`, `$ ls -l`
* eliminate the persistent mount line you inserted in `/etc/fstab`

### Ch.20
* show superblock locations - `$ sudo dumpe2fs /dev/sda1 | grep superblock` 
* change the maximum number of mounts between filesystem checks (max-mount-count) - `$ sudo tune2fs -c 25 /dev/sda1`
* change the time interval between checks (interval-between-checks) - `$ sudo tune2fs -i 10 /dev/sda1`
* list the contents of the superblock, including the current values of parameters which can be changed - `$ sudo tune2fs -l /dev/sda1`

* Lab 20.2
    We are going to fiddle with some properties of a formatted ext4 filesystem. This does not require unmounting the filesystem
    first.
    In the below you can work with an image file you create as in:
    $ dd if=/dev/zero of=imagefile bs=1M count=1024
    $ mkfs.ext4 imagefile
    or you can substitute /dev/sdaX (using whatever partition the filesystem you want to modify is mounted on) for imagefile.
    1. Using dumpe2fs, obtain information about the filesystem whose properties you want to adjust.
    Display:
    • The maximum mount count setting (after which a filesystem check will be forced.)
    • The Check interval (the amount of time after which a filesystem check is forced) • The number of blocks reserved, and the total number of blocks
    ```
    1. $ dumpe2fs imagefile > dumpe2fs-output-initial
    $ grep -i  -e "Mount count" -e "Check interval" -e "Block Count" dumpe2fs-output-initial
    ```
    2. Change:
    • The maximum mount count to 30.
    • The Check interval to three weeks.
    • The percentage of blocks reserved to 10 percent.
    ```
    $ tune2fs -c 30 imagefile tune2fs 1.42.9 (28-Dec-2013)
    Setting maximal mount count to 30
    $ tune2fs -i 3w imagefile
    tune2fs 1.42.9 (28-Dec-2013)
    Setting interval between checks to 1814400 seconds
    $ tune2fs -m 10 imagefile
    tune2fs 1.42.9 (28-Dec-2013)
    Setting reserved blocks percentage to 10% (26214 blocks)
    ```
    3. Using dumpe2fs, once again obtain information about the filesystem and compare with the original output.
    ```
    3. $ dumpe2fs imagefile > dumpe2fs-output-final
    dumpe2fs 1.42.9 (28-Dec-2013)
    $ grep -i  -e "Mount count" -e "Check interval" -e "Block Count" dumpe2fs-output-final
    4. $ diff dumpe2fs-output-initial dumpe2fs-output-final
    ```

### Ch.23
* for example, assuming you have already created partitions `/dev/sdb1` and `/dev/sdc1` and given them type **8e** (do all this with lvm):
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

* Lab 23.1
    1. Create two 250 MB partitions of type logical volume (8e).
    ```
    1. Execute:
        $ sudo fdisk /dev/sda
    using whatever hard disk is appropriate, and create the two partitions. While in fdisk, typing t will let you set the partition type to 8e. While it doesn’t matter if you don’t set the type, it is a good idea to lessen confusion. Use w to rewrite the partition table and exit, and then
        $ sudo partprobe -s
    or reboot to make sure the new partitions take effect.
    ```
    2. Convert the partitions to physical volumes.
    ```
    2. Assuming the new partitions are /dev/sdaX and /dev/sdaY:
        $ sudo pvcreate /dev/sdaX
        $ sudo pvcreate /dev/sdaY
        $ sudo pvdisplay
    ```
    3. Create a volume group named myvg and add the two physical volumes to it. Use the default extent size.
    ```
    3. $ sudo vgcreate myvg /dev/sdaX /dev/sdaY
    $ sudo vgdisplay
    ```
    4. . Allocate a 300 MB logical volume named mylvm from volume group myvg.
    ```
    4. $ sudo lvcreate -L 300M -n mylvm myvg
    $ sudo lvdisplay
    ```
    5. Format and mount the logical volume mylvm at /mylvm
    ```
    5. $ sudo mkfs.ext4 /dev/myvg/mylvm
    $ sudo mkdir /mylvm
    $ sudo mount /dev/myvg/mylvm /mylvm
    If you want the mount to be persistent, edit /etc/fstab to include the line:
    ```
    6. Use lvdisplay to view information about the logical volume.
    ```
    6. $ sudo lvdisplay
    ```
    7. Grow the logical volume and corresponding filesystem to 350 MB.
    ```
    7. $ df -h
    $ sudo lvresize -r -L 350M /dev/myvg/mylvm
    $ df -h
    or
    $ sudo lvresize -r -L +50M /dev/myvg/mylvm
    or with older methods you can do:
    $ df -h
    $ sudo lvextend -L 350M /dev/myvg/mylvm
    $ sudo resize2fs /dev/myvg/mylvm
    $ df -h
    ```

### Ch.24
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


### Lab 24.1
    1. Create two 200 MB partitions of type raid (fd) either on your hard disk using fdisk, or using LVM.
    ```
    1. If you need to create new partitions do:
        $ sudo fdisk /dev/sda
    and create the partitions as we have done before. For purposes of being definite, we will call them /dev/sdaX and /dev/sdaY. You will need to run partprobe or kpartx or reboot after you are done to make sure the system is properly aware of the new partitions.
    ```
    2. Create a RAID 1 device named /dev/md0 using the two partitions.
    ```
    2. $ sudo mdadm -C /dev/md0 --level=1 --raid-disks=2 /dev/sdaX /dev/sdaY
    ```
    3. Format the RAID device as an ext4 filesystem. Then mount it at /myraid and make the mount persistent.
    ```
    3. $ sudo mkfs.ext4 /dev/md0
    $ sudo mkdir /myraid
    $ sudo mount /dev/md0 /myraid
    and add to /etc/fstab
    ```
    4. Place the information about /dev/md0 in /etc/mdadm.conf file using mdadm. (Depending on your distribution, this file may not previously exist.)
    ```
    4. $ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"
    ```
    5. Examine /proc/mdstat to see the status of your RAID device.
    ```
    5. $ cat /proc/mdstat
        Personalities : [raid1]
        md0 : active raid1 dm-14[1] dm-13[0]
            204736 blocks [2/2] [UU]
        unused devices: <none>
    ```
### Extras
* 18.10: mount a filesystem by UUID
* 18.13: mount a network filesystem
* Lab 20.1: defragmentation
* Lab 22.1: disk encryption w/ cryptsetup
* Lab 22.2: encrypted swap w/ cryptsetup

