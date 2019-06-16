Storage Management
------------------


### Ch.16
* list available filesystems - `$ cat /proc/filesystems`
* mount an xfs filesystem using the loopback mechanism and create a container for the filesystem:
   * `$ dd if=/dev/zero of=junk bs=1M count=512` ('if' seems to be input file and 'of' output file)
   * makes a 512MB file of zeros
   * `$ sudo /sbin/mkfs.xfs junk` (maybe 'mkfs' stands for MaKe FileSystem)
   * `$ sudo mount junk /mnt` no need for '-o loop'
   * `$ df -h`
   * `$ cat /proc/filesystems` has xfs
   * `$ lsmod | less` shows the xfs module loaded
* Lab 16.1
    * Mount a new instance of tmpfs anywhere on your directory structure with a command like:
    ```
    $ sudo mkdir /mnt/tmpfs
    $ sudo mount -t tmpfs none /mnt/tmpfs
    ```
    * See how much space the filesystem has been given and how much it is using:
    ```
    $ df -h /mnt/tmpfs
    ```
    * You should see it has been allotted a default value of half of your RAM; however, the usage is zero, and will only start to grow as you place files on /mnt/tmpfs. You could change the allotted size as a mount option as in:
    ```
    $ sudo mount -t tmpfs -o size=1G none /mnt/tmpfs
    ```
    * You might try filling it up until you reach full capacity and see what happens. Do not forget to unmount when you are done with:
    ```
    $ sudo umount /mnt/tmpfs
    ```
    * Virtually all modern Linux distributions mount an instance of tmpfs at /dev/shm:
    ```
    $ df -h /dev/shm
    Filesystem     Type   Size  Used Avail Use% Mounted on
    tmpfs          tmpfs  3.9G   24M  3.9G   1% /dev/shm
    ```
    * Many applications use this such as when they are using POSIX shared memory as an inter-process communication mecha- nism. Any user can create, read and write files in /dev/shm, so it is a good place to create temporary files in memory.
    Create some files in /dev/shm and note how the filesystem is filling up with df.
    In addition, many distributions mount multiple instances of tmpfs; for example, on a RHEL 7 system:
    ```
    $ df -h | grep tmpfs
    ```
    * Notice this was run on a system with 8 GB of ram, so clearly you can’t have all these tmpfs filesystems actually using the 4 GB they have each been allotted!

### Ch.17
* `$ sudo fdisk -l /dev/sda` list partition table
* **blkid** is a utility to locate block devices and report on their attributes
   * it works with the **libblkid** library
   * example: `$ sudo blkid /dev/sda*`
* **lsblk** presents block device information in a tree format
* `$ lsblk`
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
* `sudo partprobe -s` try and read in the revised partition table (not recommended)
* `$ cat /proc/partitions` shows partitions the OS is aware of; for me:
   ```
   major minor  #blocks  name

   8        0   41943040 sda
   8        1   28319744 sda1
   8        2     975872 sda2
   11        0    1048575 sr0
   ```
When adding a new internal disk to an existing server, in which best logical order should the following steps be executed to have the disk persistently mounted after rebooting the system?
1. Create a new partition
2. Format with a filesystem
3. Edit `/etc/fstab`
4. Reboot the system
I got it right. :)  


* Lab 17.1
    1. Create a file full of zeros 1 GB in length:
    ```
        $ dd if=/dev/zero of=imagefile bs=1M count=1024
    You can make a much smaller file if you like or do not have that much available space in the partition you are creating the file on.
    ```
    2. Put a filesystem on it:
    ```
        $ mkfs.ext4 imagefile
        mke2fs 1.42.9 (28-Dec-2013)
        imagefile is not a block special device.
        Proceed anyway? (y,n) y
        Discarding device blocks: done
        .....
    ```
    Of course you can format with a different filesystem, doing mkfs.ext3, mkfs.vfat, mkfs.xfs etc.
    3. Mount it somewhere:
    ```
        $ mkdir mntpoint
        $ sudo mount -o loop imagefile mntpoint
    You can now use this to your heart’s content, putting files etc. on it.
    ```
    4. When you are done unmount it with:
    ```
        $ sudo umount mntpoint
    ```
    5. An alternative method to using the loop option to mount would be:
    ```
    $ sudo losetup /dev/loop2 imagefile
    $ sudo mount /dev/loop2 mntpoint
    ....
    $ sudo umount mntpoint
    $ sudo losetup -d /dev/loop2
    ```
* Lab 17.2
    1. Run fdisk on your imagefile
    2. Type m to get a list of commands
    3. Create a new primary partition and make it 256 MB (or whatever size you would like
    4. Add a second primary partition also of 256 MB in size
    5. Write the partition table to disk and exit
* Lab 17.3
    1. Associate the image file with a loop device:
    ```
    $ sudo losetup -f
    /dev/loop1
        $ sudo losetup /dev/loop1 imagefile
    where the first command finds the first free loop device. The reason to do this is you may already be using one or more loop devices. For example, on the system that this is being written on, before the above command is executed:
    $ losetup -a
        /dev/loop0: []: (/usr/src/KERNELS.sqfs)
    a squashfs compressed, read-only filesystem is already mounted using /dev/loop0. (The output of this command will vary with distribution.) If we were to ignore this and use losetup on /dev/loop0 we would almost definitely corrupt the file.
    ```
    2. Create a disk partition label on the loop device (image file):
    ```
        $ sudo parted -s /dev/loop1 mklabel msdos
    ```
    3. Create three primary partitions on the loop device:
    ```
        $ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256
        $ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512
        $ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024
    ```
    4. Check the partition table:
    ```
        $ fdisk -l /dev/loop1
        Disk /dev/loop1: 1073 MB, 1073741824 bytes, 2097152 sectors
        Units = sectors of 1 * 512 = 512 bytes
        Sector size (logical/physical): 512 bytes / 512 bytes
        I/O size (minimum/optimal): 512 bytes / 512 bytes
        Disk label type: dos
        Disk identifier: 0x00050c11
            Device Boot      Start         End      Blocks   Id  System
        /dev/loop1p1               1      500000      250000   83  Linux
        /dev/loop1p2          500001     1000000      250000   83  Linux
        /dev/loop1p3         1000001     2000000      500000   83  Linux
    ```
    5. What happens next depends on what distribution you are on. For example, on RHEL 7 and Ubuntu 16.04 you will find new device nodes have been created:
    ```
    $ ls -l /dev/loop1*
        brw-rw---- 1 root disk   7, 1 Oct  7 14:54 /dev/loop1
        brw-rw---- 1 root disk 259, 0 Oct  7 14:54 /dev/loop1p1
        brw-rw---- 1 root disk 259, 3 Oct  7 14:54 /dev/loop1p2
        brw-rw---- 1 root disk 259, 4 Oct  7 14:54 /dev/loop1p3
    and we will use them in the following.
    ```
    6. Put filesystems on the partitions:
    ```
        $ sudo mkfs.ext3 /dev/loop1p1
        $ sudo mkfs.ext4 /dev/loop1p2
        $ sudo mkfs.vfat /dev/loop1p3
    ```
    7. Mount all three filesystems and show they are available:
    ```
        $ mkdir mnt1 mnt2 mnt3
        $ sudo mount /dev/loop1p1 mnt1
        $ sudo mount /dev/loop1p2 mnt2
        $ sudo mount /dev/loop1p3 mnt3
    $ df -Th
    Filesystem
    /dev/sda1
    ....
    /dev/loop1p1
    /dev/loop1p2
    /dev/loop1p3
    Type Size Used Avail Use% Mounted on ext4 29G 8.5G 19G32%/
    ext3      233M  2.1M  219M   1% mnt1
    ext4      233M  2.1M  215M   1% mnt2
    vfat 489M 0489M 0%mnt3
    ```
    8. After using the filesystems to your heart’s content you can unwind it all:
    ```
    $ sudo umount mnt1 mnt2 mnt3
    $ rmdir mnt1 mnt2 mnt3
    $ sudo losetup -d /dev/loop1
    ```

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





* Lab 18.1
    1. With your normal user account use touch to create an empty file named /tmp/appendit.
    ```
    1. $ cd /tmp
    $ touch appendit $ ls -l appendit
        -rw-rw-r-- 1 coop coop 0 Oct 23 19:04 appendit
    ```
    2. Use cat to append the contents of /etc/hosts to /tmp/appendit.
    ```
    2. $ cat /etc/hosts > appendit
    ```
    3. Compare the contents of /tmp/appendit with /etc/hosts; there should not be any differences.
    ```
    3. $ diff /etc/hosts appendit
    ```
    4. Try to add the append-only attribute to /tmp/appendit by using chattr. You should see an error here. Why?
    ```
    4. $ chattr +a appendit
    chattr: Operation not permitted while setting flags on appendit
    ```
    5. As root, retry adding the append-only attribute; this time it should work. Look at the file’s extended attributes by using lsattr.
    ```
    5. $ sudo chattr +a appendit $ lsattr appendit
        -----a-------e-- appendit
    ```
    6. As a normal user, try and use cat to copy over the contents of /etc/passwd to /tmp/appendit. You should get an error. Why?
    ```
    6. $ cat /etc/passwd > appendit
    bash: appendit: Operation not permitted
    ```
    7. Try the same thing again as root. You should also get an error. Why?
    ```
    7. $ sudo su
    $ cat /etc/passwd > appendit
        bash: appendit: Operation not permitted
    $ exit
    ```
    8. As the normal user, again use the append redirection operator (>>) and try appending the /etc/passwd file to /tmp/ appendit. This should work. Examine the resulting file to confirm.
    ```
    8.
    $ cat /etc/passwd >> /tmp/appendit
    $ cat appendit
    ```
    9. As root, set the immutable attribute on /tmp/appendit, and look at the extended attributes again.
    ```
    9. $ sudo chattr +i appendit
    $ lsattr appendit
    ----ia-------e- appendit
    ```
    10. Try appending output to /tmp/appendit, try renaming the file, creating a hard link to the file, and deleting the file as both the normal user and as root.
    ```
    10. $ echo hello >> appendit
    -bash: appendit: Permission denied
        $ mv appendit appendit.rename
        mv: cannot move `appendit' to `appendit.rename': Operation not permitted
        $ ln appendit appendit.hardlink
        ln: creating hard link `appendit.hardlink' => `appendit': Operation not permitted
        $ rm -f appendit
        rm: cannot remove `appendit': Operation not permitted
        $ sudo su
        $ echo hello >> appendit
        -bash: appendit: Permission denied
        $ mv appendit appendit.rename
        mv: cannot move `appendit' to `appendit.rename': Operation not permitted
        $ ln appendit appendit.hardlink
        ln: creating hard link `appendit.hardlink' => `appendit': Operation not permitted
        $ rm -f appendit
        rm: cannot remove `appendit': Operation not permitted
    $ exit
    ```
    11. We can remove this file by removing the extended attributes. Do so.
    ```
    $ sudo su
    $ lsattr appendit
        ----ia-------e- appendit
        $ chattr -ia appendit
        $ rm appendit
        rm: remove regular file `appendit'? y
    $ ls appendit
        ls: cannot access appendit: No such file or directory
    ```

* Lab 18.2
    1. Use fdisk to create a new 250 MB partition on your system, probably on /dev/sda. Or create a file full of zeros to use as a loopback file to simulate a new partition.
    2. Use mkfs to format a new filesystem on the partition or loopback file just created. Do this three times, changing the block size each time. Note the locations of the superblocks, the number of block groups and any other pertinent information, for each case.
    3. Create a new subdirectory (say /mnt/tempdir) and mount the new filesystem at this location. Verify it has been mounted.
    4. Unmount the new filesystem, and then remount it as read-only.
    5. Try to create a file in the mounted directory. You should get an error here, why?
    6. Unmount the filesystem again.
    7. Add a line to your /etc/fstab file so that the filesystem will be mounted at boot time.
    8. Mount the filesystem.
    9. Modify the configuration for the new filesystem so that binary files may not be executed from the filesystem (change defaults to noexec in the /mnt/tempdir entry). Then remount the filesystem and copy an executable file (such as /bin/ls) to /mnt/tempdir and try to run it. You should get an error: why?
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
* **virtual memory** (**overcommission**) functions in two ways:
   * many programs do not use all the memory they are permitted to use
   * memory pressure can cause less active memory regions to be swapped out to disk
   * lower priority swap areas are not used until higher priority areas are filled
* `$ cat /proc/swaps` check on swap areas; mine:
   ```
   Filename				Type		Size	Used	Priority
   /dev/sda2                               partition	975868	414500	-2
   ```
* `$ free -m` get basic memory statistics; mine:
   ```
                 total        used        free      shared  buff/cache   available
   Mem:           3771         838        1172          17        1759        2579
   Swap:           952         404         548
   ```
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
  
### 19.12: du: Disk Usage
----
* **du** (**d**isk **u**sage) is used to evaluate disk capacity and usage
   * `$ du` display disk usage for current directory
   * `$ du -a` list all files, not just directories
   * `$ du -h somedir` display disk usage for a specific directory
  
### 19.13 Knowledge Check 19.3
----
Organize the steps below in the best logical order to enable quota on an existing filesystem:
1. Edit `/etc/fstab` to add **usrquota** mount option.
2. Remount the filesystem.
3. Run **quotaon** to enable quota.
4. Edit individual user or group quota with **edquota**.


* Lab 19.1
    * Examine your current swap space by doing:
    ```
    $ cat /proc/swaps
    Filename                                Type
    /dev/sda11                              partition
    Size    Used
    4193776 0
    Priority -1
    ```
    * We will now add more swap space by adding either a new partition or a file. To use a file we can do:
    ```
    $ dd if=/dev/zero of=swpfile bs=1M count=1024
    1024+0 records in
    1024+0 records out
    1073741824 bytes (1.1 GB) copied, 1.30576 s, 822 MB/s
    $ mkswap swpfile
    Setting up swapspace version 1, size = 1048572 KiB
    no label, UUID=85bb62e5-84b0-4fdd-848b-4f8a289f0c4c
    (For a real partition just feed mkswap the partition name, but be aware all data on it will be erased!)
    ```
    * Activate the new swap space:
    ```
    $ sudo swapon swpfile
    swapon: /tmp/swpfile: insecure permissions 0664, 0600 suggested.
    swapon: /tmp/swpfile: insecure file owner 500, 0 (root) suggested.
    ```
    * Remove the swap file from use and delete it to save space:
    ```
    $ sudo swapoff swpfile
    $ sudo rm swpfile
    ```

* Lab 19.2
    1. Change the entry in /etc/fstab for your new filesystem to use user quotas (change noexec to usrquota in the entry for /mnt/tempdir). Then remount the filesystem.
    ```
    1. Change /etc/fstab to have one of the following two lines according to whether you are using a real partition or a loopback file:
    Then remount:
        $ sudo mount -o remount /mnt/tempdir
    ```
    2. Initialize quotas on the new filesystem, and then turn the quota checking system on.
    ```
    $ sudo quotacheck -u /mnt/tempdir
    $ sudo quotaon -u /mnt/tempdir
    $ sudo chown student.student /mnt/tempdir
    (You won’t normally do the line above, but we are doing it to make the next part easier).
    ```
    3. Now set some quota limits for the normal user account: a soft limit of 500 blocks and a hard limit of 1000 blocks.
    ```
    3. Substitute your user name for the student user account.
    ```
    4. As the normal user, attempt to use dd to create some files to exceed the quota limits. Create bigfile1 (200 blocks) and bigfile2 (400 blocks).
    You should get a warning. Why?
    ```
    4. $ sudo edquota -u student
    ```
    5. Create bigfile3 (600 blocks).
    You should get an error message. Why? Look closely at the file sizes.
    ```
    $ cd /mnt/tempdir
    $ dd if=/dev/zero of=bigfile1 bs=1024 count=200
        200+0 records in
        200+0 records out
        204800 bytes (205 kB) copied, 0.000349604 s, 586 MB/s
    $ quota
    Disk quotas for user student (uid 500): Filesystem blocks quota lim grace files qu lim gr /dev/sda11 200 500 1000 1 00
    $ dd if=/dev/zero of=bigfile2 bs=1024 count=400
    sda11: warning, user block quota exceeded.
    400+0 records in
    400+0 records out
    4096600 bytes (410 kB) copied, 0.000654847 s, 625 MB/s
    Create bigfile3 (600 blocks).
    ```
    6. Eliminate the persistent mount line you inserted in /etc/fstab.
    ```
    6. $ quota
    Disk quotas for user student (uid 500):
    Filesystem blocks quota limit grace files qu lim gr
    /dev/sda11    600*   500  1000 6days   2  0  0
    $ dd if=/dev/zero of=bigfile3 bs=1024 count=600
    sda11: write failed, user block limit reached.
    dd: writing `bigfile3': Disk quota exceeded
    401+0 records in
    400+0 records out
    409600 bytes (410 kB) copied, 0.00177744 s, 230 MB/s
    $ quota
    Disk quotas for user student (uid 500):
    Filesystem blocks  quota limit grace files quota limit grace
    /dev/sda11   1000*   500  1000 6days     3     0     0
    $ ls -l
    total 1068
    -rw------- 1 root    root      7168 Dec 10 18:56 aquota.user
    -rw-rw-r-- 1 student student 204800 Dec 10 18:58 bigfile1
    -rw-rw-r-- 1 student student 409600 Dec 10 18:58 bigfile2
    -rw-rw-r-- 1 student student 409600 Dec 10 19:01 bigfile3
    drwx------ 2 root    root     16384 Dec 10 18:47 lost+found
    -rwxr-xr-x 1 root    root     41216 Dec 10 18:52 more
    Look closely at the file sizes.
    7. Get rid of the line in /etc/fstab.
    ```

### Ch.20
* `$ sudo dumpe2fs /dev/sda1 | grep superblock` shows superblock locations
* **tune2fs** is used to change filesystem parameters
* `$ sudo tune2fs -c 25 /dev/sda1`: change the maximum number of mounts between filesystem checks (max-mount-count)
* `$ sudo tune2fs -i 10 /dev/sda1`: change the time interval between checks (interval-between-checks)
* `$ sudo tune2fs -l /dev/sda1`: list the contents of the superblock, including the current values of parameters which can be changed
* Lab 20.1
    * **Extra:** defragmentation

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

### Ch.22
* Lab 22.1
    * **Extra:** disk encryption w/ cryptsetup
* Lab 22.2
    * **Extra:** encrypted swap w/ cryptsetup

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


* Lab 24.1
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
