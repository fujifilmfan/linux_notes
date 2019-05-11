Chapter 39: init: SystemV, Upstart, systemd
-------------------------------------------

[39.1: init: SystemV, Upstart, systemd](#391-init-systemv,-upstart,-systemd)  
[39.2: Chapter 39 Introduction](#392-chapter-39-introduction)  
[39.3: Learning Objectives](#393-learning-objectives)  
[39.4: The init Process](#394-the-init-process)  
[39.5: Startup Alternatives](#395-startup-alternatives)  
[39.6: systemd](#396-systemd)  
[39.7: systemd Configuration Files](#397-systemd-configuration-files)  
[39.8: systemctl](#398a-systemctl)  
[39.9: systemctl Demo](#399-systemctl-demo)  
[39.10: A Word About SysVinit](#3910-a-word-about-sysvinit)  
[39.11: SysVinit Runlevels](#3911-sysvinit-runlevels)  
[39.12: SysVinit and /etc/inittab](#3912-sysvinit-and-etcinittab)  
[39.13: SysVinit Startup Scripts](#3913-sysvinit-startup-scripts)  
[39.14: chkconfig](#3914-chkconfig)  
[39.15: service](#3915-service)  
[39.16: chkconfig and service on Debian-based Systems](#3916-chkconfig-and-service-on-debian-based-systems)  
[39.17: Upstart](#3917-upstart)  
[39.18: Upstart Utilities](#3918-upstart-utilities)  
[39.19.a: Knowledge Check 39.1](#3919a-knowledge-check-391)  
[39.19.b: Knowledge Check 39.2](#3919b-knowledge-check-392)  
[39.19.c: Knowledge Check 39.3](#3919c-knowledge-check-393)  
[39.19.d: Knowledge Check 39.4](#3919d-knowledge-check-394)  
[39.19.e: Knowledge Check 39.5](#3919e-knowledge-check-395)  
[39.19.f: Knowledge Check 39.6](#3919f-knowledge-check-396)  
[Lab 39.1: Adding a New Startup Service with SysVinit](#lab-391-adding-a-new-startup-service-with-sysvinit)  
[Lab 39.2: Adding a New Startup Service with systemd](#lab-392-adding-a-new-startup-service-with-systemd)  
[Paths and Commands](#paths-and-commands)  
  
### 39.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Understand the importance of the **init** process.
* Understand how **systemd** (and **Upstart**) arose and how they work.
* Use **systemctl** to configure and control **systemd**.
* Explain how the traditional **SysVinit** method works and how it incorporates **runlevels** and what happens in each one.
* Use **chkconfig** and **service** (and alternative utilities) to start and stop services or make them persistent across reboots when using S**ysVinit**.
  
### 39.4: The init Process
----
* `/sbin/init` (**init**) is the first user-level process (or task) run on the system and continues to run until the system is shutdown
    * traditionally, it has been considered the **parent** of all user processes, although technically that is not true, as some processes are started directly by the kernel
    * init coordinates the later stages of the boot process, configures all aspects of the environment, and starts the processes needed for logging into the system
    * init also works closely with the kernel in cleaning up after processes when they terminate.
* SysVinit was not concerned with shutdown/reboot speed, as those were considered rare events, and it viewed startup as a serial process, not taking advantage of parellel processing
  
### 39.5: Startup Alternatives
----
* alternatives methods of controlling system startup:
    * **Upstart** (Ubuntu, which now uses **systemd**)
    * **systemd** adopted by all major distributions now
* "The history of all this and the controversies are quite complicated, and colorful personalities have ensured not all the discussion is of a technical nature. In our context, we will not look through this lens."
* SysVinit utilities and methods are still used even though things are different "under the hood"
  
### 39.6: systemd
----
* features of **systemd** include:
    * is compatible with SysVinit scripts
    * boots faster than previous systems
    * provides aggressive parallelization capabilities
    * uses socket and D-Bus activation for starting services
    * replaces shell scripts with programs
    * offers on-demand starting of daemons
    * keeps track of processes using cgroups
    * supports creating snapshots and restoring of the system state
    * maintains mount and automount points
    * implements an elaborate transactional dependency-based service control* logic
    * can work as a drop-in replacement for SysVinit
* systemd uses `.service` files instead of Bash scripts
* systemd sorts all daemons into their own Linux cgroups (control groups)
* systemd is backward compatible with SysVinit, and the concept of runlevels is supported via runlevel **targets**
    * the **telinit** program is emulated to work with runlevels
  
### 39.7: systemd Configuration Files 
----
* systemd prefers to use new standardized configuration files, like `/etc/hostname`, which would replace:
    * `/etc/sysconfig/network` in Red Hat
    * `/etc/HOSTNAME` in SUSE
    * `/etc/hostname`
* other files might include:
    * `/etc/vconsole.conf`: default keyboard mapping and console font
    * `/etc/sysctl.d/*.conf`: drop-in directory for kernel sysctl parameters
    * `/etc/os-release`: distribution ID file
  
### 39.8: systemctl
----
* **systemctl** is the main utility for managing services
* example usage:
    * `$ systemctl [options] command [name]` general syntax
    * `$ systemctl` show the status of everything that systemd controls
    * `$ systemctl list-units -t service --all` show all available services
    * `$ systemctl list-units -t service` show only active services
    * `$ sudo systemctl start foo` start (activate) one or more units, where a unit can be a service or a socket
    * `$ sudo systemctl start foo.service` start (activate) one or more units, where a unit can be a service or a socket
    * `$ sudo systemctl start /path/to/foo.service` start (activate) one or more units, where a unit can be a service or a socket
    * `$ sudo systemctl stop foo.service` stop (deactivate) a service
    * `$ sudo systemctl enable sshd.service` enable a service (equivalent of **chkconfig on/off** and doesn't actually start the service)
    * `$ sudo systemctl disable sshd.service` disable a service
* [SysVinit to Systemd Cheatsheet](https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet)
  
### 39.9: systemctl Demo
----
  
### 39.10: A Word About SysVinit
----
* still worth covering due to the existence of compatibility layers and some holdouts, like VMWare
  
### 39.11: SysVinit Runlevels
----
* as a SysVinit system starts, it passes through a sequence of **runlevels** which define different system states; they are numbered from 0 to 6
* these are defined differently on different distributions

**System Runlevels**
Runlevel | Meaning
-------- | -------
S, s     | Same as 1
0        | Shutdown system and turn power off
1        | Single User Mode
2        | Multiple user, no NFS, only text login
3        | Multiple user, with NFS and network, only text login
4        | Not used
5        | Multiple user, with NFS and network, graphical login with X
6        | Reboot

* `$ runlevel` display current runlevel, where first character is the previous level (N means unknown)
    > N 5
* `$ sudo /sbin/telinit 5` change from runlevel 3 to runlevel 5
  
### 39.12: SysVinit and /etc/inittab
----
* when the init process is started, the first thing it does is to read `/etc/inittab`, which told init which scripts to run to bring the system up each runlevel, and was done with a series of lines, one for each runlevel:
    > id:runlevel(s):action:process
    where:
    * id: a unique 1-4 character identification for the entry
    * runlevel(s): zero or more single characters or digit identifiers indicating which runlevel the action will be taken for
    * action: describes the action to be taken
    * process: specifies the process to be executed
* however, in more recent systems, the only un-commented line and the only thing being set in this file is the default runlevel with the line
    > id:5:initdefault
    * my machine has no un-commented lines; the first line is: 
    > \# inittab is no longer used when using systemd
    * "some recent systemd-based distributions do not use this file at all; all lines in it are comments, but it is kept around to avoid breaking old scripts"
  
### 39.13: SysVinit Startup Scripts
----

### 39.13.b: SysVinit Startup Scripts II
----

### 39.14.a: chkconfig I
----

### 39.14.b: chkconfig II
----

### 39.15.a: service I
----

### 39.15.b: service II
----

### 39.16: chkconfig and service on Debian-based Systems
----

### 39.17.a: Upstart I
----

### 39.17.b: Upstart II
----

### 39.18: Upstart Utilities
----

### 39.19.a: Knowledge Check 39.1
----

### 39.19.b: Knowledge Check 39.2
----

### 39.19.c: Knowledge Check 39.3
----

### 39.19.d: Knowledge Check 39.4
----

### 39.19.e: Knowledge Check 39.5
----

### 39.19.f: Knowledge Check 39.6
----

### Lab 39.1: Adding a New Startup Service with SysVinit
----

### Lab 39.2: Adding a New Startup Service with systemd
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
`/sbin/init` | LFS201 39.4

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------

