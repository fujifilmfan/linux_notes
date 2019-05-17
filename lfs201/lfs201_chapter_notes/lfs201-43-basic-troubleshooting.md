Chapter 43: Basic Troubleshooting
---------------------------------

[43.3: Learning Objectives](#433-learning-objectives)  
[43.4: Troubleshooting Levels](#434-troubleshooting-levels)  
[43.5: Basic Techniques](#435-basic-techniques)  
[43.6: Intuition and Experience](#436-intuition-and-experience)  
[43.7: Things to Check: Networking](#437-things-to-check-networking)  
[43.8: Things to Check: File Integrity](#438-things-to-check-file-integrity)  
[43.9: Boot Process Failures](#439-boot-process-failures)  
[43.10: Filesystem Corruption and Recovery](#4310-filesystem-corruption-and-recovery)  
[Paths and Commands](#paths-and-commands)  
  
### 43.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Troubleshoot your system, following a number of steps iteratively until solutions are found.
* Check your network and file integrity for possible issues.
* Resolve problems when there is system boot failure.
* Repair and recover corrupted filesystems.
* Understand how **rescue and recovery** media can be used for troubleshooting.
  
### 43.4: Troubleshooting Levels
----
* it takes a few years of practice to get good
  
### 43.5: Basic Techniques
----
basic strategy:
1. characterize the problem
1. reproduce the problem
1. always try the easy things first
1. eliminate possible causes one at a time
1. change only one thing at a time; if that doesn't fix the problem, change it back
1. check the system logs (`/var/log/messages`, `/var/log/secure`, etc.) for further information
  
### 43.6: Intuition and Experience
----
* use well-known procedures, not your gut, to ensure you'll eventually be able to solve the problem and not have to rely on a wizard
* if you do use your intuition, determine quickly whether it's a good path
* useful intuition is not magic, it is distilled experience
  
### 43.7: Things to Check: Networking
----
* **IP configuration**: use **ifconfig** or **ip** to see if the interface is up, and if so, if it is configured
* **Network Driver**: if the interface can't be brought up, maybe the correct device driver for the network card(s) is not loaded. Check with **lsmod** if the network driver is loaded as a kernel module, or by examining relevant pseudo-files in `/proc` and `/sys`, such as `/proc/interrupts` or `/sys/class/net`
* **Connectivity**:
    * use **ping** to see if the network is visible, checking for response time and packet loss
    * **traceroute** can follow packets through the network
    * **mtr** can do this in a continuous fashion
    * use of these utilities can tell you if the problem is local or on the internet
* **Default gateway and routing configuration**: run **route -n** and see if the routing table makes sense
* **Hostname resolution**: run **dig** or **host** on a URL and see if DNS is working properly
* if the network is up and running but performance is terrible, it really falls under the banner of performance tuning, not troubleshooting
  
### 43.8: Things to Check: File Integrity
----
Packaging systems have methods of verifying file integrity and checking for changes:
* `$ rpm -V some_package` verify file integrity for a single package
* `$ rpm -Va` check all packages on the system
* `$ debsums options some_package` check integrity of the filed in some_package; however, not all packages maintain checksums so this might not be completely useful
* **aide** is used for  **intrusion detection** and is another way to check for changes in files
* `$ sudo aide --check` run a scan on your files and compare them to the last scan
  
### 43.9: Boot Process Failures
----
* if the system fails to boot properly or fully, being familiar with what happens at each stage is important in identifying particular sources of problems
* assuming you get through the BIOS stage, you may reach one of these unfortunate states:
    * **No boot loader screen**
        * check for GRUB misconfiguration, or a corrupt boot sector
        * you may have to re-install the boot loader
    * **Kernel fails to load**
        * if the kernel **panics** during the boot process, it is most likely a misconfigured or corrupt kernel, or incorrect parameters specified on the kernel command line in the GRUB configuration file
        * if the kernel has booted successfully in the past, it has either been corrupted, or the kernel command line in the GRUB configuration file has been altered in an unproductive way
        * depending on which, you can reinstall the kernel, or enter into the interactive GRUB menu at boot and use very minimal command line parameters and try to fix that way
        * or, you can try booting into a rescue image
    * **Kernel loads, but fails to mount the root filesystem**
        * the main causes are:
        1. misconfigured GRUB configuration file
        2. misconfigured `/etc/fstab`
        3. no support for the root filesystem type either built into the kernel or as a module in the **initramfs** initial ram disk or filesystem
    * **Failure during the `init` process**
        * there are many things that can go wrong once **init** starts look closely at the messages that are displayed before things stop
        * if things were working before, with a different kernel, this is a big clue
        * look out for corrupted filesystems, errors in startup scripts, etc.
        * try booting into a lower runlevel, such as 3 (no graphics) or 1 (single user mode) 
  
### 43.10: Filesystem Corruption and Recovery
----
* if during the boot process one or more filesystems fail to mount, **fsck** may be used to attempt repair
* however, before doing that one should check that `/etc/fstab` has not been misconfigured or corrupted
* note once again that you could have a problem with a filesystem type the kernel you are running does not understand
* if the root filesystem has been mounted you can examine this file, but `/` may have been mounted as read-only, so to edit the file and fix it you can run:
    * `$ sudo mount -o remount,rw /` remount root filesystem with write permission
* if `/etc/fstab` seems to be correct, you can move to **fsck**; first, you should try:
    * `$ sudo mount -a` try and mount all filesystem
* if this does not succeed completely, you can try to manually mount the ones with problems
* you should first run **fsck** to just examine; afterwards, you can run it again to have it try and fix any errors found
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
troubleshooting, system | `/var/log/messages` | useful for troubleshooting | LFS 201 43.5
troubleshooting, system | `/var/log/secure` | useful for troubleshooting | LFS 201 43.5
troubleshooting, system | `/proc/interrupts` | useful for troubleshooting network drivers | LFS 201 43.7
troubleshooting, system | `/sys/class/net` | useful for troubleshooting network drivers | LFS 201 43.7
troubleshooting, system | `/etc/fstab` | location of filesystem table | LFS 201 43.10

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
troubleshooting, system | `$ rpm -V some_package` | verify file integrity for a single package | LFS 201 43.8
troubleshooting, system | `$ rpm -Va` | check all packages on the system | LFS 201 43.8
troubleshooting, system | `$ debsums options some_package` | check integrity of the filed in some_package | LFS 201 43.8
troubleshooting, system | `$ aide ...` | used for  **intrusion detection** and is another way to check for changes in files | LFS 201 43.8
troubleshooting, system | `$ sudo aide --check` | run a scan on your files and compare them to the last scan | LFS 201 43.8
troubleshooting, system | `$ sudo mount -o remount,rw /` | remount root filesystem with write permission | LFS 201 43.10
troubleshooting, system | `$ sudo mount -a` | try and mount all filesystem | LFS 201 43.10
  