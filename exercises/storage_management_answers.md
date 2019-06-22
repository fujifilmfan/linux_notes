Storage Management
-----

### Chapters: LFS101: 2, LFS201: 16, 17, 18, 19, 20, 22, 23, 24, 42


### du, df, lsmod, lsof, fsck, file, other
* calculate the overall size of each of your system’s top-level directories - `$ sudo du --max-depth=1 -hx` or `$ sudo du -cshx --exclude=/proc *` (less good)
* display disk usage for current directory - `$ du` 
* list all files, not just directories - `$ du -a` 
* display disk usage for a specific directory - `$ du -h somedir`
* show filesystem capacity and usage, human-readable, fs type, inodes instead of bytes - `$ df -hTi`
* show status of kernel modules - `$ lsmod | less`
* list available filesystems - `$ cat /proc/filesystems`
* check health of unmounted filesystem - `$ sudo fsck -t ext4 /dev/sda10` or `$ sudo fsck.ext4 /dev/sda10`
* automatically repair a filesystem - `$ sudo fsck -a /dev/sda10`
* interactively repair a filesystem - `$ sudo fsck -r /dev/sda10`
* mount a filesystem with a label (how to label a filesystem?) - `$ sudo mount LABEL=home /home` or `$ sudo mount -L home /home`
* view partitions by-id, by-path, by-uuid - `ls -al /dev/disk/...`
* mount all filesystems in `/etc/fstab` - `$ mount -a`
* list open files - `$ lsof`
* list partition table - `$ sudo fdisk -l /dev/sda`
* locate block devices and report on their attributes - `$ sudo blkid /dev/sda*`
* show block device information in a tree format - `$ lsblk`
* backup **MBR** on the first disk, including the 64-bit partition table - `$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1` 
* restore the partition table (not really) - `$ sudo dd if=mbrbackup of=/dev/sda bs=512 count=1`
* determine the type of a file - `$ file <filename>`
* try and read in a revised partition table - `sudo partprobe -s`
* show partitions the OS is aware of - `$ cat /proc/partitions`

### File attributes
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

* File flags:
   * **i**: Immutable  
    A file with the immutable attribute cannot be modified (not even by root). It cannot be deleted or renamed. No hard link can be created to it, and no data can be written to the file. Only the superuser can set or clear this attribute.  
    * **a**: Append-only  
    A file with the append-only attribute set can only be opened in append mode for writing. Only the superuser can set or clear this attribute.  
    * **d**: No-dump  
    A file with the no-dump attribute set is ignored when the dump program is run. This is useful for swap and cache files that you don't want to waste time backing up.  
    * **A**: No **atime** update  
    A file with the no-atime-update attribute set will not modify its atime (access time) record when the file is accessed but not otherwise modified. This can increase the performance on some systems because it reduces the amount of disk I/O on the system.

### Filesystem quotas
* change the entry in `/etc/fstab` for your new filesystem to use user quotas (change noexec to usrquota (or grpquota) in the entry for /mnt/tempdir) - `/dev/sda11 /mnt/tempdir ext4 usrquota 1 2` or `/imagefile  /mnt/tempdir ext4 loop,usrquota 1 2`
* remount the filesystem - `$ sudo mount -o remount /mnt/tempdir`
* initialize quotas on the new filesystem  (verbose)- `$ sudo quotacheck -vu /mnt/tempdir`
* check for `/aquoata.user` and `/aquota.group`
* (initialize quotas for all users on the filesystem) - `$ sudo quotacheck -ua`
* (initialize quotas for all groups on the filesystem) - `$ sudo quotacheck -ga`
* turn the quota checking system on (verbose)- `$ sudo quotaon -vu /mnt/tempdir`, `$ sudo chown student.student /mnt/tempdir` (not usually necessary)
* set some quota limits for the normal user account: a soft limit of 500 blocks and a hard limit of 1000 blocks - `$ sudo edquota -u student`
* set some quota limits for a group account - `$ sudo edquota -g <groupname>`
* set a grace period for a user - `$ sudo edquota -t`
* as the normal user, attempt to use dd to create some files to exceed the quota limits; create bigfile1 (200 blocks) and bigfile2 (400 blocks); you should get a warning; why? - `$ cd /mnt/tempdir`, `$ dd if=/dev/zero of=bigfile1 bs=1024 count=200`, `$ quota`, `$ dd if=/dev/zero of=bigfile2 bs=1024 count=400`
* create bigfile3 (600 blocks) - `$ quota`, `$ dd if=/dev/zero of=bigfile3 bs=1024 count=600`, `$ quota`, `$ ls -l`
* eliminate the persistent mount line you inserted in `/etc/fstab`

* show current user quota - `$ quota` or `$ quota -u`
* show current group quota - `$ quota -g`
* show quota for any particular user or group - `$ sudo quota <username | group >`
* turn on all user and group quotas - `$ sudo quotaon -av`
* turn off all user and group quotas - `$ sudo quotaoff -av`
* turn on all user quotas - `$ sudo quotaon -avu`
* turn off all user quotas - `$ sudo quotaoff -avu`
* turn on all group quotas - `$ sudo quotaon -avg`
* turn off all group quotas - `$ sudo quotaoff -avg`

### Mount a new instance of tmpfs
* mount a new instance of tmpfs anywhere on your directory structure - `$ sudo mkdir /mnt/tmpfs`, `$ sudo mount -t tmpfs none /mnt/tmpfs`
* see how much space the filesystem has been given and how much it is using - `$ df -h /mnt/tmpfs`
* change the allotted size as a mount option - `$ sudo mount -t tmpfs -o size=1G none /mnt/tmpfs`
* unmount when you are done - `$ sudo umount /mnt/tmpfs`
* view the instance of tmpfs at `/dev/shm `- `$ df -h /dev/shm`
* create some files in `/dev/shm` and note how the filesystem is filling up with df
* view multiple instances of tmpfs - `$ df -h | grep tmpfs`

### Use a file as a disk partition / partition a disk image file
* create a file full of zeros 1 GB in length - `$ dd if=/dev/zero of=imagefile bs=1M count=1024`
* partition the disk image file - `$ sudo fdisk imagefile`
* put filesystems on the partitions - `$ mkfs.ext4 imagefile`
* create a mount point - `$ mkdir mntpoint`
* mount the filesystem
    * loop: `$ sudo mount -o loop imagefile mntpoint` OR
    * losetup: `$ sudo losetup /dev/loop2 imagefile`, `$ sudo mount /dev/loop2 mntpoint`
* unmount the filesystem
    * loop: `$ sudo umount mntpoint` OR
    * losetup: `$ sudo umount mntpoint`, `$ sudo losetup -d /dev/loop2`

### Using losetup and parted
* find which loop devices are already being used - `$ losetup -a`
* find first free loop device - `$ sudo losetup -f`
* associate the image file with a free loop device - `$ sudo losetup /dev/loop1 imagefile`
* create a disk partition label on the loop device (image file) - `$ sudo parted -s /dev/loop1 mklabel msdos`
* create three primary partitions on the loop device using parted - `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256`, `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512`, `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024`
* check the partition table - `$ fdisk -l /dev/loop1`
* list new device nodes - `$ ls -l /dev/loop1*`
* put filesystems on the partitions - `$ sudo mkfs.ext3 /dev/loop1p1`, `$ sudo mkfs.ext4 /dev/loop1p2`, `$ sudo mkfs.vfat /dev/loop1p3`
* mount all three filesystems and show they are available - `$ mkdir mnt1 mnt2 mnt3`, `$ sudo mount /dev/loop1p1 mnt1`, `$ sudo mount /dev/loop1p2 mnt2`, `$ sudo mount /dev/loop1p3 mnt3`, `$ df -Th`
* after using the filesystems to your heart’s content you can unwind it all - `$ sudo umount mnt1 mnt2 mnt3`, `$ rmdir mnt1 mnt2 mnt3`, `$ sudo losetup -d /dev/loop1`

### Mount a physical partition OR loopback file
* create a 250 MB partition
    * p: `$ sudo fdisk /dev/sda`, ..., `$ partprobe -s` OR
    * l: `$ sudo dd if=/dev/zero of=/imagefile bs=1M count=250`
* put a filesytem on the partition three times, changing the block size each time
    * p: `$ sudo mkfs -t ext4 -v /dev/sda11`, `$ sudo mkfs -t ext4 -b 2048 -v /dev/sda11`, `$ sudo mkfs -t ext4 -b 4096 -v /dev/sda11` or (`$ sudo mkfs.ext4 -b 4096 -v /dev/sda11`) OR
    * l: `$ sudo mkfs -t ext4 -v`, `$ sudo mkfs -t ext4 -b 2048 -v /imagefile`, `$ sudo mkfs -t ext4 -b 4096 -v /imagefile` (proceed past warning)
* create a new subdirectory and mount the new filesystem at this location, verify - `$ sudo mkdir /mnt/tempdir`
    * p: `$ sudo mount /dev/sda11 /mnt/tempdir` (`$ sudo mount -t ext4 /dev/sda11 /mnt/tempdir`), `$ mount | grep tempdir` OR
    * l: `$ sudo mount -o loop /imagefile /mnt/tempdir`, `$ mount | grep tempdir`
* unmount the new filesystem, and then remount it as read-only - `$ sudo umount /mnt/tempdir`
    * p: `$ sudo mount -o ro /dev/sda11 /mnt/tempdir` (`$ sudo mount -o remount,ro /dev/sda11 /mnt/tempdir`) OR
    * l: `$ sudo mount -o ro,loop /imagefile /mnt/tempdir`
* try to create a file in the mounted directory - `$ sudo touch /mnt/tempdir/afile` (gives an error)
* unmount the filesystem again - `$ sudo umount /mnt/tempdir`
* add a line to your `/etc/fstab` file so that the filesystem will be mounted at boot time
    * p: `/dev/sda11 /mnt/tempdir ext4 defaults 1 2` OR
    * l: `/imagefile /mnt/tempdir ext4 defaults 1 2`
* mount the filesystem - `$ sudo mount /mnt/tempdir`, `$ sudo mount | grep tempdir`
* modify the configuration for the new filesystem so that binary files may not be executed from the filesystem
    * unmount and remount OR to make persistent:
    * p: `/dev/sda11 /mnt/tempdir ext4 noexec 1 2` OR
    * l: `/imagefile /mnt/tempdir ext4 loop,noexec 1 2`
* remount the filesystem and copy an executable file (such as /bin/ls) to /mnt/tempdir and try to run it - `$ sudo mount -o remount /mnt/tempdir`, `$ sudo cp /bin/ls /mnt/tempdir`, `$ /mnt/tempdir/ls` (gives an error)

### tune2fs
* create a formatted ext4 filesystem - image file or another partition with a filesystem that can be modified (`$ dd ...`, `$ mkfs.ext4 imagefile`)
* display the maximum mount count setting (after which a filesystem check will be forced), the Check interval (the amount of time after which a filesystem check is forced), the number of blocks reserved, and the total number of blocks - `$ dumpe2fs imagefile > dumpe2fs-output-initial`, `$ grep -i -e "Mount count" -e "Check interval" -e "Block Count" dumpe2fs-output-initial`
* list the contents of the superblock, including the current values of parameters which can be changed - `$ sudo tune2fs -l /dev/sda1`
* change the maximum count to 30 - `$ tune2fs -c 30 imagefile`
* change the Check interval to three weeks - `$ tune2fs -i 3w imagefile`
* change the percentage of blocks reserved to 10 percent - `$ tune2fs -m 10 imagefile`
* once again obtain information about the filesystem and compare with the original output - `$ dumpe2fs imagefile > dumpe2fs-output-final`, `$ grep -i  -e "Mount count" -e "Check interval" -e "Block Count" dumpe2fs-output-final`, ` $ diff dumpe2fs-output-initial dumpe2fs-output-final`

### Logical volumes
* create two 250 MB partitions of type logical volume (8e) - `$ sudo fdisk /dev/sda` (press t)
* convert the partitions to physical volumes - `$ sudo pvcreate /dev/sdaX`, `$ sudo pvcreate /dev/sdaY`, `$ sudo pvdisplay` OR `$ sudo lvm` and then `lvm> pvcreate /dev/sda3`
* create a volume group named myvg and add the two physical volumes to it; use the default extent size; verify - `$ sudo vgcreate myvg /dev/sdaX /dev/sdaY` (can use -s to set physical extent size as in -s 16M), `$ sudo vgdisplay`
* (add a third PV to the VG - `$ sudo vgextend vg /dev/sdc1`)
* allocate a 300 MB logical volume named mylvm from volume group myvg, verify - `$ sudo lvcreate -L 300M -n mylvm myvg`, `$ sudo lvdisplay`
* format and mount the logical volume mylvm at /mylvm - `$ sudo mkfs.ext4 /dev/myvg/mylvm`, `$ sudo mkdir /mylvm`, `$ sudo mount /dev/myvg/mylvm /mylvm`
* make mount persistent - `/dev/myvg/mylvm /mylvm ext4 defaults 0 0`
* view information about the logical volume (notice that open is now '1') - `$ sudo lvdisplay`
* check df and then grow the logical volume and corresponding filesystem to 350 MB - `$ df -h`, `$ sudo lvresize -r -L 350M /dev/myvg/mylvm` or `$ sudo lvresize -r -L +50M /dev/myvg/mylvm`, or `$ sudo lvextend -L 350M /dev/myvg/mylvm`, `$ sudo resize2fs /dev/myvg/mylvm`, `$ df -h` (-r for --resizefs (filesystem at same time), -L for --size)
* shrink the filesystem to 250M

### Resizing volumes
* shrink the filesystem:
    * `$ sudo umount /mylvm`
    * `$ sudo fsck -f /dev/vg/mylvm`
    * `$ sudo resize2fs /dev/vg/mylvm 200M`
    * `$ sudo lvreduce -L 200M /dev/vg/mylvm`
    * `$ sudo mount /dev/vg/mylvm`
* recent versions of the **lvm** utilities allow one to expand or shrink with one step instead of two (instead of using resize2fs)
    * `$ sudo lvextend -r -L +100M /dev/vg/mylvm`
    * `$ sudo lvreduce -r -L -100M /dev/vg/mylvm`
* reduce a volume group:
    * `$ sudo pvmove /dev/sdc1`
    * `$ sudo vgreduce vg /dev/sdc1`

### RAID
* create two 200 MB partitions of type raid (fd) either on your hard disk using fdisk, or using LVM
* create a RAID 1 device named `/dev/md0` using the two partitions - `$ sudo mdadm -C /dev/md0 --level=1 --raid-disks=2 /dev/sdaX /dev/sdaY` (-C or --create)
* format the RAID device as an ext4 filesystem, mount it at `/myraid`, and make the mount persistent - `$ sudo mkfs.ext4 /dev/md0`, `$ sudo mkdir /myraid`, `$ sudo mount /dev/md0 /myraid`, 
    and add to `/etc/fstab`: `/dev/md0 /myraid ext4 defaults 0 0`
* place the information about `/dev/md0` in `/etc/mdadm.conf` file using mdadm - `$ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"` (-c: read commands from string) (switch order with mounting?)
* monitor RAID device - `$ cat /proc/mdstat` or `$ sudo mdadm --detail /dev/md0`
* start service to monitor RAID device - `$ sudo systemctl start mdmonitor`
* ensure RAID monitor starts at boot - `$ sudo systemctl enable mdmonitor`
* stop RAID device - `$ sudo mdadm -S /dev/md0`

### Disk mounting quiz
When adding a new internal disk to an existing server, in which best logical order should the following steps be executed to have the disk persistently mounted after rebooting the system?
1. Create a new partition
2. Format with a filesystem
3. Edit `/etc/fstab`
4. Reboot the system

### Quota quiz
Organize the steps below in the best logical order to enable quota on an existing filesystem:
1. Edit `/etc/fstab` to add **usrquota** mount option.
2. Remount the filesystem.
3. Run **quotaon** to enable quota.
4. Edit individual user or group quota with **edquota**.

### RAID quiz
You added two new disks to a server. In which order would you use the following commands to create a RAID device with `/dev/sdb1` and `/dev/sdc1`, and then make the RAID device available using an ext4 filesystem?
1. `$ fdisk /dev/sdb; fdisk /dev/sdc`
2. `$ mdadm --create /dev/md0 ... /dev/sdb1 /dev/sdc1`
3. `$ mkfs.ext4 /dev/md0`
4. `$ mdadm --detail --scan >> /etc/mdadm.conf`
5. `$ reboot`

### Extras
* 18.10: mount a filesystem by UUID
* 18.13: mount a network filesystem
* 19.10: Setting Quotas
   * copy userproto's user quota values to username - `$ edquota -u -p [userproto] [username]`
   * copy groupproto's group quota values to groupname - `$ edquota -g -p [groupproto] [groupname]`
* Lab 20.1: defragmentation
* Lab 22.1: disk encryption w/ cryptsetup
* Lab 22.2: encrypted swap w/ cryptsetup
* 23: LVM Snapshots
    * create a snapshot of an existing logical volume - `$ sudo lvcreate -l 128 -s -n mysnap /dev/vg/mylvm`
    * make a mount point - `$ mkdir /mysnap`
    * mount the snapshot - `$ mount -o ro /dev/vg/mysnap /mysnap`
    * unmount the snapshot - `$ sudo umount /mysnap`
    * remove the snapshot - `$ sudo lvremove /dev/vg/mysnap`
* 24.8: RAID Hot Spares
    * create a hot spare when creating the RAID array (** -x 1** switch) - `$ sudo mdadm --create /dev/md0 -l 5 -n3 -x 1 /dev/sda8 /dev/sda9 /dev/sda10 /dev/sda11`
    * test redundancy and hot spare of the array - `$ sudo mdadm --fail /dev/md0 /dev/sdb2`
    * remove faulty drive in test or failure situation - `$ sudo mdadm --remove /dev/md0 /dev/sdb2`
    * add new drive in test or failure situation - `$ sudo mdadm --add /dev/md0 /dev/sde2`
