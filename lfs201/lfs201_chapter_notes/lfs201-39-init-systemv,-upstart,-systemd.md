Chapter 39: init: SystemV, Upstart, systemd
-------------------------------------------

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
* first, `/etc/rc.d/rc.sysinit` is run (it often has a symbolic link to `/etc`)
* second, `/etc/rc.d/rc` is run with the desired runlevel as an argument, causing the system to go to `rc.d/rc[0-6].d` and run the scripts there, as in:
    * `$ ls -lF /etc/rc.d/rc5.d`
* `/etc/rc.d/rc.local` script may be used to start system-specific applications
    * all the actual scripts are in `/etc/init.d`; each runlevel directory just links back to them
    * start scripts start with S in the name
    * kill scripts start with K in the name
    * the number following the K or S in each script's name determines the order in which the scripts are invoked
    * the script name is the name of the service
    * example:
        ```
        [student@centos /etc/rc.d/rc6.d]$ ls -l
        total 0
        lrwxrwxrwx. 1 root root 20 May 10  2018 K50netconsole -> ../init.d/netconsole
        lrwxrwxrwx. 1 root root 17 May 10  2018 S10network -> ../init.d/network
        ```
* the existence or non-existence of a script's symbolic link in a runlevel directory determines whether or not the script is executed at that runlevel
    * **chkconfig** is used to manage these symbolic links
  
### 39.14: chkconfig
----
* **chkconfig** is used to query and configure what runlevels the various system services are to run in
* example use:
    * `$ chkconfig some_service` check a particular service to see if it is set up to run in the current runlevel; true if running, false otherwise
    * `$ chkconfig --list [service names]` see what services are configured to run in each of the runlevels
    * `$ sudo chkconfig some_service on` turn on a certain service next time the system boots (does not affect the current state)
    * `$ chkconfig some_service off` do not turn a certain service on next time the system boots (does not affect the current state)
    * `$ sudo service some_service [stop | start]` start or stop a service
* add your own services and write your own startup scripts by placing a script in `/etc/init.d` which has to have certain features in it and then use `chkconfig --add` to enable or `chkconfig --del` to disable use of the on and off instructions
* scripts contain lines like: **`# chkconfig: 2345 10 90`**
    * first argument defines runlevels on which to have service on by default
    * second argument is the numerical prefix in the start script, S10
    * third argument is the numerical prefix in the stop script, K90
  
### 39.15: service
----
* on a Linux system using or emulating SysVinit, the services are those in the /etc/init.d directory
    * `$ sudo service network status` see the current status of a particular service
        ```
        Configured devices:
        lo ens33
        Currently active devices:
        lo ens33 virbr0
        ```
    * `$ sudo service network` see available options for **service**, which vary by the particular service
        ```
        Usage: /etc/init.d/network {start|stop|restart|reload|status}
        ```
    * `$ sudo service --status-all` see status of all services on the system
* all **service** really does is change directory to `/etc/init.d` and run the appropriate script in that directory with the supplied options
* starting and stopping services with **service** is only effective during the current operation of the system
  
### 39.16: chkconfig and service on Debian-based Systems
----
* on Debian-based systems, including Ubuntu, the utilities will work only if you have installed the **sysvinit-utils** and **chkconfig** packages, as in:
    * `$ sudo apt install sysvinit-utils chkconfig` install sysvinit-utils and chkconfig on Debian-based system and older Ubuntu systems
    * `$ sudo invoke-rc.d cups [ status | start | stop ]` equivalent of **service** on Ubuntu
    * `$ sudo status cups` check or change the cups situation; **status** preferred over **invoke-rc.d**
    * `$ sudo update-rc.d cups [ defaults | purge ]` equivalent of **chkconfig**
    * `$ sudo sysv-rc-conf cups [ on | off ]` equivalent of **chkconfig**
  
### 39.17: Upstart
----
* Upstart is **event-driven**, rather than being a set of serial procedures 
    * event notifications are sent to the init process to tell it to execute certain commands at the right time after pre-requisites have been fulfilled
* Upstart configuration files include:
    * `/etc/init/rcS.conf`
    * `/etc/rc.sysinit`
    * `/etc/inittab`
    * `/etc/init/rc.conf`
    * `/etc/rc[0-5].d`
    * `/etc/init/start-ttys.conf`
* when the kernel starts the init process, this causes the **rcS.conf** script to be executed
* this, in turn, causes **rc-sysinit.conf** to be run
  
### 39.18: Upstart Utilities
----
* using **initctl** you can view, start, stop jobs in much the same way that **service** does
    * `$ initctl options command` general syntax
  
### Lab 39.1: Adding a New Startup Service with SysVinit
----
In this and the following exercise, we will create a simple startup service. First we will do it for a **SysVinit** system. Note that
if you are using a **systemd**-based system everything should still work because of the backwards compatibility layer that all distributions utilize. However, in the next exercise we will do natively for **systemd**.  
* Move fake_service script to `/etc/init.d/fake_service` and make it executable: `$ sudo chmod 755 /etc/init.d/fake_service`
* You’ll notice the script includes the file `/etc/sysconfig/fake service`. Give it the following contents:
    ```
    VAR1= "true"
    VAR2= "true"
    ```
* Test to see if the script works properly by running the following commands (these commands do not actually cause the service to run, I think):
    * `$ sudo service fake_service`
        > Usage: fake_service {start | stop}
    * `$ sudo service fake_service start`
        > Running fake_service in start mode...
    * `$ sudo service fake_service stop`
        > Running fake_service in start mode...
* Look at the file named `/var/log/fake_service.log`. What does it contain?
    ```
    /etc/init.d/fake_service start at Tue Jun  4 20:00:06 PDT 2019
    /etc/init.d/fake_service stop at Tue Jun  4 20:00:19 PDT 2019
    /etc/init.d/fake_service start at Tue Jun  4 20:02:36 PDT 2019
    ```
* Next we will want to have the ability to start fake service whenever the system starts, and stop it when it shuts down. If you do the following, you will get an error as it hasn’t been set up yet for this:
    * `$ sudo chkconfig --list fake_service`
        ```
        ...
        service fake_service supports chkconfig, but is not referenced in any runlevel (run 'chkconfig --add fake_service')
        ```
* You can easily do this with:
    * `$ sudo chkconfig --add fake_service`
* ...and you can turn it on or off at boot time with
    * `$ sudo chkconfig fake_service on`
    * `$ sudo chkconfig --list fake_service`
        ```
        fake_service   	0:off	1:off	2:on	3:on	4:on	5:on	6:off
        ```
    * `$ sudo chkconfig fake_service off`
* To test this completely you’ll have to reboot the system to see if it comes on automatically. You can also try varying the runlevels in which the service is running.

### Lab 39.2: Adding a New Startup Service with systemd
----
As mentioned in the previous exercise, you can still use the **SysVinit** startup script procedure with **systemd** but this is
deprecated.  
* The analogous procedure is to create (as root) a file directly under `/etc/systemd/system` or somewhere else in that directory tree; distributions have some varying tastes on this. For example a very minimal file named `/etc/systemd/system/fake2.service` containing the following:
    ```
    [Unit]
    Description=fake2
    # service should start only after the network does
    After=network.target

    [Service]
    ExecStart=/bin/sh -c '/bin/echo I am starting the fake2 service ; /bin/sleep 30'
    ExecStop=/bin/echo I am stopping the fake2 service

    [Install]
    # service should start when we reach multiple-user mode, equivalent to runlevels 2 and 3 in SysVinit
    # "graphical.target" would correlate with runlevel 5
    WantedBy=multi-user.target
    ```
* Now all we have to do to start, stop, and check the service status are to issue the commands:
    * `$ sudo systemctl start fake2.service`
    * `$ sudo systemctl status fake2.service`
        ```
        ● fake2.service - fake2
            Loaded: loaded (/etc/systemd/system/fake2.service; disabled; vendor preset: disabled)
            Active: active (running) since Tue 2019-06-04 20:52:02 PDT; 9s ago
          Main PID: 15636 (sh)
             Tasks: 2
            CGroup: /system.slice/fake2.service
                    ├─15636 /bin/sh -c /bin/echo I am starting the fake2 service ; /bin/sleep 30
                    └─15644 /bin/sleep 30

        Jun 04 20:52:02 centos systemd[1]: Started fake2.
        Jun 04 20:52:02 centos sh[15636]: I am starting the fake2 service
        ```
    * `$ sudo systemctl stop fake2.service`
* If you are fiddling with the unit file while doing this you’ll need to reload things with:
    * `$ sudo systemctl daemon-reload`
* To keep an eye directly on the output you can do:
    * `$ sudo tail -f /var/log/messages`
        ```
        Jun  4 20:54:31 centos systemd: Configuration file /etc/systemd/system/fake2.service is marked executable. Please remove executable permission bits. Proceeding anyway.
        Jun  4 20:54:31 centos systemd: Started fake2.
        Jun  4 20:54:31 centos sh: I am starting the fake2 service
        Jun  4 20:54:43 centos systemd: Configuration file /etc/systemd/system/fake2.service is marked executable. Please remove executable permission bits. Proceeding anyway.
        Jun  4 20:54:46 centos systemd: Stopping fake2...
        Jun  4 20:54:46 centos echo: I am stopping the fake2 service
        Jun  4 20:54:46 centos systemd: Stopped fake2.
        Jun  4 20:54:46 centos systemd: Configuration file /etc/systemd/system/fake2.service is marked executable. Please remove executable permission bits. Proceeding anyway.
        Jun  4 20:54:46 centos systemd: Configuration file /etc/systemd/system/fake2.service is marked executable. Please remove executable permission bits. Proceeding anyway.
        ```
* To set things up so the service turns on or off on system boot:
    * `$ sudo systemctl enable fake2.service`
        ```
        Created symlink from /etc/systemd/system/multi-user.target.wants/fake2.service to /etc/systemd/system/fake2.service.
        ```
    * `$ sudo systemctl disable fake2.service`
        ```
        Removed symlink /etc/systemd/system/multi-user.target.wants/fake2.service.
        ```
* Once again, you really need to reboot to make sure it has taken effect.

### Paths and Commands
----
  
#### Paths  
  
Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
startup/shutdown | `/sbin/init` | coordinates later stages of the boot process, configures all aspects of the environment, and starts the processes needed for logging into the system | LFS201 39.4
startup/shutdown | `/etc/sysconfig/network` | location for system hostname(?) in Red Hat | LFS201 39.7
startup/shutdown | `/etc/HOSTNAME` | location for system hostname(?) in SUSE | LFS201 39.7
startup/shutdown | `/etc/hostname` | systemd location for system hostname | LFS201 39.7
startup/shutdown | `/etc/vconsole.conf` | systemd default keyboard mapping and console font | LFS201 39.7
startup/shutdown | `/etc/sysctl.d/*.conf` | systemd drop-in directory for kernel sysctl parameters | LFS201 39.7
startup/shutdown | `/etc/os-release` | systemd distribution ID file | LFS201 39.7
startup/shutdown | `/etc/inittab` | read when the init process is started (SysVinit) | LFS201 39.12
startup/shutdown | 1 `/etc/rc.d/rc.sysinit` | run first on SysVinit systems | LFS201 39.13
startup/shutdown | 2 `/etc/rc.d/rc` | run second with the desired runlevel as an argument on SysVinit systems | LFS201 39.13
startup/shutdown | 3 `rc.d/rc[0-6].d` | scripts here are run after `/etc/rc.d/rc` on SysVinit systems | LFS201 39.13
startup/shutdown | `/etc/rc.d/rc.local` | script that may be used to start system-specific applications on SysVinit systems | LFS201 39.13
startup/shutdown | `/etc/init.d` | hold all of the actual startup scripts on SysVinit systems | LFS201 39.13
startup/shutdown | `/etc/init/rcS.conf` | Upstart configuration source; run when the kernel starts the init process | LFS201 39.17
startup/shutdown | `/etc/rc.sysinit` | Upstart configuration source | LFS201 39.17
startup/shutdown | `/etc/inittab` | Upstart configuration source | LFS201 39.17
startup/shutdown | `/etc/init/rc.conf` | Upstart configuration source | LFS201 39.17
startup/shutdown | `/etc/rc[0-5].d` | Upstart configuration source | LFS201 39.17
startup/shutdown | `/etc/init/start-ttys.conf` | Upstart configuration source | LFS201 39.17
startup/shutdown | `/etc/systemd/system` | location for **systemd** services | Lab 39.2
    
#### Commands  
  
Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
startup/shutdown | `$ telinit ...` | used to send control commands to the init daemon | LFS201 39.6
startup/shutdown | `$ systemctl [options] command [name]` | general syntax; main utility for managing services | LFS201 39.8
startup/shutdown | `$ systemctl` | show the status of everything that systemd controls | LFS201 39.8
startup/shutdown | `$ systemctl list-units -t service --all` | show all available services | LFS201 39.8
startup/shutdown | `$ systemctl list-units -t service` | show only active services | LFS201 39.8
startup/shutdown | `$ sudo systemctl start foo` | start (activate) one or more units, where a unit can be a service or a socket | LFS201 39.8
startup/shutdown | `$ sudo systemctl start foo.service` | start (activate) one or more units, where a unit can be a service or a socket | LFS201 39.8
startup/shutdown | `$ sudo systemctl start /path/to/foo.service` | start (activate) one or more units, where a unit can be a service or a socket | LFS201 39.8
startup/shutdown | `$ sudo systemctl stop foo.service` | stop (deactivate) a service | LFS201 39.8
startup/shutdown | `$ sudo systemctl enable sshd.service` | enable a service (equivalent of **chkconfig on/off** and doesn't actually start the service) to start at boot | LFS201 39.8
startup/shutdown | `$ sudo systemctl disable sshd.service` | disable a service | LFS201 39.8
startup/shutdown | `$ runlevel` | display current runlevel, where first character is the previous level (N means unknown) | LFS201 39.11
startup/shutdown | `$ sudo /sbin/telinit 5` | change from runlevel 3 to runlevel 5 | LFS201 39.11
startup/shutdown | `$ chkconfig ...` | used to query and configure what runlevels the various system services are to run in; used to manage startup script symbolic links | LFS201 39.14
startup/shutdown | `$ chkconfig some_service` | check a particular service to see if it is set up to run in the current runlevel; true if running, false otherwise | LFS201 39.14
startup/shutdown | `$ chkconfig --list [service names]` | see what services are configured to run in each of the runlevels | LFS201 39.14
startup/shutdown | `$ sudo chkconfig some_service on` | turn on a certain service next time the system boots (does not affect the current state) | LFS201 39.14
startup/shutdown | `$ chkconfig some_service off` | do not turn a certain service on next time the system boots (does not affect the current state) | LFS201 39.14
startup/shutdown | `$ sudo service some_service [stop | start]` | start or stop a service | LFS201 39.14
startup/shutdown | `$ service ...` | used to run a System V init script (scripts stored in `/etc/init.d`) | LFS201 39.15
startup/shutdown | `$ sudo service network status` | shows the current status of a particular service | LFS201 39.15
startup/shutdown | `$ sudo service network` | shows available options for **service**, which vary by the particular service | LFS201 39.15
startup/shutdown | `$ sudo service --status-all` | shows status of all services on the system | LFS201 39.15
startup/shutdown | `$ sudo apt install sysvinit-utils chkconfig` | install sysvinit-utils and chkconfig on Debian-based system and older Ubuntu systems | LFS201 39.16
startup/shutdown | `$ sudo invoke-rc.d cups [ status | start | stop ]` | equivalent of **service** on Ubuntu | LFS201 39.16
startup/shutdown | `$ sudo status cups` | check or change the cups situation; **status** preferred over **invoke-rc.d** | LFS201 39.16
startup/shutdown | `$ sudo update-rc.d cups [ defaults | purge ]` | equivalent of **chkconfig** | LFS201 39.16
startup/shutdown | `$ sudo sysv-rc-conf cups [ on | off ]` | equivalent of **chkconfig** | LFS201 39.16
startup/shutdown | `$ initctl options command` | general syntax; view, start, stop jobs (in Upstart) in much the same way that **service** does | LFS201 39.18
startup/shutdown | `$ sudo chkconfig --list fake_service` | show the runlevels for fake_service | Lab 39.1
startup/shutdown | `$ sudo chkconfig --add fake_service` | add fake_service for management | Lab 39.1
startup/shutdown | `$ sudo systemctl status fake2.service` | show status of fake2.service | Lab 39.2
startup/shutdown | `$ sudo systemctl daemon-reload` | reload 'things' after editing 'unit file' | Lab 39.2
apps, monitoring, troubleshooting | `$ sudo tail -f /var/log/messages` | view log messages in real-time | Lab 39.2
