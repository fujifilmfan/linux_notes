Chapter 19: Filesystem Features II: Swap, Quotas, Usage
-------------------------------------------------------

[19.3: Learning Objectives](#193-learning-objectives)  
[19.4: Using swap](#194-using-swap)  
[19.5: Filesystem Quotas](#195-filesystem-quotas)  
[19.6: Setting up Quotas](#196-setting-up-quotas)  
[19.7: quotacheck](#197-quotacheck)  
[19.8: Turning Quotas On and Off](#198-turning-quotas-on-and-off)  
[19.9: Examining Quotas](#199-examining-quotas)  
[19.10: Setting Quotas](#1910-setting-quotas)  
[19.11: df: Filesystem Usage](#1911-df-filesystem-fsage)  
[19.12: du: Disk Usage](#1912-du-disk-usage)  
[Lab 19.1: Managing Swap Space](#lab-191-managing-swap-space)  
[Lab 19.2: Filesystem Quotas](#lab-192-filesystem-quotas)  
[19.13 Knowledge Check 19.3](#1913-knowledge-check-193)  
[Paths and Commands](#paths-and-commands)  
  
### 19.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the concepts of swap and quotas.
* Use the utilities that help manage quotas: **quotacheck**, **quotaon**, **quotaoff**, **edquota**, and **quota**.
* Use the utilities **df** and **du**.
  
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
-a, --all               | turn quotas off for all filesystems
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
  
### Lab 19.1: Managing Swap Space
----
  
### Lab 19.2: Filesystem Quotas
----
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem, memory | `/proc/swaps` | swap areas | LFS201 19.4
filesystem | `aquoata.user` and `aquota.group` | quota database files | LFS201 19.5

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem, memory | `$ cat /proc/swaps` | check on swap areas | LFS201 19.4
filesystem, memory | `$ free -m` | get basic memory statistics | LFS201 19.4
filesystem | `$ mkswap ...` | format a swap partition or file | LFS201 19.4
filesystem | `$ swapon ...` | activate a swap partition or file | LFS201 19.4
filesystem | `$ swapoff ...` | deactivate a swap partition or file | LFS201 19.4
filesystem | `$ quotacheck` | generates and updates quota accounting files | LFS201 19.5
filesystem | `$ quotaon` | enables quota accounting | LFS201 19.5
filesystem | `$ quotaoff` | disables quota accounting | LFS201 19.5
filesystem | `$ edquota` | used for editing user or group quotas | LFS201 19.5
filesystem | `$ quota` | reports on usage and limits | LFS201 19.5
filesystem | `$ xfs_quota` | ?? | LFS201 19.5
filesystem | `$ sudo mount -o remount /home` | step 1 in testing `/etc/fstab` entry | LFS201 19.6
filesystem | `$ sudo quotacheck -vu /home` | step 2 in testing `/etc/fstab` entry | LFS201 19.6
filesystem | `$ sudo quotaon -vu /home` | step 3 in testing `/etc/fstab` entry | LFS201 19.6
filesystem | `$ sudo edquota someusername` | step 4 in testing `/etc/fstab` entry | LFS201 19.6
filesystem | `$ sudo quotacheck -ua` | update user files for all filesystems in `/etc/fstab` with user quota options | LFS201 19.7
filesystem | `$ sudo quotacheck -ga` | update group files for all filesystems in `/etc/fstab` with group quota options | LFS201 19.7
filesystem | `$ sudo quotacheck -u [somefilesystem]` | update the user file for a particular filesystem | LFS201 19.7
filesystem | `$ sudo quotacheck -g [somefilesystem]` | update the group file for a particular filesystem | LFS201 19.7
filesystem | `$ sudo quotaon [flags] [filesystem]` | generic **quotaon** syntax | LFS201 19.7
filesystem | `$ sudo quotaoff [flags] [filesystem]` | generic **quotaoff** syntax | LFS201 19.8
filesystem | `$ sudo quotaon -av` | turn on all verbose | LFS201 19.8
filesystem | `$ sudo quotaoff -av` | turn off all verbose | LFS201 19.8
filesystem | `$ sudo quotaon -avu` | turn on for all users verbose | LFS201 19.8
filesystem | `$ sudo quotaoff -avu` | turn off for all users verbose | LFS201 19.8
filesystem | `$ sudo quotaon -avg` | turn on for all groups verbose | LFS201 19.8
filesystem | `$ sudo quotaoff -avg` | turn off for all groups verbose | LFS201 19.8
filesystem | `$ quota` or filesystem `$ quota -u` | show your current user quota | LFS201 19.9
filesystem | `$ quota -g` | shows your current group quota | LFS201 19.9
filesystem | `$ sudo quota <username>` | see quota for any particular user or group | LFS201 19.9
filesystem | `$ edquota -u [username]` | edit limits for username | LFS201 19.10
filesystem | `$ edquota -g [groupname]` | edit limits for groupname | LFS201 19.10
filesystem | `$ edquota -u -p [userproto] [username]` | copy userproto's user quota values to username | LFS201 19.10
filesystem | `$ edquota -g -p [groupproto] [groupname]` | copy groupproto's group quota values to groupname | LFS201 19.10
filesystem | `$ edquota -t` | set grace periods | LFS201 19.10
filesystem | `$ df -h` | view filesystem usage in human-readable format | LFS201 19.11
filesystem | `$ df -hT` | add filesystem type to output | LFS201 19.11
filesystem | `$ df -hi` | add inode to output; no "Size" | LFS201 19.11
filesystem | `$ df -hiT` | add type and indoe; no "Size" | LFS201 19.11
filesystem | `$ du` | display disk usage for current directory | LFS201 19.12
filesystem | `$ du -a` | list all files, not just directories | LFS201 19.12
filesystem | `$ du -h somedir` | display disk usage for a specific directory | LFS201 19.12
