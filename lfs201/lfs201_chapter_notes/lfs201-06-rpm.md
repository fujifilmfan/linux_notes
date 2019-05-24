Chapter 6: RPM
--------------

[6.3: Learning Objectives](#63-learning-objectives)  
[6.4: RPM](#64-rpm)  
[6.5: Advantages of Using RPM](#65-advantages-of-using-rpm)  
[6.6: Package File Names](#66-package-file-names)  
[6.7: Database Directory](#67-database-directory)  
[6.8: Helper Programs and Modifying Settings](#68-helper-programs-and-modifying-settings)  
[6.9: Queries](#69-queries)  
[6.10: Verifying Packages](#610-verifying-packages)  
[6.11: Installing Packages](#611-installing-packages)  
[6.12: Uninstalling Packages](#612-uninstalling-packages)  
[6.13: Using the 'rpm' Command Demo](#613-using-the-rpm-command-demo)  
[6.14: Upgrading Packages](#614-upgrading-packages)  
[6.15: Freshening Packages](#615-freshening-packages)  
[6.16: Upgrading the Kernel](#616-upgrading-the-kernel)  
[6.17: Using rpm2cpio](#617-using-rpm2cpio)  
[Lab 6.1: Using RPM](#lab-61-using-rpm)  
[Lab 6.2: Rebuilding the RPM Database](#lab-62-rebuilding-the-rpm-database)  
[Paths and Commands](#paths-and-commands)  
  
### 6.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Understand how the **RPM** system is organized and what major operations the **rp** program can accomplish.
* Explain the naming conventions used for both binary and source **rpm** files.
* Know how to query, verify, install, uninstall, upgrade and freshen packages.
* Grasp why new kernels should be installed rather than upgraded.
* Know how to use **rpm2cpio** to copy packaged files into a **cpio** archive, as well as to extract the files without installing them.
* **yum** and **zypper** hanlded dependencies in a more robust fashion; rpm more for packages listed on command line (?)

### 6.4: RPM
----
* **RPM** == the **R**ed **H**at **P**ackage **M**anager
    * all files related to a specific task are packaged into a single rpm file
    * contains information about how and where to install and uninstall files
    * new versions of software lead to new rpm files which are then used for updating
* rpm files contain dependency information
* rpm does not retrieve from the network, but only from the local machine using absolute or relative paths
* rpm files are usually distribution-dependent

### 6.5: Advantages of Using RPM
----
For system administrators, RPM makes it easy to:  
* determine what package (if any) any file on the system is part of
* determine what version is installed
* install and uninstall (erase) packages without leaving debris behind
* verify that a package was installed correctly; this is useful for both troubleshooting and system auditing
* distinguish documentation files from the rest of the package and optionally decide not to install them to save space
* use ftp or HTTP to install packages over the Internet  

For developers RPM offers advantages as well:  
* software often is made available on more than one operating system; with RPM the original full and unmodified source is used as the basis, but a developer can separate out the changes needed to build on Linux
* more than one architecture can be built using only one source package

### 6.6: Package File Names
----
RPM package file names are based on fields that represent specific information, as documented in the RPM standard  (http://www.rpm.org/)  
* the standard naming format for a binary package is:  
    * <name>-<version>-<release>.<distro>.<architecture>.rpm  
    * sed-4.2.1-10.el6.x86_64.rpm  
* the standard naming format for a source package is:  
    * <name>-<version>-<release>.<distro>.src.rpm  
    * sed-4.2.1-10.el6.src.rpm  
Note that the distro field often actually specifies the repository that the package came from, as a given installation may use a number of different package repositories as we shall discuss when we discuss yum and zypper which work above RPM.  

### 6.7: Database Directory
----
* `/var/lib/rpm` is the default system directory which holds RPM database files in the form of **Berkeley DB** hash files; database files should not be manually modified; updates should be done only through use of the rpm program
* an alternative database directory can be specified with the **--dbpath** option to the rpm program; one might do this, for example, to examine an RPM database copied from another system
* you can use the **--rebuilddb** option to rebuild the database indices from the installed package headers; this is more of a repair, and not a rebuild from scratch

### 6.8: Helper Programs and Modifying Settings
----
* Helper programs and scripts used by RPM reside in /usr/lib/rpm; there are quite a few; for example on a RHEL 7 system:  
`$ ls /usr/lib/rpm | wc -l`
> 73
where wc is reporting the number of lines of output
* you can create an rpmrc file to specify default settings for rpm; by default, rpm looks for the following in order:
    * `/usr/lib/rpm/rpmrc`
    * `/etc/rpmrc`
    * `~/.rpmrc`
* note all these files are read; rpm does not stop as soon as it finds that one exists; an alternative rpmrc file can be specified using the **--rcfile** option

### 6.9: Queries
----
All rpm inquiries include the **-q** option, which can be combined with numerous sub-options, as in:  
* Which version of a package is installed?
    `$ rpm -q bash`
* Which package did this file come from?
    `$ rpm -qf /bin/bash` # **-f** for file
* What files were installed by this package?
    `$ rpm -ql bash`
* Show information about this package.
    `$ rpm -qi bash`
* Show information about this package from the package file, not the package database.
    `$ rpm -qip foo-1.0.0-1.noarch.rpm` (didn't work for me, and not when using packages on my system, either - "No such file or directory")
* List all installed packages on this system.
    `$ rpm -qa`
* Return a list of prerequisites for a package:
    `$ rpm -qp --requires foo-1.0.0-1.noarch.rpm`
* Show what installed package provides a particular requisite package:
    `$ rpm -q --whatprovides libc.so.6`

### 6.10: Verifying Packages
----
* **-V** option to **rpm** lets you verify whether files from package are consistent with database
* to verify all packages on system:
    * `$ rpm -Va`
* output generated only when there is a probem (my CentOS7 packages seem to have a lot of problems, then)
* codes (in order):

Character | Meaning
--------- | -------
S         | file size differs
M         | file permissions and/or type differs
5         | MD5 checksum differs
D         | device major/minor number mismatch
L         | symbolic link path mismatch
U         | user ownership differs
G         | group ownership differs
T         | modification time differs
P         | capabilities differ
.         | (at any of the above positions) test passed
?         | (at any of the above positions) test cannot be performed

* these do not necessarily indicate a problem; for example, many config files are modified as system evolves 
* if you specify one or more package names as arguments, you examine only that package:
    * no output when everything is okay: 
  `$ rpm -V bash`  
    * output indicating that a file's size, checksum, and modification time have changed:
  `$ rpm -V talk`  
  > S.5....T in.ntalkd.8  
    * output indicating that a file is missing:
  `$ rpm -V talk`  
  > missing /usr/bin/talk  

### 6.11: Installing Packages
----
Installing a package is as simple as:  
`$ sudo rpm -ivh foo-1.0.0-1.noarch.rpm`  where the **-i** is for install, **-v** is for verbose, and **-h** just means print out hash marks while doing to show progress.

RPM performs a number of tasks when installing a package:
    * performs dependency checks
    * performs conflict checks
    * executes commands required before installation
    * deals intelligently with configuration files: when installing a configuration file, if the file exists and has been changed since the previous version of the package was installed, RPM saves the old version with the **suffix .rpmsave**; allows you to integrate the changes in the old config file into the new version of the file
    * unpacks the files from packages and installs them with correct attributes
    * executes commands required after installation
    * updates the system RPM database; uses info in database when checking for conflicts

### 6.12: Uninstalling Packages
----
* a successful uninstall produces no output
* errors occur when the package isn't installed or is required by other packages
`$ sudo rpm -e system-config-lvm` where **-e** is for erase and 'system-config-lvm' is the package name, not rpm file name
`$ sudo rpm -e --test -vv system-config-lvm` where **--test** will tell you whether the install would succeed or fail w/o actually doing it and **-vv** gives more information
* do not uninstall the rpm package itself! (would require booting into rescue env. or reinstalling OS)

### 6.13: Using the 'rpm' Command Demo
----
`$ rpm -qa | grep bzip2` # **-q** for query, **-a** for all 
> bzip2-libs-1.0.6-13.el7.x86_64 # libraries  
> bzip2-1.0.6-13.el7.x86_64 # actual program  
`$ find / -name "bzip2*" -type d 2>&1 | grep -v 'Permission denied'`  
> /usr/share/doc/bzip2-libs-1.0.6  
> /usr/share/doc/perl-IO-Compress-2.061/io/bzip2  
> /usr/share/doc/bzip2-1.0.6  
`$ rpm -qil bzip2 | less` # **-i** for information, **l** for list  
`$ ls -lF $(rpm -ql bzip2)` # make it a shell command (pipe through less if too much output)  
? Why doesn't `$ rpm -ql bzip2 | ls -lF` work?  
`$ sudo rpm -e --test bzip2`  
> error: Failed dependencies:  
>  bzip2 is needed by (installed) libvirt-daemon-driver-qemu-3.9.0-14.el7_5.5.x86_64  
>  bzip2 is needed by (installed) sos-3.5-7.el7.centos.noarch  
>  /usr/bin/bzip2 is needed by (installed) file-roller-3.22.3-1.el7.x86_64  
`$ rpm -q --whatprovides bzip2`  
> bzip2-1.0.6-13.el7.x86_64  
`$ rpm -q --whatrequires bzip2`  
> libvirt-daemon-driver-qemu-3.9.0-14.el7_5.5.x86_64  
> sos-3.5-7.el7.centos.noarch  

### 6.14: Upgrading Packages
----
* upgrading replaces the original package if installed:
`$ sudo rpm -Uvh bash-4.2.45-5.el7_0.4.x86_64.rpm` # **-U** for Upgrade
* old packaged removed after newer version is installed except for config from original, which is saved with an **.rpmsave** extension
* **-U** allows installation without error if the package is not already installed
* **-i** not designed for upgrades
* however, different versions can be installed if each version does not contain the same files (typically kernel packages and library packages from alternative architectures)
* use **--oldpackage** with **rpm -U** to replace current version with an earlier one

### 6.15: Freshening Packages
----
`$ sudo rpm -Fvh *.rpm`# **-F** for Freshen
1. if an older version of a package is installed, it will be upgraded to the newer version in the directory
2. if the version on the system is the same as the one in the directory, nothing happens
3. if there is no version of a package installed, the package in the directory is ignored
* useful for applying a lot of patches (i.e., upgraded packages) at once

### 6.16: Upgrading the Kernel
----
When you install a new kernel on your system, it requires a reboot (one of the few updates that do) to take effect. You should not do an upgrade (-U) of a kernel: an upgrade would remove the old currently running kernel.  
This in and of itself won't stop the system, but if, after a reboot, you have any problems, you will no longer be able to reboot into the old kernel, since it has been removed from the system. However, if you install (**-i**), both kernels coexist and you can choose to boot into either one; i.e., you can revert back to the old one if need be.  
To install a new kernel do:  
`$ sudo rpm -ivh kernel-{version}.{arch}.rpm` filling in the correct version and architecture names.  
When you do this, the **GRUB** configuration file will automatically be updated to include the new version; it will be the default choice at boot, unless you reconfigure the system to do something else.  
Once the new kernel version has been tested, you may remove the old version if you wish, though this is not necessary. Unless you are short on space, it is recommended that you keep one or more older kernels available.  

### 6.17: Using rpm2cpio
----
Suppose you have a need to extract files from an rpm but do not want to actually install the package? The **rpm2cpio** program can be used to copy the files from an **rpm** to a **cpio** archive, and also extract the files if so desired.  
* Create the cpio archive with:
`$ rpm2cpio foobar.rpm > foobar.cpio`
* To list files in an rpm:
`$ rpm2cpio foobar.rpm | cpio -t`
* but a better way is to do:
`$  rpm -qilp foobar.rpm`
* To extract onto the system:
`$ rpm2cpio bash-4.2.45-5.el7_0.4.x86_64.rpm |  cpio -ivd bin/bash`  
`$ rpm2cpio foobar.rpm | cpio --extract --make-directories`

### Lab 6.1: Using RPM
----

1. Find out what package the file /etc/logrotate.conf belongs to.
    * My solution: 
    * `$ rpm -qf /etc/logrotate.conf`
> logrotate-3.8.6-15.el7.x86_64

2. List information about the package including all the files it contains.
    * My solution:
    * `$ rpm -qil logrotate-3.8.6-15.el7.x86_64`
> Name        : logrotate
> V> ersion     : 3.8.6
> Release     : 15.el7
> Architecture: x86_64
> Install Date: Thu 10 May 2018 09:57:07 AM PDT
> Group       : System Environment/Base
> Size        : 106988
> License     : GPL+
> Signature   : RSA/SHA256, Wed 25 Apr 2018 04:25:49 AM PDT, Key ID 24c6a8a7f4a80eb5
> Source RPM  : logrotate-3.8.6-15.el7.src.rpm
> Build Date  : Tue 10 Apr 2018 05:51:42 PM PDT
> Build Host  : x86-01.bsys.centos.org
> Relocations : (not relocatable)
> Packager    : CentOS BuildSystem <http://bugs.centos.org>
> Vendor      : CentOS
> URL         : https://github.com/logrotate/logrotate
> Summary     : Rotates, compresses, removes and mails system log files
> Description :
> The logrotate utility is designed to simplify the administration of
> log files on a system which generates a lot of log files.  Logrotate
> allows for the automatic rotation compression, removal and mailing of
> log files.  Logrotate can be set to handle a log file daily, weekly,
> monthly or when the log file gets to a certain size.  Normally,
> logrotate runs as a daily cron job.
> 
> Install the logrotate package if you need a utility to deal with the
> log files on your system.
> /etc/cron.daily/logrotate
> /etc/logrotate.conf
> /etc/logrotate.d
> /etc/rwtab.d/logrotate
> /usr/sbin/logrotate
> /usr/share/doc/logrotate-3.8.6
> /usr/share/doc/logrotate-3.8.6/CHANGES
> /usr/share/doc/logrotate-3.8.6/COPYING
> /usr/share/man/man5/logrotate.conf.5.gz
> /usr/share/man/man8/logrotate.8.gz
> /var/lib/logrotate
> /var/lib/logrotate/logrotate.status
    * Fancier solution from the book which combines 1 and 2:
    * `$ rpm -qil $(rpm -qf /etc/logrotate.conf)`

3. Verify the package installation.
    * My solution: 
    * $ rpm -V logrotate-3.8.6-15.el7.x86_64
> ..?......  c /etc/cron.daily/logrotate
    * means MD5 checksum test cannot be performed

4. Try to remove the package.
    * My solution: 
    * `$ rpm -e --test logrotate-3.8.6-15.el7.x86_64`
> error: Failed dependencies:
>   logrotate is needed by (installed) vsftpd-3.0.2-22.el7.x86_64
>   logrotate >= 3.5.2 is needed by (installed) rsyslog-8.24.0-16.el7_5.4.x86_64


### Lab 6.2: Rebuilding the RPM Database
----
There are conditions (which?) under which the RPM database stored in /var/lib/rpm can be corrupted. In this exercise we will construct a new one and verify its integrity.

1. Backup the contents of /var/lib/rpm as the rebuild process will overwrite the contents. If you neglect to do this and something goes wrong you are in serious trouble.
    * My solution: 
    * `sudo /bin/rm -f /var/lib/rpm/__db*`
    * `sudo tar czvf $(hostname).rpmdatabase.tar.gz /var/lib/rpm`
    * `sudo mv centos.rpmdatabase.tar.gz /var/lib/`
    * Correct solution:
    * `$ sudo cp -a rpm rpm_BACKUP` # then the questions 3 and 5 can use **ls** in a straightforward way

2. Rebuild the database.
    * My solution:
    * `$ sudo rpm --rebuilddb`

3. Compare the new contents of the directory with the backed up contents; don’t examine the actual file contents as they are binary data, but note the number and names of the files.
    * My solution:
    * `$ tar -xzvf centos.rpmdatabase.tar.gz` # that unpacked the files into the original directory instead of my current directory
    * `$ tar -xzvf centos.rpmdatabase.tar.gz -C ~/rpm`
    * `$ ls -al | wc`
>   18     155    1054
    * `$ ls -al /var/lib/rpm | wc`
>     18     155     986

4. Get a listing of all rpms on the system. You may want to compare this list with one generated before you actually do the rebuild procedure. If the query command worked, your new database files should be fine.
    * My solution:
    * `$ rpm -qa | wc -l`
> 1627
    * Book solution:
    * `$ rpm -qa | tee /tmp/rpm-qa.output`

5. Compare again the two directory contents. Do they have the same files now?
    * I didn't get a count before the rebuild

6. You could delete the backup (probably about 100MB in size) but you may want to keep it around for a while to make sure your system is behaving properly before trashing it.  You may want to look at http://www.rpm.org/wiki/Docs/RpmRecovery for a more complete examination of steps you can take to verify and/or recover the database integrity.
    * That link gives a 404 currently

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
packages | `/var/lib/rpm` | default system directory which holds RPM database files in the form of **Berkeley DB** hash files | LFS201 6.7

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
packages | `$ ls /usr/lib/rpm | wc -l` | shows programs and scripts used by RPM | LFS201 6.8
packages | `/usr/lib/rpm` | holds programs and scripts used by RPM | LFS201 6.8
packages | 1 `/usr/lib/rpm/rpmrc` | first place RPM looks for settings; each is read | LFS201 6.8
packages | 2 `/etc/rpmrc` | second place RPM looks for settings; each is read | LFS201 6.8
packages | 3 `~/.rpmrc` | third place RPM looks for settings; each is read | LFS201 6.8
packages | `$ rpm -q ...` | make an RPM inquiry | LFS201 6.9
packages | `$ rpm -q bash` | shows which version of a package is installed | LFS201 6.9
packages | `$ rpm -qf /bin/bash` | shows which package a file came from; **-f** for file | LFS201 6.9
packages | `$ rpm -ql bash` | shows what files were installed by this package | LFS201 6.9
packages | `$ rpm -qi bash` | shows information about this package | LFS201 6.9
packages | `$ rpm -qip foo-1.0.0-1.noarch.rpm` | shows information about this package from the package file, not the package database | LFS201 6.9
packages | `$ rpm -qa` | lists all installed packages on this system | LFS201 6.9
packages | `$ rpm -qp --requires foo-1.0.0-1.noarch.rpm` | return a list of prerequisites for a package | LFS201 6.9
packages | `$ rpm -q --whatprovides libc.so.6` | shows what installed package provides a particular requisite package | LFS201 6.9
packages | `$ rpm -Va` | verify all packages on system; output only when there is a problem | LFS201 6.10
packages | `$ rpm -V bash` | verify **bash** package | LFS201 6.10
packages | `$ rpm -V talk` | verify **talk** package | LFS201 6.10
packages | `$ sudo rpm -ivh foo-1.0.0-1.noarch.rpm` | install the foo-1.0.0-1.noarch.rpm package | LFS201 6.11 
packages | `$ sudo rpm -e system-config-lvm` | erase the **system-config-lvm** package | LFS201 6.12
packages | `$ sudo rpm -e --test -vv system-config-lvm` | test whether erasing the **system-config-lvm** package will succeed or fail | LFS201 6.12
packages | `$ rpm -qa | grep bzip2` | find **bzip2** packages | LFS201 6.13
packages | `$ find / -name "bzip2*" -type d 2>&1 | grep -v 'Permission denied'` | find **bzip2** packages | LFS201 6.13
packages | `$ rpm -qil bzip2 | less` | find **bzip2** packages | LFS201 6.13
packages | `$ ls -lF $(rpm -ql bzip2)` | make it a shell command (pipe through less if too much output) | LFS201 6.13 
packages | `$ sudo rpm -e --test bzip2` | test whether erasing **bzip2** will work | LFS201 6.13
packages | `$ rpm -q --whatprovides bzip2` | check packages provide **bzip2** | LFS201 6.13
packages | `$ rpm -q --whatrequires bzip2` | show prerequisites for **bzip2** | LFS201 6.13
packages | `$ sudo rpm -Uvh bash-4.2.45-5.el7_0.4.x86_64.rpm` | upgrade package | LFS201 6.14
packages | `$ sudo rpm -Fvh *.rpm` | freshen package; useful for applying a lot of patches at once | LFS201 6.15
packages | `$ sudo rpm -ivh kernel-{version}.{arch}.rpm` | install a new kernel | LFS201 6.16
packages | `$ rpm2cpio foobar.rpm > foobar.cpio` | creates the cpio archive | LFS201 6.17
packages | `$ rpm2cpio foobar.rpm | cpio -t` | lists files in an RPM | LFS201 6.17
packages | `$  rpm -qilp foobar.rpm` | better way to list files in an RPM | LFS201 6.17
packages | 1 `$ rpm2cpio bash-4.2.45-5.el7_0.4.x86_64.rpm |  cpio -ivd bin/bash` | extract onto the system  | LFS201 6.17
packages | 2 `$ rpm2cpio foobar.rpm | cpio --extract --make-directories` | extract onto the system | LFS201 6.17
packages | `$ rpm -qf /etc/logrotate.conf` | find out what package the file `/etc/logrotate.conf` belongs to | LFS201 Lab 6.1
packages | `$ rpm -qil logrotate-3.8.6-15.el7.x86_64` | lists information about the package including all the files it contains | LFS201 Lab 6.1
packages | `$ rpm -qil $(rpm -qf /etc/logrotate.conf)` | find out the owner of `/etc/logrotate.conf` *and* list information about the package | LFS201 Lab 6.1
packages | `$ rpm -V logrotate-3.8.6-15.el7.x86_64` | verify the package installation | LFS201 Lab 6.1
packages | `$ sudo cp -a rpm rpm_BACKUP` | make a backup copy of the RPM database | LFS201 Lab 6.2
packages | `$ sudo rpm --rebuilddb` | rebuild the RPM database | LFS201 Lab 6.2
packages | `$ tar -xzvf centos.rpmdatabase.tar.gz -C ~/rpm` | unpack files into the `~/rpm` directory (**-C** for 'change') | LFS201 Lab 6.2
packages | `$ rpm -qa | tee /tmp/rpm-qa.output` | get a listing of all RPMs on the system | LFS201 Lab 6.2
  