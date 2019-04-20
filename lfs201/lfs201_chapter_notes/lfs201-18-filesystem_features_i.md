Chapter 18: Filesystem Features I: Attributes, Creating, Checking, Mounting
---------------------------------------------------------------------------

[18.3: Learning Objectives](#183-learning-objectives)  
[18.4: Extended Attributes and lsattr/chattr](#184-extended-attributes-and-lsattrchattr)  
[18.5: Creating and Formatting Filesystems](#185-creating-and-formatting-filesystems)  
[18.6: Checking and Repairing Filesystems](#186-checking-and-repairing-filesystems)  
[18.7: Mounting Filesystems](#187-mounting-filesystems)  
[18.10: mount](#1810-mount)  
[18.11: mount Options](#1811-mount-options)  
[18.12: umount](#1812-umount)  
[18.13: Network Shares (NFS)](#1813-network-shares-nfs)  
[18.14: Mounting at Boot](#1814-mounting-at-boot)  
[18.15: Automatic Filesystem Mounting](#1815-automatic-filesystem-mounting)  
[18.16: automount Example](#1816-automount-example)  
[18.17: Listing Currently Mounted Filesystems](#1817-listing-currently-mounted-filesystems)  
[18.18: Filesystems Demo](#1818-filesystems-demo)  
[Lab 18.1: Working with File Attributes](#lab-181-working-with-file-attributes)  
[Lab 18.2: Mounting Options](#lab-182-mounting-options)  
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
* `$ chattr [+|-|=mode] filename` generic **chattr** format
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
* multiple partitions are commonly joined together in the same filesystem tree
* **mount** allows attachment at any point in the tree structure
* **unmount** allows detaching them
* the **mount point** is the directory where the filesystem is attached; it must exist already
   * files in the existing directory will be hidden until the mounted FS is unmounted
* `$ sudo mount -t ext /dev/sdb4 /home` mount an ext4 filesystem on the `/dev/sdb4` partition at the position `/home`  
? how would this look if it was an external drive or netowrk? maybe `/dev/sdb4` is the thing to be mounted

### 18.10: mount
----
* `$ mount [options] <source> <directory>` generic **mount** format
* mounting can use the filesystem's device node, label, or UUID
* equivalent examples:
   * `$ sudo mount /dev/sda2 /home`
   * `$ sudo mount LABEL=home /home`
   * `$ sudo mount -L home /home`
   * `$ sudo mount UUID=26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home`
   * `$ sudo mount -U 26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home`
* **UUIDs** are assigned when partitions are created as contatiners for the filesystem and formatted with **mkfs**
* **UUIDs** are best to use because they should always be unique

### 18.11: mount Options
----
* **mount** has many options and a long **man** page
* `$ sudo mount -o remount,ro /myfs`
   * **-o remount,ro** remounts a FS with a read-only attribute
   * **-a** mounts all filesytems in `/etc/fstab`, as in `$ mount -a`
* `$ cat /etc/fstab`
   ```
   #
   # /etc/fstab
   # Created by anaconda on Thu May 10 11:54:58 2018
   #
   # Accessible filesystems, by reference, are maintained under '/dev/disk'
   # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
   #
   UUID=76e6adc1-cbce-4eb4-82b8-0f89f42f82de /                       ext4    defaults        1 1
   /dev/sda2 swap                    swap    defaults        0 0
   ```
* let's take a look at `/dev/disk`
   * `$ ll /dev/disk`
      ```
      total 0
      drwxr-xr-x 2 root root  60 Jan 18 03:38 by-id
      drwxr-xr-x 2 root root 120 Jan 18 03:38 by-path
      drwxr-xr-x 2 root root  80 Jan 18 03:38 by-uuid
      ```
   * `$ ll by-path/`
      ```
      total 0
      lrwxrwxrwx 1 root root  9 Jan 18 03:38 pci-0000:00:07.1-ata-2.0 -> ../../sr0
      lrwxrwxrwx 1 root root  9 Apr 13 11:53 pci-0000:00:10.0-scsi-0:0:0:0 -> ../../sda
      lrwxrwxrwx 1 root root 10 Apr 13 11:53 pci-0000:00:10.0-scsi-0:0:0:0-part1 -> ../../sda1
      lrwxrwxrwx 1 root root 10 Apr 13 11:53 pci-0000:00:10.0-scsi-0:0:0:0-part2 -> ../../sda2
      ```
   * `$ ll by-id/`
      ```
      total 0
      lrwxrwxrwx 1 root root 9 Jan 18 03:38 ata-VMware_Virtual_IDE_CDROM_Drive_10000000000000000001 -> ../../sr0
      ```
   * `$ ll by-uuid/`
      ```
      total 0
      lrwxrwxrwx 1 root root 10 Apr 13 11:53 76e6adc1-cbce-4eb4-82b8-0f89f42f82de -> ../../sda1
      lrwxrwxrwx 1 root root 10 Apr 13 11:53 829c9ec3-8178-4cae-abec-9e723755552e -> ../../sda2
      ```
* `$ mount --help` shows a quick summary of options
  
### 18.12: umount
----
* `$ umount [device-file | mount-point]` generic umount syntax
* `$ sudo umount /home` unmount the `/home` filesystem
* `$ sudo umount /dev/sda3` unmount the `/dev/sda3` device
* use the **man** pages for specific option information
* this cannot be done on a filesystem currently in use or you'll get a **device is busy** error
   * can with a terminal window open in a directory on the mounted filesystem or a process using files on the filesystem
   * kill the terminal windows and / or processes
   * use **fuser** to see which users are using the FS
   * use **lsof** ("list open files") to see which are being used
      * use **vim** to open a file on my desktop
      * `$ lsof | grep LF_`
         > vim       74774              student    4u      REG                8,1     12288   1566818 /home/student/Desktop/.LF_VM_info.txt.swp

### 18.13: Network Shares (NFS)
----
* **NFS** (**N**etwork **F**ile **S**ystem) historically the most common method used to mount remote filesystems as if they were on the local machine
   * developed by Sun Microsystems in 1989
   * modern systems use NFSv4 since 2000
   * others include **AFS** (Andrew File System) and **SMB** (Server Message Book), also termed **CIFS** (Common Internet File System)
* systems must be prepared for a network filesystem to be unavailable, so it needs to know not to hang after waiting longer than a certain period
   * `$ sudo mount -t nfs myserver.com:/shdir /mnt/shdir`
   * or put the following line in `/etc/fstab` to mount on boot or with `mount -a`:  
      > `myserver.com:/shdir /mnt/shdir nfs rsize=8192,wsize=8192,timeo=14,intr 0 0`  
   * **netdev** and **noauto** options can prevent the system from trying to mount the **NFS** before the network is up
   * **autofs** or **automount** can also be used
  
### 18.14: Mounting at Boot
----
* `$ mount -a` runs during system initialization
* `/etc/fstab` shows
   * what filesystems may be automatically mounted at boot
   * where they may be found on the local machine or network
   * who may mount them and with what permissions
   * device node, label, or UUID (except for filesystems which do not have a device node, such as **tmpfs**, **proc**, and **sysfs**)
   * mount point; can also be a place holder, like for swap, which is not mounted anywhere
   * filesystem type (i.e., ext4, xfs, btrfs, vfat)
   * a comma-separated list of options
   * **dump** frequency (or a 0); used by the rarely used dump -w command
   * **fsck** pass number (or 0, meaning do not check state at boot)
* **mount** and **umount** can use information in `/etc/fstab`
   * `$ sudo mount /usr/src`
   * `$ sudo mount LABEL=src /usr/src`
  
### 18.15: Automatic Filesystem Mounting
----
* **autofs** can be used to mount a filesystem only when needed
* **systemd**-based systems use **automount**, which can configured with a line in `/etc/fstab`:
   * `LABEL=Sam128 /SAM ext4 noauto,x-systemd.automount,x-systemd.device-timeout=10,x-systemd.idle-timeout=30 0 0` and then rebooting or running:
   * `$ sudo systemctl daemon-reload` and `$ sudo systemctl restart local-fs.target`
  
### 18.16: automount Example
----
* entry in `/etc/fstab` for USB pen drive always plugged in but only mounted when used:
   > `LABEL=Sam128   /SAM  ext4  noauto,x-systemd.automount,x-systemd.device-timeout=10,x-systemd.idle-timeout=30 0 0`  
   * **noauto**: Do not mount at boot. Here, **auto** does not refer to **automount**.
   * **x-systemd.automount**: Use the **systemd automount** facility.
   * **x-systemd.automount.device-timeout=10**: If this device is not available, say it is a network device accessible through NFS, timeout after 10 seconds instead of getting hung.
   * **x-systemd.automount.idle-timeout=30**: If the device is not used for 30 seconds, unmount it.

### 18.17: Listing Currently Mounted Filesystems
----
* `$ mount` shows currently mounted filessytems
* output on my machine:
```
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
devtmpfs on /dev type devtmpfs (rw,nosuid,size=1915080k,nr_inodes=478770,mode=755)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,mode=755)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct,cpu)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_prio,net_cls)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
configfs on /sys/kernel/config type configfs (rw,relatime)
/dev/sda1 on / type ext4 (rw,relatime,data=ordered)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=24,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=24321)
mqueue on /dev/mqueue type mqueue (rw,relatime)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw,relatime)
tmpfs on /run/user/42 type tmpfs (rw,nosuid,nodev,relatime,size=386152k,mode=700,uid=42,gid=42)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=386152k,mode=700,uid=1000,gid=1000)
gvfsd-fuse on /run/user/1000/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,relatime,user_id=1000,group_id=1000)
fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,relatime)
```

### 18.18: Filesystems Demo
----
**As root**:
* `# fdisk -l /dev/sda`: see what's on disk
* `# cat /proc/swaps`: shows a swapfile (not a swap partition?)
* `# ls -lh /swapfile`: shows swapfile
* `# fdisk /dev/sda`: options chosen: n, p, 2, first sector, +10G, p, w
* `# partprobe -s`: read in new partition table
* `# ls -l /dev/sda*`: shows new partition, `/dev/sda2`
* `# mkfs.ext4 /dev/sda2`: make a filesystem on the partition
* `# mount /dev/sda2 /mnt`: mount the filesystem
* `# df -h -T`: show the type of filesystems mounted
* `# dumpe2fs /dev/sda2 | less`: get more information; my 'Mount count' is 31; my 'Maximum mount count' is -1
* `# fsck /dev/sda2`: check new filesystem
* `# umount /mnt`: unmount the filesystem
* `# fsck /dev/sda2`: check again
* `# fsck -f /dev/sda2`: checks inodes, directory structure, etc.
  
### Lab 18.1: Working with File Attributes
----
### Lab 18.2: Mounting Options
----
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
partitions | `/dev/disk` | contains informaiton about system partitions | LFS201 18.11
filesystem, partitions | `/etc/fstab` | configuration file containing the necessary information to automate the process of mounting partitions | LFS201 18.11
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
files | `$ chattr [+|-|=mode] filename` | change attributes for a file | LFS201 18.4
files | `$ lsattr <filename>` | display attributes for a file | LFS201 18.4
filesystem | `$ mkfs [-t fstype] [options] [device-file]` | generic **mkfs** syntax | LFS201 18.5
filesystem | `$ sudo mkfs -t ext4 /dev/sda10` | make an ext4 fs on `/dev/sda10` | LFS201 18.5
filesystem | `$ sudo mkfs.ext4 /dev/sda10` | make an ext4 fs on `/dev/sda10` | LFS201 18.5
filesystem | `$ fsck [-t fstype] [options] [device-file]` | generic **fsck** syntax | LFS201 18.6
filesystem | `$ sudo fsck -t ext4 /dev/sda10` | check ext4 filesystem on `/dev/sda10` | LFS201 18.6
filesystem | `$ sudo fsck.ext4 /dev/sda10` | check ext4 filesystem on `/dev/sda10` | LFS201 18.6
filesystem | `$ sudo fsck /dev/sda10`  | check filesystem on `/dev/sda10` | LFS201 18.6
filesystem | `$ sudo touch /forcefsck` then `$ sudo reboot` | force a check of mounted filesystems at boot, which allows **fsck** to check the root FS | LFS201 18.6
filesystem | `$ sudo mount -t ext /dev/sdb4 /home` | mount an ext4 filesystem on the `/dev/sdb4` partition at the position `/home` | LFS201 18.7
filesystem | `$ mount [options] <source> <directory>` | generic **mount** syntax | LFS201 18.10
filesystem | `$ sudo mount /dev/sda2 /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount LABEL=home /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount -L home /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount UUID=26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount -U 26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount -o remount,ro /myfs` | remounts a FS with a read-only attribute | LFS201 18.11
filesystem | `$ mount -a` | mount all partitions in `/etc/fstab` | LFS201 18.11
filesystem | `$ umount [device-file | mount-point]` | generic umount syntax | LFS201 18.12
filesystem | `$ sudo umount /home` | unmount the `/home` filesystem | LFS201 18.12
filesystem | `$ sudo umount /dev/sda3` | unmount the `/dev/sda3` device | LFS201 18.12
filesystem | `$ fuser [options]` | show which users are using the FS | LFS201 18.12
filesystem | `$ lsof` | list open files, shows which files are being used | LFS201 18.12
filesystem | `$ sudo mount -t nfs myserver.com:/shdir /mnt/shdir` | mount **NFS** so system won't hang when unavailable(?) | LFS201 18.13
filesystem | `$ autofs` | service countrol for the **automount** daemon | LFS201 18.13
filesystem | `$ automount` | manages **autofs** mount points | LFS201 18.13
filesystem | `$ sudo mount /usr/src` | ?? | LFS201 18.14
filesystem | `$ sudo mount LABEL=src /usr/src` | ?? | LFS201 18.14
filesystem | `$ sudo systemctl daemon-reload` and `$ sudo systemctl restart local-fs.target` | restart after editing `/etc/fstab` | LFS201 18.15
filesystem | `$ mount` | show currently mounted filesystems | LFS201 18.17
filesystem | `$ dumpe2fs <source>` | show detailed information about filesystem | LFS201 18.18
