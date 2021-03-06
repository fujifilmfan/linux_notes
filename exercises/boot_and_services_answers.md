Boot and Services Management
-----

### Chapters: LFS101: , LFS201: 37, 38, 39

### Paths
* `/etc/sysconfig` files here are used when starting, stopping, configuring or querying system services on RHEL 7
* `/etc/default/useradd` sets the defaults that are used when new user accounts are being created
* `/sbin/init` (**init**) is the first user-level process (or task) run on the system and continues to run until the system is shutdown
* systemd prefers to use new standardized configuration files, like `/etc/hostname`, which would replace `/etc/sysconfig/network` in Red Hat
* `/etc/os-release`: distribution ID file
  
### SysVinit runlevels
* display current runlevel, where first character is the previous level (N means unknown) - `$ runlevel`
* change from runlevel 3 to runlevel 5 - `$ sudo /sbin/telinit 5`

### service
* see the current status of httpd, sshd, and network - `$ sudo service network status`
* see available options for httpd, sshd, and network - `$ sudo service network` 
* see status of all services on the system - `$ sudo service --status-all` 

### systemctl
* check whether httpd, sshd, and network are set up to run in the current runlevel `$ systemctl is-enabled sshd.service`
* see what services are configured to run in each of the runlevels - `$ systemctl list-unit-files`
* see the current status of httpd, sshd, and network - `$ systemctl status sshd.service`

### Adding a new startup service with SysVinit
* move fake_service script to `/etc/init.d/fake_service` and make it executable - `$ sudo chmod 755 /etc/init.d/fake_service`
* you’ll notice the script includes the file `/etc/sysconfig/fake service`; give it the following contents:
    ```
    VAR1= "true"
    VAR2= "true"
    ```
* show fake_service actions - `$ sudo service fake_service`
* turn fake_service on - `$ sudo service fake_service start` (might not actually be running)
* turn fake_service off - `$ sudo service fake_service stop` (might not actually be running)
* look at the file named `/var/log/fake_service.log`
* make fake_service start when the system starts - `$ sudo chkconfig --add fake_service`
* turn fake_service on at boot time - `$ sudo chkconfig fake_service on`
* show fake_service runlevels - `$ sudo chkconfig --list fake_service`
* turn fake_service off at boot time- `$ sudo chkconfig fake_service off`

### Adding a new startup service with systemd
* show the status of everything that systemd controls - `$ systemctl`
* show all available services - `$ systemctl list-units -t service --all`
* show only active services - `$ systemctl list-units -t service`
* put fake2.service in `/etc/systemd/system/`
* start fake2.service - `$ sudo systemctl start fake2.service`
* check fake2.service - `$ sudo systemctl status fake2.service`
* stop fake2.service - `$ sudo systemctl stop fake2.service`
* "reload things if you're fiddling with the unit file" - `$ sudo systemctl daemon-reload`
* keep an eye on log messages - `$ sudo tail -f /var/log/messages`
* make fake2.service start at boot - `$ sudo systemctl enable fake2.service`
* make fake2.service not start at boot - `$ sudo systemctl disable fake2.service`

### Boot steps quiz
Organize the basic steps of the boot sequence in the correct order.  
1. The boot loader is executed.
2. The Linux kernel and the **initrd** or **initramfs** image are loaded into memory and the kernel executes.
3. The **init** process starts.
4. Additional kernel modules (including device drivers) are loaded, and system services are started.

### Extras
* Shutting down and rebooting
    * shutdown in one minute with a message - `$ sudo shutdown -h +1 "Power Failure imminent"` (note: "if the time argument is used, 5 minutes before the system goes down the `/run/nologin` file is created to ensure that further logins shall not be allowed")
    * shutdown now - `$ sudo shutdown -h now` or `$ sudo shutdown now`?
    * reboot now - `$ sudo shutdown -r now`
* chkconfig
    * check whether httpd, sshd, and network are set up to run in the current runlevel - `$ chkconfig sshd` or `$ systemctl is-enabled sshd.service`
    * see what services are configured to run in each of the runlevels - `$ chkconfig --list [service names]` or `$ systemctl list-unit-files`
    * turn on a certain service next time the system boots (does not affect the current state) - `$ sudo chkconfig some_service on`
    * do not turn a certain service on next time the system boots (does not affect the current state) - `$ chkconfig some_service off` 
    * start or stop a service - `$ sudo service some_service [stop | start]` 
    * add your own services and write your own startup scripts by placing a script in `/etc/init.d` which has to have certain features in it and then use `chkconfig --add` to enable or `chkconfig --del` to disable use of the on and off instructions
* GRUB
    * at boot, a basic configuration file is read, either `/boot/grub/grub.cfg` or `/boot/grub2/grub.cfg` (mine), depending on distribution
    * this file is auto-generated by **update-grub** (or **grub2-mkconfig** on RHEL 7) based on configuration files in the `/etc/grub.d` directory and on `/etc/default/grub` and should never be edited by hand
    * install GRUB for version 2 (read the **man** first!!) - `$ sudo grub2-install /dev/sda` 
    * in both GRUB versions, the first hard drive is denoted as hd0, the second is hd1, etc; however, in Version 1, partitions start counting from 0, and in Version 2 from 1:
        * sda1 is (hd0,1) in GRUB 2, but (hd0,0) in GRUB 1
        * sdc4 is (hd2,4) in GRUB 2, but (hd2,3) in GRUB 1
    * the root partition is not the same as the root directory - it's the partition that contains the kernel itself in the `/boot` directory
* SysVinit and /etc/inittab
    * when the init process is started, the first thing it does is to read `/etc/inittab`- not on my systemd machine
* Lab 38.1: Booting into non-graphical mode using GRUB 
    * reboot your machine and go into the **GRUB** interactive shell - hit **e**
    * make your system boot into non-graphical mode. How you do this depends on the system - add **3** to the end of the line beginning "linux16..." according to [Boot into non-graphical mode from Grub prompt RHEL/CentOS 7](https://unix.stackexchange.com/questions/303406/boot-into-non-graphical-mode-from-grub-prompt-rhel-centos-7)
    * hit the proper key to make system continue booting - hit CTRL-X or CTRL-C (I don't remember) to boot
    * after the system is fully operational in non-graphical mode, bring it up to graphical mode - `$ sudo systemctl start gdm`
* 39.13: SysVinit Startup Scripts

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
