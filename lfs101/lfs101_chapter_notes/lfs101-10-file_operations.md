Chapter 10: File Operations
---------------------------

[10.0: Introduction/ Learning Objectives](#100-introduction-learning-objectives)  
[10.1: Filesystems](#101-filesystems)  
[10.2: Filesystem Architecture](#102-filesystem-architecture)  
[10.3: Comparing Files and File Types](#103-comparing-files-and-file-types)  
[10.4: Backing Up and Compressing Data](#104-backing-up-and-compressing-data)  
[10.5: Summary](#105-summary)  
[Paths and Commands](#paths-and-commands)  

### 10.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should be able to:
* Explore the filesystem and its hierarchy.
* Explain the filesystem architecture.
* Compare files and identify different file types.
* Back up and compress data.

### 10.1: Filesystems
----
#### Introduction to Filesystems
* in Linux (and all UNIX-like operating systems), "everything is a file"
* the root directory, `/`, is also called the **trunk**

#### Filesystem Varieties
* Linux filesystems:
   * **ext3**
   * **ext4**
   * **squashfs**
   * **brtfs**
* Linux offers implementations of filesystems of other operating systems:
   * **Windows**  (**ntfs**, **vfat**)
   * **MacOS** (**hfs**, **hfs+**)
   * **IBM** (**JFS**)
   * **SGI** (**xfs**)
* many legacy filesytems, like **FAT**, are also supported
* the most advanced filesystem types in common use are the journaling varieties: **ext4**, **xfs**, **btrfs**, and **jfs**

#### Linux Partitions
* important programs required to run the system are often kept on a separate partition (known as **root** or `/`) than the one that contains files owned by regular users of that system (`/home`)
* temporary files created and destroyed during the normal operation of Linux are often located on dedicated partitions

#### Mount Points
* before a filesystem can be used, it needs to be mounted to a **mount point**
* **mount point**: a directory (which may or may not be empty) where the filesystem is to be attached (mounted)

#### Mounting and Unmounting
* the **mount** command is used to attach a filesystem (can be local to the computer or on a network) somewhere within the filesystem tree
   * the basic arguments are the **device node** and **mount point**: `$ mount /dev/sda5 /home`
   * the **disk label** or **UUID** can be used instead of the device node
   * show all presently mounted filesystems: `$ mount`
* **unmount** a partition: `$ umount /home`
* must be logged in as root or use sudo for these commands
* if you want it to be automatically available every time the system starts up, you need to edit `/etc/fstab` (fstab == Filesystem Table); fstab shows the configuration of all pre-configured filesystems
* `$ df -Th` (disk-free) will display information about mounted filesystems, including usage statistics about currently used and available space

#### NFS and Network Filesystems
* **network** filesystems are sometimes called **distributed** filesystems
* most common one: **NFS** (**Network Filesystem**) developed by Sun
* another one: **CIFS** (also termed **SAMBA**) with Microsoft roots

#### NFS on the Server
* start NFS daemons with `$ sudo systemctl start nfs`  
* the text file `/etc/exports` contains the directories and permissions that a host is willing to share with other systems over NFS (that file on my VM is empty)  
   * a very simple entry in this file may look like the following: `/projects *.example.com(rw)`  
   * allows the directory `/projects` to be mounted using NFS with read and write (`rw`) permissions and shared with other hosts in the `example.com` domain  
* after modifying `/etc/exports`, notify Linux with `$ exportfs -av` (can also restart NFS, but that halts NFS before starting up again)  
* use `$ sudo systemctl enable nfs` to ensure NFS service starts whenever the system is booted  
* resources about `/etc/exports` options:  
   * [man/5/exports](https://linux.die.net/man/5/exports) (My own copy of the main page did not contain the `no_subtree_check` option.)  
   * [3. Setting Up an NFS Server](http://nfs.sourceforge.net/nfs-howto/ar01s03.html)  

#### NFS on the Client
* on client machine, to have remote filesystem mounted automatically upon boot, add an entry to `/etc/fstab`
   * example entry: `servername:/projects /mnt/nfs/projects nfs defaults 0 0`
* mount without a reboot or as a one-time mount: `$ sudo mount servername:/projects /mnt/nfs/projects`

#### Lab 10.1: Exploring Mounted Filesystems
1. Compare output of `$ cat /etc/fstab` and `$ mount`  
   * differences: fstab only shows preconfigured filesystems while mount shows all currently mounted filesystems
2. Find another way to view mounted filesystems by examining `/proc`
   * `$ cat /proc/self/mounts` (or `$ cat /proc/mounts` which links to `/proc/self/mounts`)
   * `$ cat /proc/filesystems`  

### 10.2: Filesystem Architecture
----
#### Overview of User Home Directories
* `/root` is just the home directory of the root users
* non-root users get `/home`
* on multi-user systems `/home` might be mounted as a separate file system
* users can be grouped, like `/home/faculty/`, `/home/staff/`, `/home/students/`

#### The /bin and /sbin Directories
* `/bin` contains executable binaries and essential commands required by all system users
* `/sbin`  is intended for essential binaries related to system administration, such as **fsck** and **shutdown**
* non-essential commands for system boot or operation in single-user mode should be in `/usr/bin` and `/usr/sbin`
   * ? why `/usr/sbin` for system administration functions?
   * historically, this was so `/usr` could be mounted as a separate filesystem, but that isn't used now
   * now `/usr/bin` and `/bin` are symbolically linked, as are `/usr/sbin` and `/sbin`

#### The proc Filesystem
* `/proc` is a **pseudo filesystem** b/c it has no permanent presence on the disk
* it's **virtual files** exist only in memory
* contains runtime system information (e.g. system memory, devices mounted, hardware configuration, etc.)
* some important files include:
   * `/proc/cpuinfo`
   * `/proc/interrupts`
   * `/proc/meminfo`
   * `/proc/mounts`
   * `/proc/partitions`
   * `/proc/version`
* subdirectories:
   * `/proc/<Process-ID-#>`: directory for every process running on system, containing vital information about it
   * `/proc/sys`: information about the entire system, in particular its hardware and configuration

#### The /dev Directory
* contains **device nodes**, a type of pseudo-file used by most hardware and software devices, except for network devices
* empty on disk partition when not mounted
* contains entries which are created by the **udev** system
* `/dev` contains items like:
   * `/dev/sda1` (first partition on the first hard disk)
   * `/dev/lp1` (second printer)
   * `/dev/dvd1` (first DVD drive).

#### The /var Directory
* the `/var` directory contains files that are expected to change in size and content as the system is running (var stands for variable)
* examples:
   * System log files: `/var/log`
   * Packages and database files: `/var/lib`
   * Print queues: `/var/spool`
   * Temp files: `/var/tmp`
   * FTP service: `/var/ftp`
   * HTTP service: `/var/www`
* can be put in its own filesystem so the growth of the files doesn't affect system

#### The /etc Directory
* `/etc` - home for system configuration files
* no binary programs, but some scripts
* only for system-wide configuration; user-specific configuration files are found in their home directory

#### The /boot Directory
* `/boot` - essential files for booting
* for every kernal, four files:
1. `vmlinuz`: the compressed Linux kernel, required for booting
2. `initramfs`: the initial **ram** filesystem, required for booting, sometimes called **initrd**, not **initramfs**
3. `config`: the kernel configuration file, only used for debugging and bookkeeping
4. `System.map`: kernel symbol table, only used for debugging

#### The /lib and /lib64 Directories
* `/lib` - libraries needed by programs in `/bin` and `/sbin`
* most libraries known as **dynamically loaded libraries** (aka **shared libraries** or **Shared Objects** (**SO**))
* `/lib64` - 64-bit libraries
* kernel **modules** (kernel code, often device drivers, that can be loaded and unloaded without re-starting the system) are located in `/lib/modules/<kernel-version-number>`

#### Removable media: the /media, /run and, /mnt Directories
* USB, CD, DVD used to be mounted under `/media` but now usually `/run`
* for example, a USB pen drive with a label myusbdrive for a user name student would be mounted at `/run/media/student/myusbdrive`
* "The `/mnt` directory has been used since the early days of UNIX for temporarily mounting filesystems.  These might be network filesystems with **NFS**, not normally mounted or temporary partitions, or so-called **loopback** filesystems, which are files which pretend to be partitions."

#### Additional Directories Under /:
Directory name | Usage
-------------- | -----
`/opt`         | Optional application software packages.
`/sys`         | Virtual pseudo-filesystem giving information about the system and the hardware. Can be used to alter system parameters and for debugging purposes. 
`/srv`         | Site-specific data served up by the system. Seldom used.
`/tmp`         | Temporary files; on some distributions erased across a reboot and/or may actually be a ramdisk in memory.
`/usr`         | Multi-user applications, utilities and data.

#### The /usr Directory Tree
* "The `/usr` directory tree contains theoretically non-essential programs and scripts (in the sense that they should not be needed to initially boot the system) and has at least the following sub-directories:"
  
Directory name | Usage
-------------- | -----
`/usr/include` | Header files used to compile applications.
`/usr/lib`     | Libraries for programs in /usr/bin and /usr/sbin.
`/usr/lib64`   | 64-bit libraries for 64-bit programs in /usr/bin and /usr/sbin.
`/usr/sbin`    | Non-essential system binaries, such as system daemons.
`/usr/share`   | Shared data used by applications, generally architecture-independent.
`/usr/src`     | Source code, usually for the Linux kernel.
`/usr/local`   | Data and programs specific to the local machine. Subdirectories include bin, sbin, lib, share, include, etc.
`/usr/bin`     | This is the primary directory of executable commands on the system. 
* ? `/usr/bin` primary ?

### 10.3: Comparing Files and File Types
----
#### Comparing Files with diff
* **diff** is used to compare files and directories; usage: `$ diff [options] <filename1> <filename2>`  
* for binaries, use **cmp**  
  
diff Option | Usage
----------- | -----
-c          | Provides a listing of differences that include 3 lines of context before and after the lines differing in content
-r          | Used to recursively compare subdirectories, as well as the current directory
-i          | Ignore the case of letters
-w          | Ignore differences in spaces and tabs (white space)
-q          | Be quiet: only report if files are different without listing the differences

#### Using diff3 and patch
* **diff3** can compare three files at once; usage: `$ diff3 MY-FILE COMMON-FILE YOUR-FILE`  
* **patches** to source code and config files (made with **patch** program), are based on the **deltas** determined using **diff**; example:
   * `$ diff -Nur originalfile newfile > patchfile`  
   * patches only need to contain the lines in a file that changed  
* apply a patch using either:  
   * `$ patch -p1 < patchfile` (directory tree)  
   * `$ patch originalfile patchfile` (one file)  
#### Using the 'file' Utility
* Linux does not rely on file extensions to determine the type of file ("file.txt" can be an executable, for instance)  
* **file** can be used to determine the type of file (works on directories, too)  
* examines the contents and certain characteristics to determine whether the files are plain text, shared libraries, executable programs, scripts, or something else

#### Lab 10.2: Using diff and patch
Copy a file to /tmp, convert the contents to uppercase and save in a new file, prepare a patch, and apply the patch to the first file.  
My solution:  
* `$ cd /tmp`  
* `$ cp /etc/group .`  
* `$ dd if=/tmp/group of=/tmp/GROUP conv=ucase`  
* `$ diff group GROUP`  
* `$ diff -Nur group GROUP > patchfile`  ; I tried running patch before this step:  
   > `patch: **** Only garbage was found in the patch input.`  
* `$ patch --dry-run group patchfile`  
   > `checking file group`  
* `$ patch group patchfile`  
   > `patching file group`  
* `$ diff group GROUP`  

Lab solution:
```
For this exercise, you could use any text file, but we will use /etc/group as described.

    student:/tmp> cd /tmp

    student:/tmp> cp /etc/group /tmp

    student:/tmp> dd if=/tmp/group of=/tmp/GROUP conv=ucase

    1+1 records in
    1+1 records out
    963 bytes (963 B) copied, 0.000456456 s, 2.1 MB/s

    student:/tmp> diff -Nur group GROUP > patchfile
    student:/tmp> cat patchfile

    --- group       2015-04-17 11:03:26.710813740 -0500
    +++ GROUP       2015-04-17 11:15:14.602813740 -0500
    @@ -1,68 +1,68 @@
    -root:x:0:
    -daemon:x:1:
    -bin:x:2:
    -sys:x:3:
    ....
    -libvirtd:x:127:student
    -vboxsf:x:999:
    +ROOT:X:0:
    +DAEMON:X:1:
    +BIN:X:2:
    +SYS:X:3:
    .....

    student:/tmp> patch --dry-run group patchfile

    checking file group

    student:/tmp> patch  group patchfile

    patching file group

    Note you could have also done either of these two commands:

    student:/tmp> patch group < patchfile
    student:/tmp> patch < patchfile

    student:/tmp> diff group GROUP

    student:/tmp>
```

### 10.4: Backing Up and Compressing Data
----
#### Backing Up Data
* both **cp** and **rsync** can be used for backup, but
* **rsync** is more efficient b/c it only copies what has changed and can walk directory tree recursively (`-r` option)  

#### Using rsync
* example use: `$ rsync -r project-X archive-machine:archives/project-X`  
* rsync can be destructive - test using `--dry-run`  
* basic syntax: `$ rsync sourcefile destinationfile`  
* used the **target:path** form, where target can be in the form of **\[user@\]host**

#### Compressing Data
Linux compression methods:  

Command | Usage  
------- | -----  
gzip    | The most frequently used Linux compression utility  
bzip2   | Produces files significantly smaller than those produced by gzip  
xz      | The most space-efficient compression utility used in Linux  
zip     | Is often required to examine and decompress archives from other operating systems  
* **tar** is used to group files in an **archive** and then compress the whole archive at once  

#### Compressing Data Using gzip
* gzip compresses well and is very fast  
* usage examples:  

Command            | Usage  
-------            | -----  
`gzip *`           | Compresses all files in the current directory; each file is compressed and renamed with a .gz extension.  
`gzip -r projectX` | Compresses all files in the projectX directory, along with all files in all of the directories under projectX.  
`gunzip foo`       | Decompresses foo found in the file foo.gz. Under the hood, the gunzip command is actually the same as gzip –d.  

#### Compressing Data Using bzip2
* produces smaller files than gzip but takes more time  
* usually used to compress larger files  
* usage examples:

Command         | Usage  
-------         | -----  
`bzip2 *`       | Compresses all of the files in the current directory and replaces each file with a file renamed with a .bz2 extension.  
`bunzip2 *.bz2` | Decompresses all of the files with an extension of .bz2 in the current directory. Under the hood, bunzip2 is the same as calling bzip2 -d.  

#### Compressing Data Using xz
* most space-efficient compression utility in Linux  
* usage examples:  

Command                             | Usage  
-------                             | -----  
`xz *`                              | Compresses all of the files in the current directory and replaces each file with one with a .xz extension.  
`xz foo`                            | Compresses the file foo into foo.xz using the default compression level (-6), and removes foo if compression succeeds.  
`xz -dk bar.xz`                     | Decompresses bar.xz into bar and does not remove bar.xz even if decompression is successful.  
`xz -dcf a.txt b.txt.xz > abcd.txt` | Decompresses a mix of compressed and uncompressed files to standard output, using a single command.  
`xz -d *.xz`                        | Decompresses the files compressed using xz.  

#### Handling Files Using zip
* **zip** is not often used to compress files in Linux but to examine and decompress archives from other OSs  
* used in Linux when getting a zipped file from a Windows user  
* legacy program  
* usage examples:  

Command               | Usage
-------               | -----
`zip backup *`        | Compresses all files in the current directory and places them in the file backup.zip.
`zip -r backup.zip ~` | Archives your login directory (~) and all files and directories under it in the file backup.zip.
`unzip backup.zip`    | Extracts all files in the file backup.zip and places them in the current directory.

#### Archiving and Compressing Data Using tar
* **tar** stood for "**t**ape **ar**chive"  
* used to create or extract files from an archive file, often called a **tarball**  
* can optionally compress while creating the archive and decompress while extracting  
* usage examples:  

Command                        | Usage
-------                        | -----
`tar xvf mydir.tar`            | Extract all the files in mydir.tar into the mydir directory
`tar zcvf mydir.tar.gz mydir`  | Create the archive and compress with gzip
`tar jcvf mydir.tar.bz2 mydir` | Create the archive and compress with bz2
`tar Jcvf mydir.tar.xz mydir`  | Create the archive and compress with xz
`tar xvf mydir.tar.gz`         | Extract all the files in mydir.tar.gz into the mydir directory. Note you do not have to tell tar it is in gzip format.

* separate archiving and compression stages:  
   * `$ tar cvf mydir.tar mydir ; gzip mydir.tar`  
   * `$ gunzip mydir.tar.gz ; tar xvf mydir.tar`  
* this is slower and wastes space by creating an unneeded intermediary **.tar** file

#### Disk-to-Disk Copying
* **dd** is useful for making copies of disks; "data definition" or "disk destroyer" or "delete data"  
* CAN ERASE A HARD DISK  
* usage examples:  

Command                                    | Usage  
-------                                    | -----  
`dd if=/dev/sda of=sda.mbr bs=512 count=1` | back up Master Boot Record (MBR)  
`dd if=/dev/sda of=/dev/sdb`               | make a copy of one disk onto another, erasing everything that existed on second disk  

#### Lab 10.3: Archiving (Backing Up) the Home Directory
Create tar backups of the home directory, move to /tmp, compare sizes between different types of backups.  
My solution:  
* `tar -jcvf home.tar.bz2 ~`  
* `mv home.tar.bz2 /tmp/backup.tar`  
* `tar -zcvf home.tar.gz ~`  
* `mv home.tar.gz /tmp/backup.tar.gz`  

The gzip backup is much smaller than the bzip2 backup.
Results of running the lab solution:
* -rw-rw-r--   1 student student 156M Nov  6 16:29 doc.tar
* -rw-rw-r--   1 student student  44M Nov 11 18:13 doc.tar.bz2
* -rw-rw-r--   1 student student  53M Nov 11 18:08 doc.tar.gz
* -rw-rw-r--   1 student student  39M Nov 11 18:16 doc.tar.xz

Lab solution:  
```
To construct a tarball archive of your home directory you can do:

student:/tmp> tar -cvf /tmp/backup.tar ~
      

or equivalently

student:/tmp> tar -cvf /tmp/backup.tar /home/student
      

Note you can have omitted the - in the options with no change. In the following we will not bother using the -v option for verbose. To create archives with all three compression utilities:

student:/tmp> tar zcf /tmp/backup.tar.gz ~
student:/tmp> tar jcf /tmp/backup.tar.bz2 ~
student:/tmp> tar Jcf /tmp/backup.tar.xz ~
      

Comparing the sizes (first using the -h option to ls to make it human-readable):

student@ubuntu:~student:/tmp> ls -lh /tmp/backup*

-rw-rw-r-- 1 student student 8.3M Apr 17 10:14 /tmp/backup2.tar.gz
-rw-rw-r-- 1 student student  12M Apr 17 10:13 /tmp/backup.tar
-rw-rw-r-- 1 student student 8.4M Apr 17 10:15 /tmp/backup.tar.bz2
-rw-rw-r-- 1 student student 8.3M Apr 17 10:14 /tmp/backup.tar.gz
-rw-rw-r-- 1 student student 8.2M Apr 17 10:15 /tmp/backup.tar.xz
      

and then without it:

student@ubuntu:~student:/tmp> ls -l /tmp/backup*

-rw-rw-r-- 1 student student  8686942 Apr 17 10:14 /tmp/backup2.tar.gz
-rw-rw-r-- 1 student student 12226560 Apr 17 10:13 /tmp/backup.tar
-rw-rw-r-- 1 student student  8720491 Apr 17 10:15 /tmp/backup.tar.bz2
-rw-rw-r-- 1 student student  8686929 Apr 17 10:14 /tmp/backup.tar.gz
-rw-rw-r-- 1 student student  8551064 Apr 17 10:15 /tmp/backup.tar.xz
      

Note in this case there is not much difference in the different archiving methods, but this particular directory was a bad choice because it already contained a lot of compressed files. A somewhat better example involving more text files:

student:/tmp> tar cf  /tmp/doc.tar     /usr/share/doc
student:/tmp> tar zcf /tmp/doc.tar.gz  /usr/share/doc
student:/tmp> tar jcf /tmp/doc.tar.bz2 /usr/share/doc
student:/tmp> tar Jcf /tmp/doc.tar.xz  /usr/share/doc

student:/tmp> ls -lh /tmp/doc.tar*

-rw-rw-r-- 1 student student 85M Apr 17 10:34 /tmp/doc.tar
-rw-rw-r-- 1 student student 31M Apr 17 10:35 /tmp/doc.tar.bz2
-rw-rw-r-- 1 student student 34M Apr 17 10:34 /tmp/doc.tar.gz
-rw-rw-r-- 1 student student 28M Apr 17 10:36 /tmp/doc.tar.xz
      

which shows xz did best, followed by bz2 and then gz. You may have noticed, however, the inverse relationship between the size reduction of the compression and how long it took! 
```

### 10.5: Summary
----
The key concepts we covered in this chapter are:
* The filesystem tree starts at what is often called the root directory (or trunk, or /).
* The **Filesystem Hierarchy Standard** (**FHS**) provides Linux developers and system administrators a standard directory structure for the filesystem.
* Partitions help to segregate files according to usage, ownership, and type.
* Filesystems can be mounted anywhere on the main filesystem tree at a mount point. Automatic filesystem mounting can be set up by editing `/etc/fstab`.
* **NFS** (The **Network Filesystem**) is a useful method for sharing files and data through the network systems.
* Filesystems like `/proc` are called pseudo filesystems because they exist only in memory.
* `/root` (slash-root) is the home directory for the root user.
* `/var` may be put in its own filesystem so that growth can be contained and not fatally affect the system.
* `/boot` contains the basic files needed to boot the system.
* **patch** is a very useful tool in Linux. Many modifications to source code and configuration files are distributed with patch files, as they contain the deltas or changes to go from an old version of a file to the new version of a file.
* File extensions in Linux do not necessarily mean that a file is of a certain type.
* **cp** is used to copy files on the local machine, while **rsync** can also be used to copy files from one machine to another, as well as synchronize contents.
* **gzip**, **bzip2**, **xz** and **zip** are used to compress files.
* **tar** allows you to create or extract files from an archive file, often called a tarball. You can optionally compress while creating the archive, and decompress while extracting its contents.
* **dd** can be used to make large exact copies, even of entire disk partitions, efficiently.
  
### Paths and Commands
----

#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem | `/` | root directory ("trunk"); contains important programs required to run the system | LFS101 10.1
filesystem | `/etc/fstab` | Filesystem Table; shows the configuration of all pre-configured filesystems | LFS101 10.1
filesystem | `/etc/exports` | contains the directories and permissions that a host is willing to share with other systems over NFS | LFS101 10.1
filesystem | `/proc/self/mounts` | contains list of mounted filesystems | LFS101 10.1
filesystem | `/proc/filesystems` | contains list of mounted filesystems | LFS101 10.1
filesystem | `/root` | home directory of root users | LFS101 10.2
filesystem | `/home` | contains files owned by regular users of the system | LFS101 10.2
filesystem | `/bin` | contains executable binaries and essential commands required by all system users | LFS101 10.2
filesystem | `/sbin` | intended for essential binaries related to system administration, such as fsck and shutdown | LFS101 10.2
filesystem | `/usr/bin` | non-essential commands for system boot or operation in single-user mode | LFS101 10.2
filesystem | `/usr/sbin` | non-essential commands for system boot or operation in single-user mode | LFS101 10.2
filesystem | `/proc` | psuedo filesystem that contains runtime system information | LFS101 10.2
filesystem | `/proc/cpuinfo` |  | LFS101 10.2
filesystem | `/proc/interrupts` |  | LFS101 10.2
filesystem | `/proc/meminfo` |  | LFS101 10.2
filesystem | `/proc/mounts` |  | LFS101 10.2
filesystem | `/proc/partitions` |  | LFS101 10.2
filesystem | `/proc/version` |  | LFS101 10.2
filesystem | `/proc/<Process-ID-#>` | directory for every process running on system | LFS101 10.2
filesystem | `/proc/sys` | information about the entire system, in particular its hardware and configuration | LFS101 10.2
filesystem | `/dev` | contains device nodes | LFS101 10.2
filesystem | `/dev/sda1` | first partition on the first hard disk | LFS101 10.2
filesystem | `/dev/lp1` | second printer | LFS101 10.2
filesystem | `/dev/dvd1` | first DVD drive | LFS101 10.2
filesystem | `/var` | contains files that are expected to change in size and content as the system is running | LFS101 10.2
filesystem | `/var/log` | system log files | LFS101 10.2
filesystem | `/var/lib` | packages and database files | LFS101 10.2
filesystem | `/var/spool` | print queues | LFS101 10.2
filesystem | `/var/tmp` | temp files | LFS101 10.2
filesystem | `/var/ftp` | FTP service | LFS101 10.2
filesystem | `/var/www` | HTTP service | LFS101 10.2
filesystem | `/etc` | home for system configuration files | LFS101 10.2
filesystem | `/boot` | essential files for booting | LFS101 10.2
filesystem | `/lib` | libraries needed by programs in /bin and /sbin | LFS101 10.2
filesystem | `/lib64` | 64-bit libraries | LFS101 10.2
filesystem | `/lib/modules/<kernel-version-number>` | kernel modules | LFS101 10.2
filesystem | `/media` | where USB, CD, DVD used to be mounted | LFS101 10.2
filesystem | `/run` | where USB, CD, DVD are usually mounted | LFS101 10.2
filesystem | `/run/media/student/myusbdrive` |  | LFS101 10.2
filesystem | `/mnt` | used since the early days of UNIX for temporarily mounting filesystems | LFS101 10.2
filesystem | `/opt` | optional application software packages | LFS101 10.2
filesystem | `/sys` | virtual pseudo-filesystem giving information about the system and the hardware | LFS101 10.2
filesystem | `/srv` | site-specific data served up by the system | LFS101 10.2
filesystem | `/tmp` | temporary files | LFS101 10.2
filesystem | `/usr` | multi-user applications, utilities, and data; contains theoretically non-essential programs and scripts | LFS101 10.2
filesystem | `/usr/include` | header files used to compile applications | LFS101 10.2
filesystem | `/usr/lib` | libraries for programs in /usr/bin and /usr/sbin | LFS101 10.2
filesystem | `/usr/lib64` | 64-bit libraries for 64-bit programs in /usr/bin and /usr/sbin | LFS101 10.2
filesystem | `/usr/sbin` | non-essential system binaries, such as system daemons | LFS101 10.2
filesystem | `/usr/share` | shared data used by applications, generally architecture-independent | LFS101 10.2
filesystem | `/usr/src` | source code, usually for the Linux kernel | LFS101 10.2
filesystem | `/usr/local` | data and programs specific to the local machine; subdirectories include bin, sbin, lib, share, include, etc | LFS101 10.2
filesystem | `/usr/bin` | primary directory of executable commands on the system | LFS101 10.2

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem | `$ mount <device node> <mount point` like `$ mount /dev/sda5 /home` | attach a filesystem with the fs tree | LFS101 10.1
filesystem | `$ mount` | show presently mounted filesystems | LFS101 10.1
filesystem | `$ umount /home` | unmount a partition | LFS101 10.1
filesystem | `$ df -Th` | disk-free; display information about mounted filesystems, including usage statistics about currently used and available space | LFS101 10.1
filesystem | `$ sudo systemctl start nfs` | start NFS daemons | LFS101 10.1
filesystem | `$ exportfs -av` | notify Linux after modifying `/etc/exports` | LFS101 10.1
filesystem | `$ sudo systemctl enable nfs` | ensure NFS service starts whenever the system is booted | LFS101 10.1
filesystem | `$ sudo mount servername:/projects /mnt/nfs/projects` | mount without a reboot or as a one-time mount | LFS101 10.1
files | `$ diff [options] <filename1> <filename2>` | compare files and directories | LFS101 10.3
files | `$ cmp ...` | compare binaries | LFS101 10.3
files | `$ diff3 MY-FILE COMMON-FILE YOUR-FILE` | compare three files at once | LFS101 10.3
files | `$ diff -Nur originalfile newfile > patchfile` | create patchfile using diff | LFS101 10.3
files | `$ patch -p1 < patchfile` | apply a patch | LFS101 10.3
files | `$ patch originalfile patchfile` | apply a patch | LFS101 10.3
files | `$ file <FILE NAME>` | determine type of file | LFS101 10.3
files | `$ dd if=/tmp/group of=/tmp/GROUP conv=ucase` | convert and copy a file | LFS101 10.3
files | `$ diff group GROUP` | compare two files | LFS101 10.3
files | `$ diff -Nur group GROUP > patchfile` | create patchfile using diff | LFS101 10.3
files | `$ patch --dry-run group patchfile` | run patch dry run | LFS101 10.3
files | `$ patch group patchfile` | apply a patch | LFS101 10.3
files | `$ rsync <source file> <destination file>` | backup data; basic syntax | LFS101 10.4
files | `$ rsync -r project-X archive-machine:archives/project-X` | backup data; example use | LFS101 10.4
files | `$ gzip *` | compresses all files in the current directory; each file is compressed and renamed with a .gz extension | LFS101 10.4
files | `$ gzip -r projectX` | compresses all files in the projectX directory, along with all files in all of the directories under projectX | LFS101 10.4
files | `$ gunzip foo` == `$ gzip -d foo` | decompresses foo found in the file foo.gz; actually the same as `gzip –d` | LFS101 10.4
files | `$ bzip2 *` | compresses all of the files in the current directory and replaces each file with a file renamed with a .bz2 extension | LFS101 10.4
files | `$ bunzip2 *.bz2` == `$ bzip2 -d *.bz2` | decompresses all of the files with an extension of .bz2 in the current directory; the same as calling `bzip2 -d` | LFS101 10.4
files | `$ xz *` | compresses all of the files in the current directory and replaces each file with one with a .xz extension | LFS101 10.4
files | `$ xz foo` | compresses the file foo into foo.xz using the default compression level (-6), and removes foo if compression succeeds | LFS101 10.4
files | `$ xz -dk bar.xz` | decompresses bar.xz into bar and does not remove bar.xz even if decompression is successful | LFS101 10.4
files | `$ xz -dcf a.txt b.text.xz > abcd.text` | decompresses a mix of compressed and uncompressed files to standard output, using a single command | LFS101 10.4
files | `$ xz -d *.xz` | decompresses the files compressed using xz | LFS101 10.4
files | `$ zip backup *` | compresses all files in the current directory and places them in the file backup.zip | LFS101 10.4
files | `$ zip -r backup.zip ~` | archives your login directory (~) and all files and directories under it in the file backup.zip | LFS101 10.4
files | `$ unzip backup.zip` | extracts all files in the file backup.zip and places them in the current directory | LFS101 10.4
files | `$ tar xvf mydir.tar` | extract all the files in mydir.tar into the mydir directory | LFS101 10.4
files | `$ tar zcvf mydir.tar.gz mydir` | create the archive and compress with gzip | LFS101 10.4
files | `$ tar jcvf mydir.tar.bz2 mydir` | create the archive and compress with bz2 | LFS101 10.4
files | `$ tar Jcvf mydir.tar.xz mydir` | create the archive and compress with xz | LFS101 10.4
files | `$ tar xvf mydir.tar.gz` | extract all the files in mydir.tar.gz into the mydir directory | LFS101 10.4
files | `$ tar cvf mydir.tar mydir` then `gzip mydir.tar` | archive and compress files in separate steps | LFS101 10.4
files | `$ gunzip mydir.tar.gz` then `tar xvf mydir.tar` | decompress and extract files in separate steps | LFS101 10.4
files | `$ dd if=/dev/sda of=sda.mbr bs=512 count=1` | back up Master Boot Record (MBR) | LFS101 10.4
files | `$ dd if=/dev/sda of=/dev/sdb` | make a copy of one disk onto another, erasing everything that existed on second disk | LFS101 10.4
