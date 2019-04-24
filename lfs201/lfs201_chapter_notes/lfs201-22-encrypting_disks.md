Chapter 21: Encrypting Disks
----------------------------

[22.3: Learning Objectives](#223-learning-objectives)  
[22.4: Why Use Encryption?](#224-why-use-encryption)  
[22.5: LUKS](#225-luks)  
[22.6: cryptsetup](#226-cryptsetup)  
[22.7: Using an Encrypted Partition](#227-using-an-encrypted-partition)  
[22.8: Mounting at Boot](#228-mounting-at-boot)  
[22.9: Setting Up an Encrypted Loopback Partition (Demo)](#229-setting-up-an-encrypted-loopback-partition-demo)  
[Lab 22.1: Disk Encryption](#lab-221-disk-encryption)  
[Lab 22.2: Encrypted Swap](#lab-222-encrypted-swap)  
[22.10: Knowledge Check 22.1](#2210-knowledge-check-221)  

[Paths and Commands](#paths-and-commands)  
  
### 22.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Provide sound reasons for using encryption and know when it is called for.
* Understand how **LUKS** operates through the use of **cryptsetup**.
* Set up and use encrypted filesystems and partitions.
* Configure the system to mount encrypted partitions at boot.

### 22.4: Why Use Encryption?
----
* use wherever senstive data is being stored and transmitted
* block device level encryption provides one of the strongest protections against harm caused by loss or compromise of data
* can encrypt all or some of your disk partitions during installation
* can encrypt an already existing partition in place, but that involves a data copying operation

### 22.5: LUKS
----
* **LUKS** (**L**inux **U**nified **K**ey **S**etup) provides block device level encryption
* LUKS is installed on top of **cryptsetup**, which can also use **plain dm-crypt** volumes, **loop-AES**, and **TrueCrypt**-compatible format
* because LUKS stores all necessary information in the partition header itself, it is rather easy to migrate partitions to other disks or systems

### 22.6: cryptsetup
----
* once encrypted volumes have been set up, they can be mounted and unmounted with normal disk utilities
* `$ cryptsetup [OPTION...] <action> <action-specific>` generic syntax

### 22.7: Using an Encrypted Partition
----
* if the partition `/dev/sdc12` already exists, the following commands will set up encryption, make it available to LUKS, format it, mount it, use it, and unmount it
* `$ sudo cryptsetup luksFormat /dev/sdc12` gives the partition to LUKS
* `$ sudo cryptsetup luksFormat --cipher aes /dev/sdc12` supplies a cryptographic method if the kernel does not support the default method
* `/proc/crypto` shows the methods the system supports
* examples from my machine (37 total):
    ```
    name         : md5
    driver       : md5-generic
    module       : kernel
    priority     : 0
    refcnt       : 1
    selftest     : passed
    type         : shash
    blocksize    : 64
    digestsize   : 16

    name         : sha256
    driver       : sha256-ssse3
    module       : kernel
    priority     : 150
    refcnt       : 2
    selftest     : passed
    type         : shash
    blocksize    : 64
    digestsize   : 32

    name         : aes
    driver       : aes-asm
    module       : kernel
    priority     : 200
    refcnt       : 1
    selftest     : passed
    type         : cipher
    blocksize    : 16
    min keysize  : 16
    max keysize  : 32
    ```
* `$ sudo cryptsetup --verbose luksOpen /dev/sdc12 SECRET` makes the volume available any time (once you enter the passphrase)
* `$ sudo mkfs.ext4 /dev/mapper/SECRET` formats the partition
* `$ sudo mount /dev/mapper/SECRET /mnt` mounts the partition
* `$ sudo umount /mnt` unmounts the partition
* `$ sudo cryptsetup --verbose luksClose SECRET` removes the association

### 22.8: Mounting at Boot
----
* two conditions need to be met to mount an encrypted partition at boot:
    * make an entry in `/etc/fstab` (not specific to encryption):  
    `/dev/mapper/SECRET /mnt /ext4 defaults 0 0`
    * add an entry to `/etc/crypttab`:  
    `SECRET /dev/sdc12`

### 22.9: Setting Up an Encrypted Loopback Partition (Demo)
----
* `$ dd if=/dev/zero of=loop-partition bs=1M count=1024` creates a 1GB file full of zeros
* `$ losetup -f` to see first available loopback device (losetup sets up and controls loop devices):
    > /dev/loop0
* `$ sudo losetup /dev/loop2 loop-partition` set `/dev/loop2` as an available loopback device (I used `/dev/loop1`)
    > NAME       SIZELIMIT OFFSET AUTOCLEAR RO BACK-FILE
    > /dev/loop1         0      0         0  0 /home/student/test
* `$ sudo crytpsetup luksFormat /dev/loop2` makes the file available for encryption
    * YES in ALLCAPS
    * make a passphrase (I used "okayfine")
* `$ sudo cryptsetup luksOpen /dev/loop2 crypt-partition` makes it available for formatting, where "crypt-partition" is the name it will be known by
* `$ ls -l /dev/mapper` shows the device
    > total 0
    > crw------- 1 root root 10, 236 Apr 23 16:26 control
    > lrwxrwxrwx 1 root root       7 Apr 23 21:29 crypt-kurt_test -> ../dm-0
* `$ sudo mkfs.ext4 /dev/mapper/crypt-partition` puts a filesystem on it
* `$ sudo mount /dev/mapper/crypt-partition /mnt` mounts the device
* `$ df -h` shows the mounted partition
    ```
    Filesystem                   Size  Used Avail Use% Mounted on
    /dev/sda1                     27G  9.4G   16G  38% /
    devtmpfs                     1.9G     0  1.9G   0% /dev
    tmpfs                        1.9G     0  1.9G   0% /dev/shm
    tmpfs                        1.9G   13M  1.9G   1% /run
    tmpfs                        1.9G     0  1.9G   0% /sys/fs/cgroup
    tmpfs                        378M  8.0K  378M   1% /run/user/42
    tmpfs                        378M   32K  378M   1% /run/user/1000
    tmpfs                        378M     0  378M   0% /run/user/0
    /dev/mapper/crypt-kurt_test  990M  2.6M  921M   1% /mnt
    ```
* `$ sudo umount /mnt` unmounts the partition
* `$ sudo cryptsetup luksClose /dev/mapper/crypte-partition` removes the association(?)
* `$ sudo losetup -d /dev/loop2` terminates the loopback device
* `$ losetup -f` only shows original loopback device
* `$ rm loop-partition` to remove file

### 22.10: Knowledge Check 22.1
----
Steps to prepare and mount an encrypted partition:
1. Create a partition for the encrypted block device.
2. Format with cryptsetup.
3. Create the un-encrypted pass through device.
4. Format with a standard filesystem such as ext4.
5. Mount the filesystem on the encrypted block device.

### Lab 22.1: Disk Encryption
----
### Lab 22.2: Encrypted Swap
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
encryption | `/proc/crypto` | shows the methods the system supports | LFS201 22.7
encryption | `/etc/crypttab` | configuration file for encrypted block devices | LFS201 22.8
partitions | `/dev/mapper` | LVM logical volumes (as opposed to LVM *physical* volumes) | LFS201 22.9

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
encryption | `$ cryptsetup [OPTION...] <action> <action-specific>` | generic syntax for cryptsetup | LFS201 22.6
encryption | `$ sudo cryptsetup luksFormat /dev/sdc12` | gives the `/dev/sdc12` partition to LUKS | LFS201 22.7
encryption | `$ sudo cryptsetup luksFormat --cipher aes /dev/sdc12` | supplies a cryptographic method | LFS201 22.7
encryption | `$ sudo cryptsetup --verbose luksOpen /dev/sdc12 SECRET` | makes the volume available any time | LFS201 22.7
encryption | `$ sudo mkfs.ext4 /dev/mapper/SECRET` | formats the partition | LFS201 22.7
encryption | `$ sudo mount /dev/mapper/SECRET /mnt` | mounts the partition | LFS201 22.7
encryption | `$ sudo umount /mnt` | unmounts the partition | LFS201 22.7
encryption | `$ sudo cryptsetup --verbose luksClose SECRET` | removes the association | LFS201 22.7
files | `$ dd if=/dev/zero of=loop-partition bs=1M count=1024` | creates a 1GB file full of zeros | LFS201 22.9
unknown | `$ losetup -f` | shows first available loopback device (losetup sets up and controls loop devices) | LFS201 22.9
unknown | `$ sudo losetup /dev/loop2 loop-partition` | sets `/dev/loop2` as an available loopback device | LFS201 22.9
encryption | `$ sudo crytpsetup luksFormat /dev/loop2` | makes the file available for encryption | LFS201 22.9
encryption | `$ sudo cryptsetup luksOpen /dev/loop2 crypt-partition` | makes the device available for formatting, where "crypt-partition" is the name it will be known by | LFS201 22.9
apps | `$ ls -l /dev/mapper` | shows logical volumes | LFS201 22.9
filesystem, partitions | `$ sudo mkfs.ext4 /dev/mapper/crypt-partition` | puts a filesystem on the volume | LFS201 22.9
partitions | `$ sudo mount /dev/mapper/crypt-partition /mnt` | mounts the device | LFS201 22.9
partitions | `$ df -h` | shows the mounted partition | LFS201 22.9
partitions | `$ sudo umount /mnt` | unmounts the partition | LFS201 22.9
encryption | `$ sudo cryptsetup luksClose /dev/mapper/crypte-partition` | removes the association(?) | LFS201 22.9
unknown | `$ sudo losetup -d /dev/loop2` | terminates the loopback device | LFS201 22.9
unknown | `$ losetup -f` | only shows original loopback device | LFS201 22.9
files | `$ rm loop-partition` | removes file | LFS201 22.9
