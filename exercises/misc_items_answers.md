

Chapter 40: Backup and Recovery Methods
---------------------------------------

[40.3: Learning Objectives](#403-learning-objectives)  
[40.4 Why Backups?](#404-why-backups)  
[40.5: What Needs Backup?](#405-what-needs-backup)  
[40.6: Backup vs. Archive](#406-backup-vs-archive)  
[40.7: Tape Drives](#407-tape-drives)  
[40.8: Backup Methods](#408-backup-methods)  
[40.9: Backup Strategies](#409-backup-strategies)  
[40.10: Backup Utilities](#4010-backup-utilities)  
[40.11: Using tar for Backups](#4011-using-tar-for-backups)  
[40.12: Using tar for Restoring Files](#4012-using-tar-for-restoring-files)  
[40.13: Incremental Backups with tar](#4013-incremental-backups-with-tar)  
[40.14: Archive Compression Methods](#4014-archive-compression-methods)   
[40.15: dd](#4015-dd)  
[40.16: dd Examples](#4016-dd-examples)  
[40.17: rsync](#4017-rsync)  
[40.18: cpio](#4018-cpio)  
[40.19: dump and restore](#4019-dump-and-restore)  
[40.20: dump Options](#4020-dump-options)  
[40.21: restore](#4021-restore)  
[40.22: mt](#4022-mt)  
[40.23: Backup Programs](#4023-backup-programs)  
[Lab 40.1: Using tar for Backup](#lab-401-using-tar-for-backup)  
[Lab 40.2: Using cpio for Backup](#lab-402-using-cpio-for-backup)  
[Lab 40.3: Using rsync for Backup](#lab-403-using-rsync-for-backup)
[Paths and Commands](#paths-and-commands)  
  
### 40.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Identify and prioritize data that needs backup.
* ​Employ different kinds of backup methods, depending on the situation.
* Establish efficient backup and restore strategies.
* Use different backup utilities, such as **cpio**, **tar**, **gzip**, **bzip2**, **xz**, **dd**, **rsync**, **dump**, **restore**, and **mt**.
* Describe the two most well-known backup programs, Amanda and Bacula.
  
### 40.4 Why Backups?
----
* Data is valuable
* Hardware fails
* Software fails
* People make mistakes
* Malicious people can cause deliberate damage
* Unexplained events happen
* Rewinds can be useful
  
### 40.5: What Needs Backup?
----
Backup priorities:  
1. Definitely:
    * Business-related data
    * System configuration files
    * User files (usually under /home)
2. Maybe:
    * Spooling directories (for printing, mail, etc.)
    * Logging files (found in /var/log, and elsewhere) (can be useful for detecting intrusions and other security violations)
3. Probably not:
    * Software that can easily be re-installed; on a well-managed system, this should be almost everything
    * The /tmp directory, because its contents are indeed supposed to be only temporary
4. Definitely not:
    * Pseudo-filesystems such as /proc, /dev and /sys
    * Any swap partitions or files
  
### 40.6: Backup vs. Archive
----
Media lifetimes before becoming unreadable (conventional estimates):
* Magnetic Tapes: 10-30 years
* CDs and DVDs: 3-10 years
* Hard Disks: 2-5 years
  
For archiving, use multiple copies and copy to newer media from time to time.  
  
### 40.7: Tape Drives
----
* relatively slow and permit only sequential access
* not usually used for primary backup
* modern tape drives are usually of the LTO (Linear Tape Open) variety, whose first versions appeared in the late 1990s as an open standards alternative
* day-to-day backups are usually done with some form of NAS (Network Attached Storage) or with cloud-based solutions
  
### 40.8: Backup Methods
----
* store backups in different physical locations
* several different kinds of backup methods can be used, often in concert with each other:
    * **Full**: backup all files on the system
    * **Incremental**: backup all files that have changed since the last incremental or full backup
    * **Differential**: backup all files that have changed since the last full backup
    * **Multiple level incremental**: backup all files that have changed since the previous backup at the same or a previous level
    * **User**: backup only files in a specific user's directory
  
### 40.9: Backup Strategies
----
* backup methods are useless without associated **restore** methods
* the simplest backup scheme is to do a full backup of everything once, and then perform incremental backups of everything that subsequently changes
    * full backups can take a lot of time
    * restoring from incremental backups can be more difficult and time consuming
    * using a mix of both can optimize time and effort.
* example:
    1. use drive 1 for a full backup on Friday
    2. use drives 2-5 for incremental backups on Monday-Thursday
    3. use drive 6 for full backup on second Friday
    4. use drives 2-5 for incremental backups on second Monday-Thursday
    5. do not overwrite drive 1 until completion of full backup on drive 6
    6. after full backup to drive 6, move drive 1 to external location for disaster recovery
    7. for next full backup (next Friday) get drive 1 and exchange for drive 6
* a good rule of thumb is to have at least two weeks of backups available
  
### 40.10: Backup Utilities
----
* programs available for backup purposes:
    * **cpio**: create and extract archives of files
    * **tar**: create and extract archives of files
    * **gzip**, **bzip2**, **xz**: compress archives made with **cpio** or **tar**
    * **dd**: often used to transfer raw data between media; it can copy entire partitions or entire disks
    * **rsync**: can synchronize directory subtrees or entire filesystems across a network, or between different filesystem locations on a local machine
    * **dump and restore**: ancient utilities designed specifically for backups
        * they read from the filesystem directly (which is more efficient)
        * however, they must be restored only on the same filesystem type that they came from * there are newer alternatives
    * **mt**: useful for querying and positioning tapes before performing backups and restores
  
### 40.11: Using tar for Backups
----
* **tar** is easy to use:
    * when creating a **tar** archive, for each directory given as an argument, all files and subdirectories will be included in the archive
    * when restoring, it reconstitutes directories as necessary
    * the **--newer** option allows incremental backups
    * can handle backups that do not fit on one device you use
* example use:
    * `$ tar --create --file /dev/st0 /root` create an archive using **--create**; **-f** or **--file** option is for specifying a device or file
    * `$ tar -cvf /dev/st0 /root` create an archive using **-c**
    * `$ tar -cMf /dev/st0 /root` create archive with multi-volume option, using **-M** or **--multi-volume** if backup won't fit on one device; **tar** will prompt for next device when needed
    * `$ tar --compare --verbose --file /dev/st0` verify files after backup with the compare option, using **--compare**
    * `$ tar -dvf /dev/st0` verify files after backup with the compare option, using **-d**
* **tar** will recursively include all subdirectories by default
* single-dashed **tar** options can be used with or without dashes unless followed by an option like **--newer** (with two dashes) ("this kind of option specification confusion sometimes occurs with old UNIX utilities like **ps** and **tar** with complicated histories involving different families of UNIX")
  
### 40.12: Using tar for Restoring Files
----
* the **-x** or **--extract** option extracts files from an archive, all by default
    * one can narrow the file extraction list by specifying only particular files
    * if a directory is specified, all included files and subdirectories are also extracted
* the **-p** or **--same-permissions** options ensures files are restored with their original permissions
* the **-t** or **--list** option lists, but does not extract, the files in the archive.
* example use:
    * `$ tar --extract --same-permissions --verbose --file /dev/st0` extract from an archive
    * `$ tar -xpvf /dev/st0` extract from an archive
    * `$ tar xpvf /dev/sts0` extract from an archive
    * `$ tar xvf /dev/st0 somefile` specify only specific files to restore
    * `$ tar --list --file /dev/st0` list the contents of a tar backup
    * `$ tar -tf /dev/st0` list the contents of a tar backup
  
### 40.13: Incremental Backups with tar
----
* the **-N** (or the equivalent **--newer**), or the **--after-date** options are used for incremental backups
    * either option requires specifying either a date or a qualified (reference) file name:
    * `$ tar --create --newer '2011-12-1' -vf backup1.tar /var/tmp` creates a backup archive of all files in /var/tmp which were modified after December 1, 2011
    * `$ tar --create --after-date '2011-12-1' -vzf backup1.tar /var/tmp` creates a backup archive of all files in /var/tmp which were modified after December 1, 2011
* **tar** only looks at a file's date, so it does not consider any other changes to the file, such as permissions or file name
    * to include files with these changes in the incremental backup, use **find** and create a list of files to be backed up
  
### 40.14: Archive Compression Methods
----
* available compression schemes in order of increasing compression efficiency (at the cost of longer compression times):
    * **gzip**: uses Lempel-Ziv Coding (LZ77) and produces **.gz** files
    * **bzip2**: uses Burrows-Wheeler block sorting text compression algorithm and Huffman coding, and produces **.bz2** files
    * **xz**: produces **.xz** files and also supports legacy **.lzma** format.
* for day-to-day use on smaller files, one usually just uses **gzip**, as it is extremely fast, but, for larger files and for archiving purposes, the other two methods are often used, such as for [The Linux Kernel Archives](https://www.kernel.org/)
* the **.zip** format is rarely used in Linux, except to extract legacy archives from other operating systems
* the compression utilities are very easily (and often) used in combination with tar:
    * `$ tar zcvf source.tar.gz source` produce a compressed archive with **gzip** (**z** option)
    * `$ tar jcvf source.tar.bz2 source` produce a compressed archive with **bzip2** (**j** option)
    * `$ tar Jcvf source.tar.xz source` produce a compressed archive with **xz** (**J** option)
    * `$ tar cvf source.tar source ; gzip -v source.tar` same as `$ tar zcvf source.tar.gz source` but is more efficient because:
        * there is no intermediate file storage
        * archiving and compression happen simultaneously in the pipeline
* for decompression:
    * `$ tar xzvf source.tar.gz` decompress a **gzip** archive
    * `$ tar xjvf source.tar.bz2` decompress a **bzip2** archive
    * `$ tar xJvf source.tar.xz` decompress a **xz** archive
    * `$ tar xvf source.tar.gz`decompress a **gzip** archive without specifying the type of compressions
  
### 40.15: dd
----
* **dd** is one of the original UNIX utilities and is extremely versatile
    * without options, it does a very low-level raw copying of files or even whole disks
    * capable of doing many kind of data conversions during the copy (such as changing byte order) and has many options to control offsets, number of bytes, block size, etc.
* **dd** is often used to read fixed amounts of data from special device nodes such as /dev/zero or /dev/random
    * `$ dd if=input-file of=output-file options` general syntax; converts and copies files; **stdin** and **stdout** used if input or output files are not specified
  
### 40.16: dd Examples
----
* **dd** example use:
    * `$ dd if=/dev/zero of=outfile  bs=1M count=10` create a 10 MB file filled with zeros
    * `$ dd if=/dev/sda of=/dev/sdb` backup an entire hard drive to another (raw copy)
    * `$ dd if=/dev/sda of=sdadisk.img` create an image of a hard disk (which could later be transferred to another hard disk)
    * `$ dd if=/dev/sda1 of=partition1.img` backup a partition
    * `$ dd if=ndata conv=swab count=1024 | uniq > ofile` use **dd** in a pipeline

### 40.17: rsync
----
* **rsync** (**r**emote **sync**hronize) is used to transfer files across a network (or between different locations on the same machine)
    * `$ rsync [options] source destination` general syntax
* the source and destination can take the form of target:path where target can be in the form of [user@]host
* examples:
    * `$ rsync file.tar someone@backup.mydomain:/usr/local`
    * `$ rsync -r a-machine:/usr/local b-machine:/usr/`
    * `$ rsync -r --dry-run /usr/local /BACKUP/usr`
* use **--dry-run** option first
* **rsync** will copy only differences when copying one directory to a similar directory
* the **-r** option causes rsync to recursively walk down the directory tree copying all files and directories below the one listed as the sourcefile
    * `$ rsync -r project-X archive-machine:archives/project-X` back up a project directory using **-r**
* a simple (and very effective and very fast) backup strategy is to simply duplicate directories or partitions across a network with rsync commands and to do so frequently
  
### 40.18: cpio 
----
* **cpio** (**c**o**p**y **i**n and **o**ut) is a general file archiver utility that has been around since the earliest days of UNIX and was originally designed for tape backups
    * **rpm2cpio** to convert RPM packages into **cpio** archives and then extract them
    * the Linux kernel uses a version of **cpio** internally to deal with **initramfs** and **initrd** initial ram filesystems and disks during boot
* one reason **cpio** lives on is that it is lighter than **tar** and other successors, even if it is somewhat less robust
* example use:
    * `$ ls | cpio --create -O /dev/st0` create an archive, use **-o** or **--create**
    * `$ cpio -i somefile -I /dev/st0` extract from an archive, use **-i** or **--extract**
    * `$ cpio -t -I /dev/st0` list contents of an archive, use **-t** or **--list**
* specify the input (**-I** device) or use redirection on the command line
* during extraction, patterns can be used to copy only certain files from the archive
  
### 40.19: dump and restore
----
* **dump** and **restore** utilities have been around since the earliest days of UNIX and were not originally designed for modern hardware, filesystems, and storage capacities
* unlike **cpio** and **tar**, they directly read and write the filesystem, which is more efficient
* advantages
    * can perform full or incremental backups
    * understand specific filesystem format and how to read and write it
    * efficient when creating full backups, head movement reduced
    * can specify output tape size and density, block size and count, or even both
    * can dump to any valid device or file; defaults to `/dev/tape`
    * parameters in `/etc/fstab` control what gets dumped and when
* disadvantages:
    * multiple filesystem passes are required for backups
    * only works with ext2, ext3, and ext4 filesystems; other filesystems may have their own utilities, such as **xfsdump**
    * cannot be run safely on mounted filesystems
* sometimes used by higher-level backup program suites, such as Amanda, so some familiarity is useful
  
### 40.20: dump Options
----
Skipped - **dump** is not installed on my CentOS 7 system
  
### 40.21: restore
----
* `$ sudo restore -rvf /tmp/boot_backup` restore all files that were dumped, relative to current directory
* **restore** is not installed on my CentOS 7 system
  
### 40.22: mt
----
* `$ mt [-h] [-f device] operation [count] [arguments...]` used to control magnetic tape devices
* **mt** is not installed on my CentOS 7 system
* example use:
    * `$ mt status` how status information about the tape device
    * `$ mt rewind` rewind the tape
    * `$ mt erase` erase the tape
    * `$ mt fsf` move forward to the end of the current archive
  
### 40.23: Backup Programs
----
Some well known backup programs:
1. **Amanda** (**A**dvanced **M**aryland **A**utomatic **N**etwork **D**isk **A**rchiver) uses native utilities (including tar and dump) but is far more robust and controllable
2. **Bacula** is designed for automatic backup on heterogenous networks
    * complicated to use and is recommended (by its authors) only to experienced administrators
3. **Clonezilla** is a very robust disk cloning program, which can make images of disks and deploy them, either to restore a backup, or to be used for ghosting, to provide an image that can be used to install many machines
    * not very hard to use and is extremely flexible, supporting many operating systems (not just Linux), filesystem types, and boot loaders

### Lab 40.1: Using tar for Backup
----
1. Create a directory called `backup` and in it place a compressed **tar** archive of all the files under `/usr/include`, with the highest level directory being `include`. You can use any compression method (**gzip**, **bzip2** or **xzip**).
    * `$ tar jcvf /tmp/backup/backup.tar.bz2 /usr/include`
    * solution: `$ cd /usr ; tar jcvf /tmp/backup/include.tar.bz2 include` or `$ tar -C /usr -jcf include.tar.bz2 include`
2. List the files in the archive.
    * `$ tar -tvf /tmp/backup/backup.tar.bz2`
    * solution: `$ ls -lh include.tar.*` - that does not show all of the files in the archive!'
    * solution: `$ tar tvf include.tar.xz`
3. Create a directory called `restore` and unpack and decompress the archive.
    * `$ tar xjvf /tmp/backup/backup.tar.bz2 /tmp/restore` - didn't work
    * `$ tar xjvf /tmp/backup/backup.tar.bz2` - default is whatever directory I'm in, it seems
4. Compare the contents with the original directory the archive was made from.
    * `$ tar -dvf /tmp/backup/backup.tar.bz2`
        ```
        ...
        usr/include/libio.h
        usr/include/libio.h: Uid differs
        usr/include/libio.h: Gid differs
        ```
    * solution: `$ cd .. ; mkdir restore ; cd restore`
    * solution: `$ tar xvf ../backup/include.tar.bz2`
    * solution: `student@centos: restore $ diff -qr usr/include /usr/include` - no difference
  
### Lab 40.2: Using cpio for Backup
----
1. Create a directory called `backup` and in it place a compressed **cpio** archive of all the files under `/usr/include`, with the highest level directory being `include`. You can use any compression method (gzip, bzip2 or xzip).
    * solution: `$ find include | cpio -c -o | gzip -c > /tmp/backup/include.cpio.gz`
        > 92536 blocks
2. List the files in the archive.
    * solution: `$ cpio -ivt < include.cpio` - "Permission denied"
3. Create a directory called `restore` and unpack and decompress the archive.
    * solution: `$ cd ../restore`
    * solution: `$ cat ../backup/include.cpio | cpio -ivt` - ??
    * solution: `$ gunzip -c include.cpio.gz | cpio -ivt` - ??
4. Compare the contents with the original directory the archive was made from.
    * solution:
        * `$ rm -rf include` - 'include' doesn't exist
        * `$ cpio -id < ../backup/include.cpio`
        * `$ ls -lR include`
    * or
        * `$ cpio -idv < ../backup/include.cpio`
        * `$ diff -qr include /usr/include`

### Lab 40.3: Using rsync for Backup
----
1. Using **rsync**, we will again create a complete copy of `/usr/include` in your `backup` directory:
    * `$ rm -rf include`
    * `$ rsync -av /usr/include .`
2. Let’s run the command a second time and see if it does anything:
    * `$ rsync -av /usr/include .`
        ```
        sending incremental file list

        sent 137,432 bytes  received 249 bytes  275,362.00 bytes/sec
        total size is 46,407,916  speedup is 337.07
        ```
3. One confusing thing about **rsync** is you might have expected the right command to be:
    * `$ rsync -av /usr/include include`
    * However, if you do this, you’ll find it actually creates a new directory, `include/include`!
4. To get rid of the extra files you can use the --delete option:
    * `$ rsync -av --delete /usr/include .`
5. For another simple exercise, remove a subdirectory tree in your backup copy and then run **rsync** again with and without the `--dry-run` option:
    * `$ rm -rf include/xen`
    * `$ rsync -av --delete --dry-run /usr/include .`
        ```
        sending incremental file list
        include/
        include/xen/
        include/xen/evtchn.h
        include/xen/privcmd.h

        sent 137,446 bytes  received 263 bytes  275,418.00 bytes/sec
        total size is 46,407,916  speedup is 337.00 (DRY RUN)
        ```
    * `$ rsync -av --delete /usr/include .` - directory is added back? but maybe not because of the `--delete` option - that just deletes files that have been removed from the source
        ```
        sending incremental file list
        include/
        include/xen/
        include/xen/evtchn.h
        include/xen/privcmd.h

        sent 143,992 bytes  received 311 bytes  288,606.00 bytes/sec
        total size is 46,407,916  speedup is 321.60
        ```
6. A simple script with a good set of options for using **rsync**:
    ```
    #!/bin/sh
    set -x
    rsync --progress -avrxH -e "ssh -c blowfish" --delete $*
    ```
    * ...which will work on a local machine as well as over the network. Note the important -x option which stops **rsync** from crossing filesystem boundaries.
* Extra Credit: For more fun, if you have access to more than one computer, try doing these steps with source and destination on
different machines.
  
### Paths and Commands
----
  
#### Paths  
  
Topics | Path | Notes | Reference
------ | ---- | ----- | ---------

#### Commands  
  
Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
backup | `$ tar --create --file /dev/st0 /root` | create an archive using **--create**; **-f** or **--file** option is for specifying a device or file | LFS201 40.11
backup | `$ tar -cvf /dev/st0 /root` | create an archive using **-c** | LFS201 40.11
backup | `$ tar -cMf /dev/st0 /root` | create archive with multi-volume option, using **-M** or **--multi-volume** if backup won't fit on one device; **tar** will prompt for next device when needed | LFS201 40.11
backup | `$ tar --compare --verbose --file /dev/st0` | verify files after backup with the compare option, using **--compare** | LFS201 40.11
backup | `$ tar -dvf /dev/st0` | verify files after backup with the compare option, using **-d** | LFS201 40.11
backup | `$ tar --extract --same-permissions --verbose --file /dev/st0` | extract from an archive | LFS201 40.12
backup | `$ tar -xpvf /dev/st0` | extract from an archive | LFS201 40.12
backup | `$ tar xpvf /dev/sts0` | extract from an archive | LFS201 40.12
backup | `$ tar xvf /dev/st0 somefile` | specify only specific files to restore | LFS201 40.12
backup | `$ tar --list --file /dev/st0` | list the contents of a tar backup | LFS201 40.12
backup | `$ tar -tf /dev/st0` | list the contents of a tar backup | LFS201 40.12
backup | `$ tar --create --newer '2011-12-1' -vf backup1.tar /var/tmp` | creates a backup archive of all files in /var/tmp which were modified after December 1, 2011 | LFS201 40.13
backup | `$ tar --create --after-date '2011-12-1' -vzf backup1.tar /var/tmp` | creates a backup archive of all files in /var/tmp which were modified after December 1, 2011 | LFS201 40.13
backup | `$ tar zcvf source.tar.gz source` | produce a compressed archive with **gzip** (**z** option) | LFS201 40.14
backup | `$ tar jcvf source.tar.bz2 source` | produce a compressed archive with **bzip2** (**j** option) | LFS201 40.14
backup | `$ tar Jcvf source.tar.xz source` | produce a compressed archive with **xz** (**J** option) | LFS201 40.14
backup | `$ tar cvf source.tar source ; gzip -v source.tar` | same as `$ tar zcvf source.tar.gz source` but is more efficient | LFS201 40.14
backup | `$ tar xzvf source.tar.gz` | decompress a **gzip** archive; do in destination directory | LFS201 40.14
backup | `$ tar xjvf source.tar.bz2` | decompress a **bzip2** archive; do in destination directory | LFS201 40.14
backup | `$ tar xJvf source.tar.xz` | decompress a **xz** archive; do in destination directory | LFS201 40.14
backup | `$ tar xvf source.tar.gz` | decompress a **gzip** archive without specifying the type of compressions; do in destination directory | LFS201 40.14
backup | `$ dd if=input-file of=output-file options` | general syntax; converts and copies files | LFS201 40.15
backup | `$ dd if=/dev/zero of=outfile  bs=1M count=10` | create a 10 MB file filled with zeros | LFS201 40.16
backup | `$ dd if=/dev/sda of=/dev/sdb` | backup an entire hard drive to another (raw copy) | LFS201 40.16
backup | `$ dd if=/dev/sda of=sdadisk.img` | create an image of a hard disk (which could later be transferred to another hard disk) | LFS201 40.16
backup | `$ dd if=/dev/sda1 of=partition1.img` | backup a partition | LFS201 40.16
backup | `$ dd if=ndata conv=swab count=1024 | uniq > ofile` | use **dd** in a pipeline | LFS201 40.16
backup | `$ rsync [options] source destination` | general syntax; sync files | LFS201 40.17
backup | `$ rsync file.tar someone@backup.mydomain:/usr/local` | example rsync use | LFS201 40.17
backup | `$ rsync -r a-machine:/usr/local b-machine:/usr/` | example rsync use | LFS201 40.17
backup | `$ rsync -r --dry-run /usr/local /BACKUP/usr` | example rsync use | LFS201 40.17
backup | `$ rsync -r project-X archive-machine:archives/project-X` | back up a project directory using **-r** | LFS201 40.17
backup | `$ ls | cpio --create -O /dev/st0` | create an archive, use **-o** or **--create** | LFS201 40.18
backup | `$ cpio -i somefile -I /dev/st0` | extract from an archive, use **-i** or **--extract** | LFS201 40.18
backup | `$ cpio -t -I /dev/st0` | list contents of an archive, use **-t** or **--list** | LFS201 40.18
backup | `$ sudo restore -rvf /tmp/boot_backup` | restores all files that were dumped, relative to current directory | LFS201 40.21
backup | `$ mt [-h] [-f device] operation [count] [arguments...]` | generic syntax; used to control magnetic tape devices | LFS201 40.22
backup | `$ mt status` | how status information about the tape device | LFS201 40.22
backup | `$ mt rewind` | rewind the tape | LFS201 40.22
backup | `$ mt erase` | erase the tape | LFS201 40.22
backup | `$ mt fsf` | move forward to the end of the current archive | LFS201 40.22
backup | `$ tar tvf /tmp/backup/backup.tar.bz2` | list files in the archive | Lab 40.1
backup | `$ diff -qr include /usr/include` | compare the contents with the original directory the archive was made from | Lab 40.1
backup | `$ find include | cpio -c -o | gzip -c > /tmp/backup/include.cpio.gz` | place compressed **cpio** archive of all the files under `/usr/include` in `/tmp/backup` | Lab 40.2
backup | `$ cpio -ivt < include.cpio` | list the files in the archive | Lab 40.2
backup | `$ rsync -av /usr/include .` | create a complete copy of `/usr/include` in the current directory | Lab 40.3
backup | `$ rsync -av --delete /usr/include .` | remove files that are no longer in source (?) | Lab 40.3
backup | `$ rsync -av --delete --dry-run /usr/include .` | see which files will be removed or added if the command was run | Lab 40.3
  


### Ch.40
* Lab 40.1
    1. Create a directory called backup and in it place a compressed tar archive of all the files under /usr/include, with the highest level directory being include. You can use any compression method (gzip, bzip2 or xzip).
    ```
    1. $ mkdir /tmp/backup
    $ cd /usr ; tar zcvf /tmp/backup/include.tar.gz include $ cd /usr ; tar jcvf /tmp/backup/include.tar.bz2 include $ cd /usr ; tar Jcvf /tmp/backup/include.tar.xz include
    or
        $ tar -C /usr -zcf include.tar.gz include
        $ tar -C /usr -jcf include.tar.bz2 include
        $ tar -C /usr -Jcf include.tar.xz include
    Notice the efficacy of the compression between the three methods:
        $ du -sh /usr/include
        55M        /usr/include
    ```
    2. List the files in the archive.
    ```
    2. $ ls -lh include.tar.*
        c7:/tmp/backup>ls -lh
        total 17M
        -rw-rw-r-- 1 coop coop 5.3M Jul 18 08:17 include.tar.bz2
        -rw-rw-r-- 1 coop coop 6.7M Jul 18 08:16 include.tar.gz
        -rw-rw-r-- 1 coop coop 4.5M Jul 18 08:18 include.tar.xz
        c7:/tmp/backup>
    ```
    3. Create a directory called restore and unpack and decompress the archive.
    ```
    3. $ tar tvf include.tar.xz
    qdrwxr-xr-x root/root
    -rw-r--r-- root/root
    -rw-r--r-- root/root
    -rw-r--r-- root/root
    -rw-r--r-- root/root
    .....
        0 2014-10-29 07:04 include/
    42780 2014-08-26 12:24 include/unistd.h
    957 2014-08-26 12:24 include/re_comp.h
    22096 2014-08-26 12:24 include/regex.h
    7154 2014-08-26 12:25 include/link.h
    Note it is not necessary to give the j, J, or z option when decompressing; tar is smart enough to figure out what is needed.
    ```
    4. Compare the contents with the original directory the archive was made from.
    ```
    4. $ cd .. ; mkdir restore ; cd restore $ tar xvf ../backup/include.tar.bz2
    include/
    include/unistd.h
    include/re_comp.h
    include/regex.h
    include/link
    .....
    $ diff -qr include /usr/include
    ```

* Lab 40.2
1. Create a directory called backup and in it place a compressed cpio archive of all the files under /usr/include, with the highest level directory being include. You can use any compression method (gzip, bzip2 or xzip).
```
1. $ (cd /usr ; find include | cpio -c -o > /home/student/backup/include.cpio) 82318 blocks
or to put it in a compressed form:
     $ (cd /usr ; find include | cpio -c -o | gzip -c > /home/student/backup/include.cpio.gz)
82318 blocks
     $ ls -lh include*
     total 64M
     -rw-rw-r-- 1 coop coop  41M Nov  3 15:26 include.cpio
     -rw-rw-r-- 1 coop coop 6.7M Nov  3 15:28 include.cpio.gz
     -rw-rw-r-- 1 coop coop 5.3M Nov  3 14:44 include.tar.bz2
     -rw-rw-r-- 1 coop coop 6.8M Nov  3 14:44 include.tar.gz
     -rw-rw-r-- 1 coop coop 4.7M Nov  3 14:46 include.tar.xz
```
2. List the files in the archive.
```
2. $ cpio -ivt < include.cpio drwxr-xr-x 86 root root
    0 Oct 29 07:04 include
42780 Aug 26 12:24 include/unistd.h
  957 Aug 26 12:24 include/re_comp.h
22096 Aug 26 12:24 include/regex.h
-rw-r--r--
-rw-r--r--
-rw-r--r--
.....
1 root     root
1 root     root
1 root     root
Note the redirection of input; the archive is not an argument. One could also do:
   $ cd ../restore
   $ cat ../backup/include.cpio | cpio -ivt
   $ gunzip -c include.cpio.gz | cpio -ivt
```
3. Create a directory called restore and unpack and decompress the archive.
```
3. $ rm -rf include
$ cpio -id < ../backup/include.cpio $ ls -lR include
or
   $ cpio -idv < ../backup/include.cpio
   $ diff -qr include /usr/include
```
4. Compare the contents with the original directory the archive was made from.
```
```

* Lab 40.3
1. Using rsync, we will again create a complete copy of /usr/include in your backup directory:
```
     $ rm -rf include
     $ rsync -av /usr/include .
     sending incremental file list
     include/
     include/FlexLexer.h
     include/_G_config.h
     include/a.out.h
     include/aio.h
     .....
```
2. Let’s run the command a second time and see if it does anything:
```
     $ rsync -av /usr/include .
     sending incremental file list
     sent 127398 bytes  received 188 bytes  255172.00 bytes/sec
     total size is 41239979  speedup is 323.23
```
3. One confusing thing about rsync is you might have expected the right command to be: 
```
$ rsync -av /usr/include include
     sending incremental file list
     ...
However, if you do this, you’ll find it actually creates a new directory, include/include!
```
4. To get rid of the extra files you can use the --delete option:
```
     $ rsync -av --delete /usr/include .
     sending incremental file list
     include/
     deleting include/include/xen/privcmd.h
     deleting include/include/xen/evtchn.h
     ....
     deleting include/include/FlexLexer.h
     deleting include/include/
     sent 127401 bytes  received 191 bytes  85061.33 bytes/sec
     total size is 41239979  speedup is 323.22
```
5. For another simple exercise, remove a subdirectory tree in your backup copy and then run rsync again with and without the --dry-run option:
```
     $ rm -rf include/xen
     $ rsync -av --delete --dry-run /usr/include .
     sending incremental file list
     include/
     include/xen/
     include/xen/evtchn.h
     include/xen/privcmd.h
     sent 127412 bytes  received 202 bytes  255228.00 bytes/sec
     total size is 41239979  speedup is 323.16 (DRY RUN)
     $ rsync -av --delete  /usr/include .
```

### Ch.41
* Lab 41.1
    * **Extra:** SELinux: contexts
* Lab 41.2
    * **Extra:** apparmor

### Ch.42
### 42.15: Using Secure Mounting Options
----
* **mount** option that enhance security:
    * **nodev**: do not interpret character or block special devices on the filesystem
    * **nosuid**: the **set-user-identifier** or **set-group-identifier** bits are not to take effect
    * **noexec**: restrict direct execution of any binaries on the mounted filesystem
    * **ro**: mount the filesystem in **read-only** mode as in `$ mount -o ro,noexec,nodev /dev/sda2 /mymountpt` or in `/etc/fstab`: `/dev/sda2 /mymountpt  ext4 ro,noexec,nodev 0 0`
  
### 42.16: setuid and setgid
----
* programs normally run with the privileges of the user rather than the owner
* setting the **setuid** (**set u**ser **ID**) flag on an executable file can give the program the access rights of the **owner** rather than the **user**
* setting the **setgid** bit can give the privileges of the group rather than the user
    * using the **setgid** setting on the directory changes this so that files created in the directory are group owned by the group owner of the directory
* this is generally a **bad idea** and should be avoided in most circumstances; write a **daemon** program with lesser privileges instead
    * some distros have disabled this ability entirely
### 42.17: Setting the setuid/setgid Bits
----
* `$ chmod u+s somefile` 'does the setuid'
* `$ chmod g+s somefile` 'does the setgid'
* `$ chmod g+s somedir` creates a shared directory; files created here are group owned by the group owner of the directory
* you can't effectively change the **setuid** on a shell script file; in fact, it won't do anything unless you actually change the **setuid** bit on the shell itself, which would be a terrible security hole

### Lab 42.1: Security and Mount Options
----
1. Set up an empty file, put a filesystem on it and mount it.
    * `$ sudo fdisk /dev/sda`
    * `$ sudo partprobe -s`
    * `$ dd if=/dev/zero of=imagefile bs=1M count=1024`
    * `$ mkfs.ext4 imagefile`
    * `$ sudo mkdir /mnt/mntpoint`
    * `$ sudo mount -o loop imagefile /mnt/mntpoint`
2. Copy an executable file to it from somewhere else on your system and test that it works in the new location.
    * `$ sudo cp /bin/ls /mnt/mntpoint`
    * `$ cd /mnt/mntpoint`
    * `$ ls .`
    * `$ cd ~`
3. Unmount it and remount with the noexec option.
    * `$ sudo umount /mnt/mntpoint`
    * `$ sudo mount -o noexec,loop imagefile /mnt/mntpoint`
4. Test if the executable still works. It should give you an error because of the noexec mount option.
    * `$ /mnt/mntpoint/ls .`
5. Clean up.
    * `$ sudo umount /mnt/mntpoint`
    * `$ sudo fdisk /dev/sda`
    * `$ sudo partprobe -s`
6. To make persistant, add something like the following to `/etc/fstab`:
    > /home/student/image  /home/student/mountpoint  ext3  loop,rw,noexec 0 0  

* Lab 42.1
1. Set up an empty file, put a filesystem on it and mount it.
```
1. $ dd if=/dev/zero of=image bs=1M count=100 $ sudo mkfs.ext3 image
$ mkdir mountpoint
$ sudo mount -o loop image mountpoint
```
2. Copy an executable file to it from somewhere else on your system and test that it works in the new location.
```
2. $ sudo cp /bin/ls mountpoint $ mountpoint/ls
```
3. Unmount it and remount with the noexec option.
```
3. $ sudo umount mountpoint
$ sudo mount -o noexec,loop image mountpoint
or
     $ sudo mount -o noexec,remount image mountpoint
```
4. Test if the executable still works. It should give you an error because of the noexec mount option.
```
4. $ mountpoint/ls
```
5. Clean up.
```
5. $ sudo umount mountpoint $ rm image
     $ rmdir mountpoint
     Note that this is not persistent. To make it persistent you would need to add the option to /etc/fstab with a line like:
    /home/student/image  /home/student/mountpoint    ext3    loop,rw,noexec 0 0
```

DO LAB 42.2

### 43.8: Things to Check: File Integrity
----
Packaging systems have methods of verifying file integrity and checking for changes:
* `$ rpm -V some_package` verify file integrity for a single package
* `$ rpm -Va` check all packages on the system
* `$ debsums options some_package` check integrity of the filed in some_package; however, not all packages maintain checksums so this might not be completely useful
* **aide** is used for  **intrusion detection** and is another way to check for changes in files
* `$ sudo aide --check` run a scan on your files and compare them to the last scan
