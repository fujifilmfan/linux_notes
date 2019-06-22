Miscellaneous Items
-----

### Chapters: LFS101: , LFS201: 40, 42


### dd
* create a 10 MB file filled with zeros - `$ dd if=/dev/zero of=outfile  bs=1M count=10` 
* backup an entire hard drive to another (raw copy) - `$ dd if=/dev/sda of=/dev/sdb` 
* create an image of a hard disk (which could later be transferred to another hard disk) - `$ dd if=/dev/sda of=sdadisk.img` 
* backup a partition - `$ dd if=/dev/sda1 of=partition1.img` 
* use **dd** in a pipeline - `$ dd if=ndata conv=swab count=1024 | uniq > ofile` 
 
### Use tar to create and restore a backup
* create a directory called backup and in it place a compressed tar archive of all the files under /usr/include, with the highest level directory being included - `$ cd /usr ; tar cvf /tmp/backup/include.tar include` or `$ tar -C /usr -jcf include.tar.bz2 include` or with `--create` instead of `c`
* create a directory called backup and in it place a compressed tar archive of all the files under /usr/include, not including the highest level directory
* create a directory called backup and in it place a compressed tar archive of all the files under /usr/include, with gzip compression - `$ cd /usr ; tar zcvf /tmp/backup/include.tar.gz include` (`j` for bzip2, `J` for xz; could also do `gzip -v source.tar` after archiving)
* list the files in the archive - `$ ls -lh include.tar.*`, `$ tar tvf include.tar.xz` or with `--list` instead of `t`
* create a directory called restore and unpack and decompress the archive - `$ tar xvf ../backup/include.tar.bz2` (decompression method does not need to be specified) or with `--extract` instead of `x`, `-C somedir` or `--directory somedir`
* extract only specific files from the archive - `$ tar xvf /dev/st0 somefile`
* compare the contents with the original directory the archive was made from - `$ diff -qr include /usr/include`; try `$ tar -dvf /tmp/backup/backup.tar.bz2` (`d` for `--compare`, check out Uid and Gid - would using --same-permissions work?)
* creates a backup archive of all files in /var/tmp which were modified after December 1, 2011 - `$ tar --create --newer '2011-12-1' -vf backup1.tar /var/tmp` or using `--after-date` instead of `--newer`

### Use cpio to create and restore a backup
* create a directory called backup and in it place a compressed cpio archive of all the files under /usr/include, with the highest level directory being included and using any compression method - `$ (cd /usr ; find include | cpio -c -o > /home/student/backup/include.cpio)` (`-o` for `--create`) or `$ (cd /usr ; find include | cpio -c -o | gzip -c > /home/student/backup/include.cpio.gz)` or `$ ls | cpio --create -O /dev/st0` (`-O` to specify archive filename)
* compare the sizes of all of the include* files - `$ ls -lh include*`
* list the files in the archive - `$ cpio -ivt < include.cpio` or `$ cat ../backup/include.cpio | cpio -ivt` or `$ cpio -t -I /dev/st0` (`-I` to specify archive filename)
* create a directory called restore and unpack and decompress the archive - `$ cpio -id < ../backup/include.cpio`, `$ ls -lR include` or `$ cpio -idv < ../backup/include.cpio` or `$ cpio -i somefile -I /dev/st0` (`-I` to specify archive filename)
* compare the contents with the original directory the archive was made from - `$ diff -qr include /usr/include`

### Use rsync to create and restore a backup
* use rsync to create a complete copy of /usr/include in your backup directory - `$ rsync -av /usr/include .`
* run the command a second time and see if it does anything - `$ rsync -av /usr/include .`
* get rid of the extra files - `$ rsync -av --delete /usr/include .`
* remove a subdirectory tree in your backup copy and then run rsync again with and without the --dry-run option - `$ rm -rf include/xen`, `$ rsync -av --delete --dry-run /usr/include .`, `$ rsync -av --delete  /usr/include .`
 
### rsync
----
* the source and destination can take the form of target:path where target can be in the form of [user@]host
* examples:
    * `$ rsync file.tar someone@backup.mydomain:/usr/local`
    * `$ rsync -r a-machine:/usr/local b-machine:/usr/`
    * `$ rsync -r --dry-run /usr/local /BACKUP/usr`

* the **-r** option causes rsync to recursively walk down the directory tree copying all files and directories below the one listed as the sourcefile
    * `$ rsync -r project-X archive-machine:archives/project-X` back up a project directory using **-r**
* a simple (and very effective and very fast) backup strategy is to simply duplicate directories or partitions across a network with rsync commands and to do so frequently

### 42.17: Setting the setuid/setgid Bits
----
* `$ chmod u+s somefile` 'does the setuid'
* `$ chmod g+s somefile` 'does the setgid'
* `$ chmod g+s somedir` creates a shared directory; files created here are group owned by the group owner of the directory

DO LAB 42.2

### 43.8: Things to Check: File Integrity
----
Packaging systems have methods of verifying file integrity and checking for changes:
* `$ rpm -V some_package` verify file integrity for a single package
* `$ rpm -Va` check all packages on the system
* `$ debsums options some_package` check integrity of the filed in some_package; however, not all packages maintain checksums so this might not be completely useful
* **aide** is used for  **intrusion detection** and is another way to check for changes in files
* `$ sudo aide --check` run a scan on your files and compare them to the last scan

### Extras
* 40.22: mt
* Lab 41.1: SELinux: contexts
* Lab 41.2: apparmor
