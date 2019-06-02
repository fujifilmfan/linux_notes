Paths and Commands by Chapter
-----------------------------

### Contents
----
[Paths](#paths)  
[Commands](#commands)  

### Paths  
----
  
Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
filesystem | `/` | primary directory of entire FH | LFS201 2.7
filesystem | `/bin` | executables needed in **single user mode** | LFS201 2.7
filesystem | `/boot` | files needed for boot, like kernel, **initrd** or **initramfs**, bootloaders | LFS201 2.7
filesystem | `/dev` | **device nodes**; pseudo-filesystem | LFS201 2.7
filesystem | `/etc` | system-wide config files | LFS201 2.7
filesystem | `/home` | user **home directories** | LFS201 2.7
filesystem | `/lib` | libraries required by binaries in `/bin` and `/sbin`; Linux kernel modules | LFS201 2.7
filesystem | `/lib64` | 64-bit libraries required by binaries in `/bin`, `/sbin` | LFS201 2.7
filesystem | `/media` | mount points for removable media | LFS201 2.7
filesystem | `/mnt` | temporarily mounted filesystems | LFS201 2.7
filesystem | `/opt` | optional application software packages | LFS201 2.7
filesystem | `/proc` | virtual pseudo-filesystem w/ info about system and processes | LFS201 2.7
filesystem | `/sys` | virtual pseudo-filesystem w/ info about system and processes; similar to a **device tree**, part of **Unified Device Model** | LFS201 2.7
filesystem | `/root` | home directory for **root** user | LFS201 2.7
filesystem | `/sbin` | essential system binaries | LFS201 2.7
filesystem | `/srv` | site-specific data; seldom used | LFS201 2.7
filesystem | `/tmp` | temporary files; lost on reboot in many distributions | LFS201 2.7
filesystem | `/usr` | multi-user applications, utilities and data; theoretically read-only | LFS201 2.7
filesystem | `/var` | variable data that changes during system operation | LFS201 2.7
filesystem | `/misc` | some distros | LFS201 2.7
filesystem | `/run` | some distros; pseudo-filesystem | LFS201 2.7
filesystem | `/tftpboot` | some distros | LFS201 2.7
apps, filesystem | `/usr/bin` | programs required only by non-root users (as opposed to those in `/bin`) | LFS201 2.9
boot, filesystem | `vmlinuz` | file for booting (compressed Linus kernel) | LFS201 2.10
boot, filesystem | `initramfs` | (**initial RAM Filesystem**, mounted before real root filesystem becomes available) | LFS201 2.10
boot, filesystem | `initrd` | (**initial RAM disk**) instead of `initramfs` on some distros | LFS201 2.10
filesystem | `config` | config file under `/boot`; used when compiling kernel; used for bookkeeping and reference when debugging | LFS201 2.11
filesystem | `System.map` | under `/boot`; kernel **symbol table**, useful for debugging; gives hex addresses of all kernel symbols | LFS201 2.11
filesystem | `/etc/skel` | used to populate new home dirs | LFS201 2.13
filesystem | `/etc/init.d` | which contains start up and shut down scripts when using System V initialization | LFS201 2.13
filesystem | `/lib/modules/<kernel-version-number>` | contains kernel modules that are loadable extensions of the kernel | LFS201 2.15
filesystem | `/lib/security` | contains Pluggable Authentication Modules | LFS201 2.15
filesystem | `/run/media/[username]/....` | contains removable media | LFS201 2.16
filesystem | `/opt/application` | example | LFS201 2.18
filesystem | `/opt/application/bin` | example | LFS201 2.18
filesystem | `/opt/application/man` | example | LFS201 2.18
filesystem | `/opt/skype` | example | LFS201 2.18
filesystem | `/opt/google` | example | LFS201 2.18
filesystem | `/opt/google/chrome` | example | LFS201 2.18
filesystem | `/opt/google/earth` | example | LFS201 2.18
filesystem | `/opt/google/talkplugin` | example | LFS201 2.18
filesystem | `/opt/bin` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/opt/doc` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/opt/include` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `opt/info` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/opt/lib` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `opt/man` | reserved for local system administrator use; packages may provide files linked to these reserved directories but must be able to function without the programs being in them | LFS201 2.18
filesystem | `/proc/interrupts` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/meminfo` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/mounts` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/partitions` | provides an up-to-the-moment glimpse of the system's hardware | LFS201 2.19
filesystem | `/proc/filesystems` | provides system configuration information and interfaces | LFS201 2.19
filesystem | `/proc/sys/` | provides system configuration information and interfaces | LFS201 2.19
filesystem | `/proc/scsi/` | information about all physical **SCSI** devices; similar for **process** directories | LFS201 2.19
filesystem | `$ sudo systemctl mask tmp.mount` | prevent using `/tmp` as a mount point for a **ram disk** using the **tmpfs** filesystem | LFS201 2.24
filesystem | `/usr/local` | contains local binaries | LFS201 2.25
filesystem | `/usr/share/man` | contains **man** pages | LFS201 2.25
filesystem | `/var/log` | contains most log files | LFS201 2.26
filesystem | `/var/spool` | contains local files for processes such as mail, printing, and cron jobs while awaiting action | LFS201 2.26
filesystem | `/proc/cpuinfo` | contains information on each of my 4 cores | LFS201 Lab 2.2
filesystem | `/proc/meminfo` | total, free, available, active, inactive, etc. memory | LFS201 Lab 2.2
filesystem | `/proc/mounts` | I don't know what I'm looking at here | LFS201 Lab 2.2
filesystem | `/proc/swaps` | shows the /dev/sda2 partition | LFS201 Lab 2.2
filesystem | `/proc/version` | linux version 3.10.0-862.3.2.el7.x86_64, gcc version, Red Hat version (no mention of CentOS 7) | LFS201 Lab 2.2
filesystem | `/proc/partitions` | lists partitions (sda, sda1, sda2, sr0) | LFS201 Lab 2.2
filesystem | `/proc/interrupts` | seems like interrupts by type and CPU | LFS201 Lab 2.2
processes | `/proc/sys/kernel/pid_max` | stores max pid, by default a 16-bit number, 32768 | LFS201 3.6
processes | `/etc/security/limits.conf` | set resource limits for all logged-in users, not just current shell | LFS201 3.8 
processes | `/etc/passwd` | changed by running `passwd` but not directly editable by normal user | LFS201 3.9
processes | `/etc/shadow` | changed by running `passwd` but not directly editable by normal user | LFS201 3.9
processes | `/etc/init.d` | scripts here start various daemons | LFS201 3.14
processes | `/etc/init.d/functions` | contains functions used by scripts in `/etc/init.d` | LFS201 3.14
processes | `/etc/security/limits.conf` | allow normal users to set niceness | LFS201 3.19
processes | `/etc/ld.so.conf` | used by **ldconfig**; lists the directories searched for shared libraries | LFS201 3.23
packages | `/var/lib/rpm` | default system directory which holds RPM database files in the form of **Berkeley DB** hash files | LFS201 6.7
packages | `/usr/bin/yum*` | contains plugins and companion programs | LFS201 8.5
packages | `/usr/sbin/yum*` | contains plugins and companion programs | LFS201 8.5
packages | `/etc/yum.repos.d/` | contains repository configuration files; they have a **.repo** extension | LFS201 8.6
monitoring, system | `/var/log` | log files | LFS201 11.5
monitoring, system | `/etc/logrotate.conf` | controls **logrotate** | LFS201 11.6
monitoring, system | `/proc/sys` | shows tunable system parameters | LFS201 11.10
monitoring, system | `/proc/sys/abi/` | Contains files with application binary information | LFS201 11.10
monitoring, system | `/proc/sys/debug/` | Debugging parameters; for now, just some control of exception reporting | LFS201 11.10
monitoring, system | `/proc/sys/dev/` | Device parameters, including subdirectories for cdrom, scsi, raid, and parport | LFS201 11.10
monitoring, system | `/proc/sys/fs/` | Filesystem parameters, including quota, file handles used, and maximums, inode and directory information, etc. | LFS201 11.10
monitoring, system | `/proc/sys/kernel/` | Kernel parameters | LFS201 11.10
monitoring, system | `/proc/sys/net/` | Network parameters; there are subdirectories for ipv4, netfilter, etc. | LFS201 11.10
monitoring, system | `/proc/sys/vm` | Virtual memory parameters | LFS201 11.10
monitoring, system | `/sys/class/net` | contains network device information | LFS201 11.12
monitoring, system | `/var/log/sa` | contains data stored by system activity data collector | LFS201 11.13
monitoring, system | `/etc/cron.d/sysstat` | cron job for periodic data collection for sadc | LFS201 11.13
monitoring, system | `/proc/<PID>/cmdline` | command line used to start the process | LFS201 11.15
monitoring, system | `/var/log` | log files | LFS201 11.5
monitoring, system | `/etc/logrotate.conf` | controls **logrotate** | LFS201 11.6
monitoring, system | `/proc/sys` | shows tunable system parameters | LFS201 11.10
monitoring, system | `/proc/sys/abi/` | Contains files with application binary information | LFS201 11.10
monitoring, system | `/proc/sys/debug/` | Debugging parameters; for now, just some control of exception reporting | LFS201 11.10
monitoring, system | `/proc/sys/dev/` | Device parameters, including subdirectories for cdrom, scsi, raid, and parport | LFS201 11.10
monitoring, system | `/proc/sys/fs/` | Filesystem parameters, including quota, file handles used, and maximums, inode and directory information, etc. | LFS201 11.10
monitoring, system | `/proc/sys/kernel/` | Kernel parameters | LFS201 11.10
monitoring, system | `/proc/sys/net/` | Network parameters; there are subdirectories for ipv4, netfilter, etc. | LFS201 11.10
monitoring, system | `/proc/sys/vm` | Virtual memory parameters | LFS201 11.10
monitoring, system | `/sys/class/net` | contains network device information | LFS201 11.12
monitoring, system | `/var/log/sa` | contains data stored by system activity data collector | LFS201 11.13
monitoring, system | `/etc/cron.d/sysstat` | cron job for periodic data collection for sadc | LFS201 11.13
monitoring, system | `/proc/<PID>/cmdline` | command line used to start the process | LFS201 11.15
monitoring, memory | `/proc/sys/vm` | contains tunable knobs to control the **Virtual Memory** system | LFS201 13.6
monitoring, memory | `Documentation/sysctl/vm.txt` | documentation in the kernel source for `/proc/sys/vm` | LFS201 13.6
monitoring, system | `/etc/sysctl.conf` | configuration for values set at boot time | LFS201 13.6
monitoring, system | `/proc/meminfo` | contains a summary of memory statistics | LFS201 13.8
monitoring, system | `/proc/sys/vm/overcommit_memory` | overcommit memory settings | LFS201 13.9
monitoring, system | `/proc/sys/vm/overcommit_ratio` | change percentage of RAM available before memory requests fail | LFS201 13.9
monitoring, system | `/proc/<pid>/oom_score` | contains **badness* value | LFS201 13.9
monitoring, system | `/proc/<pid>/oom_adj` | shows number of bits to adjust badness score if two entries are in same directory; deprecated | LFS201 13.9
monitoring, system | `/proc/<pid>/oom_score_adj` | used to directly adjust the badness point value | LFS201 13.9
monitoring, system | `/proc/swaps` | shows swap partitions and files on the system | LFS201 Lab 13.1
monitoring, system | `/var/log/messages` | contains system messages | LFS201 Lab 13.1
monitoring, system | `/var/log/syslog` | contains system messages | LFS201 Lab 13.1
i/o | `/sys/block/sda/queue/scheduler` | I/O scheduling information | LFS201 15.5
i/o | `/sys/block/sda/queue/scheduler` | I/O scheduling information | LFS201 15.6
i/o | `/sys/block/<device>/queue/iosched` | contains tunables | LFS201 15.7
filesystem | `/proc/filesystems` | currently registered filesystems | LFS201 16.10
filesystem | `/dev/zero` | special file in Unix-like operating systems that provides null characters | LFS201 16.10
filesystem | `/dev/shm` | most modern Linux distributions mount an instance of **tmpfs** here | LFS201 Lab 16.1
filesystem | `$ sudo mkdir /mnt/tmpfs` | create a **tmpfs** filesystem | LFS201 Lab 16.1
filesystem | `$ sudo mount -t tmpfs none /mnt/tmpfs` | mount a new **tmpfs** filesystem | LFS201 Lab 16.1
filesystem | `$ df -h /mnt/tmpfs` | see how much space the filesystem has been given and how much it is using
filesystem | `$ sudo mount -t tmpfs -o size=1G none /mnt/tmpfs` | change the allotted size of **tmpfs** as a mount option | LFS201 Lab 16.1
filesystem | `$ sudo umount /mnt/tmpfs` | unmount the **tmpfs** filesystem | LFS201 Lab 16.1
filesystem | `$ df -h /dev/shm` | see how much space the filesystem has been given and how much it is using | LFS201 Lab 16.1
filesystem | `$ df -h | grep tmpfs` | many distributions mount multiple instances of **tmpfs** | LFS201 Lab 16.1
filesystem, partitions | `/etc/fstab` | configuration file containing the necessary information to automate the process of mounting partitions | LFS201 17.19
partitions | `/dev/disk` | contains informaiton about system partitions | LFS201 18.11
filesystem, partitions | `/etc/fstab` | configuration file containing the necessary information to automate the process of mounting partitions | LFS201 18.11
filesystem, partitions | `/proc/partitions` | stores information about partitions on the system | LFS201 Lab 18.2
filesystem, memory | `/proc/swaps` | stores information about swap areas | LFS201 19.4
filesystem | `aquoata.user` and `aquota.group` | quota database files | LFS201 19.5
encryption | `/proc/crypto` | shows the methods the system supports | LFS201 22.7
encryption | `/etc/crypttab` | configuration file for encrypted block devices | LFS201 22.8
partitions | `/dev/mapper` | LVM logical volumes (as opposed to LVM *physical* volumes) | LFS201 22.9
lvm | `/sbin/lv*` | logical volume utilties | LFS201 23.7
raid | `/dev/mdX` | RAID device | LFS201 24.4
raid | `/proc/mdstat` | RAID status | LFS201 24.6
boot, kernel | `/boot/grub2/grub.cfg` | GRUB configuration file | LFS201 25.6
boot, kernel | `/proc/cmdline` | Linux command line used at boot | LFS201 25.6
boot, kernel | `Documentation/kernel-parameters.txt` | parameter documentation in kernel source | LFS201 25.7
boot, kernel | `/proc/sys` | contains current kernel values | LFS201 25.8
boot, kernel | `/etc/sysctl.conf` | config file allowing settings to be fixed at boot time | LFS201 25.8
boot, kernel | `/usr/lib/sysctl.d/00-system.conf` | settings location on **systemd** distros | LFS201 25.8
kernel, processes | `/proc/sys/kernel/pid_max` | stores setting for the highest PID before wrapping | LFS201 Lab 25.2
kernel, modules | `/lib/modules/<kernel-version>` or `/lib/modules/$(uname -r)` | normal filesystem location for kernel modules | LFS201 26.6
kernel, modules | `/lib/modules/$(uname -r)/modules.dep` | module dependency table | LFS201 26.7
kernel, modules | `/sys/module/<my_module>` | also contains information about kernel modules | LFS201 26.9
kernel, modules | `/sys/module/<my_module>/parameters` | contains module parameters | LFS201 26.9
kernel, modules | `/etc/modprobe.d` | has files that control some parameters when loading with **modprobe** | LFS201 26.11
devices | `/dev` | holds device nodes | LFS201 27.4
devices | `/sys` | contains **sysfs** pseudo-filesystem, which has device node information | LFS201 27.8
devices | `/etc/udev/udev.conf` | main configuration file with info on where to place nodes, default permissions, etc. | LFS201 27.8
devices | `/etc/udev/rules.d` and `/usr/lib/udev/rules.d` | hold rules for naming device nodes | LFS201 27.8
virtualization | `/proc/cpuinfo` | has CPU information, including virtualization abilities | LFS201 28.9
accounts | `/etc/passwd` | file that describes basic account attributes | LFS201 30.5
accounts | `/etc/shadow` | file that contains password hashes | LFS201 30.6
accounts | `/etc/group` | file that contains group information | LFS201 30.7
accounts | `/etc/login.defs` | contains account configurations, such as UID and GID information | LFS201 30.6
accounts | `/etc/nologin.txt` | contains message for login error when user is locked | LFS201 30.8
accounts | `/sbin/nologin` | prevents login by denying a shell?? | LFS201 30.8
accounts, network | `/etc/ssh/sshd_config` | configures **ssh** | LFS201 30.16  
accounts, network | `/etc/securetty` and **pam_securetty.so** | used with **PAM** | LFS201 30.16
accounts, network | `~/.ssh` | SSH configuration directory | LFS201 30.18
accounts, network | `~/.ssh/id_rsa` | user's private encryption key | LFS201 30.18
accounts, network | `~/.ssh/id_rsa.pub` | user's public encryption key | LFS201 30.18
accounts, network | `~/.ssh/authorized_keys` | list of public keys that are permitted to login | LFS201 30.18
accounts, network | `~/.ssh/known_hosts` | list of hosts from which logins have been allowed in the past | LFS201 30.18
accounts, network | `~/.ssh/config` | configuration file for specifying various options | LFS201 30.18
accounts | `/etc/group` | holds group definitions | LFS201 31.4
accounts | `/etc/gshadow` | holds group passwords | LFS201 31.4
accounts, system | `/etc/profile` | contains system-wide environment and startup programs | LFS201 31.6
accounts, system | `/etc/bashrc` | contains system-wide functions and aliases | LFS201 31.6
accounts, auth | `/etc/pam.d` | directory containing files of authentication rules for each application or service using PAM | LFS201 33.5
accounts, auth | `/lib*/security` | contains modules used by PAM for authentication | LFS201 33.7
accounts, auth | `/etc/openldap/ldap.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/pam_ldap.conf/` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/nslcd.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/sssd/sssd.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/nsswitch.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
networks, security | `/etc/firewalld` | configuration files for **firewalld**; override those in `/usr/lib/firewalld` | LFS201 36.9
networks, security | `/usr/lib/firewalld` | configuration files for **firewalld** `/etc/firewalld` | LFS201 36.9
networks, security | `/etc/firewalld/zones/internal.xml` | created when assigning an interface to a particular zone permanently | LFS201 36.12
networks, system | `/etc/services` | stores information about services on the machine, including name, port number, protocol, and aliases; a mapping between human-friendly textual names for internet services, and their underlying assigned port numbers and protocol types; every networking program should look into this file to get the port number (and protocol) for its service | LFS201 36.15
networks, security | `/etc/firewalld/services` | add new services by editing this | LFS201 36.14
startup/shutdown | `/boot/grub/grub.cfg` | GRUB configuration file | LFS201 38.4
startup/shutdown | `/boot/grub2/grub.cfg` | GRUB configuration file | LFS201 38.4
startup/shutdown | `/etc/grub.d` | used by **update-grub** or **grub2-mkconfig** to modify `grub.cfg` | LFS201 38.4
startup/shutdown | `/etc/default/grub` | used by **update-grub** or **grub2-mkconfig** to modify `grub.cfg` | LFS201 38.4
startup/shutdown | `/boot/grub` or `/boot/grub2` | GRUB files needed at boot time | LFS201 38.6
startup/shutdown | `vmlinuz-*` | file needed by the Linux kernel | LFS201 38.6
startup/shutdown | `initramfs-*` | file needed by the Linux kernel | LFS201 38.6
startup/shutdown | 1 `/etc/default/grub` | file used to update GRUB configuration file `grub.cfg` | LFS201 38.8
startup/shutdown | 2 `/etc/grub.d` | directory used to update GRUB configuration file `grub.cfg` | LFS201 38.8



### Commands  
----
  
Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
filesystem | `$ sudo du -cshx --exclude=/proc *` | calculate overall size of directories | LFS201 Lab 2.1
filesystem | `$ sudo du --max-depth=1 -hx /` | calculate overall size of directories | LFS201 Lab 2.1
processes | `$ ulimit [options] [limit]` | generic syntax | LFS201 3.8
processes | `$ ulimit -a` | displays or resets resource limits associated with processes running under a shell | LFS201 3.8
processes | `$ ulimit -n 1600` | changes maximum number of file descriptors | LFS201 3.8
processes | `$ passwd ...` | **setuid** program; changes password | LFS201 3.9
processes | `&` | at end of command for background processing: LFS201 3.14
processes | `$ ps -elf` | show kernel-created processes; list all processes on the system while showing the parent process IDs | LFS201 3.16
processes | `$ nice -n 5 command [ARGS]` | increase niceness by 5 | LFS201 3.18
processes | `$ nice -5 command [ARGS]` | increase niceness by 5 | LFS201 3.18
processes | `$ renice +3 13848` | increase niceness by 3 of the process with pid = 13848, where 13848 is the pid of the process; 3 would be the new niceness, not the change | LFS201 3.19
processes | `$ ps lf` | shows processes and priorities | LFS201 3.20
processes | `$ gnome-system-monitor` | opens gui system monitor | LFS201 3.20
processes | `$ ldd /usr/bin/vi` | shows shared libraries that executable requires | LFS201 3.23
processes | `$ ulimit -H -n` | view hard limit | LFS201 Lab 3.1
processes | `$ ulimit -S -n` | view soft limt | LFS201 Lab 3.1
processes | `$ ulimit -n 4096` | set soft limit to hard limit value(??) | LFS201 Lab 3.1
processes | `$ ulimit -H -n 2048` | set hard limit to 2048(??) | LFS201 Lab 3.1
processes | `$ ipcs` |  | LFS201 Lab 3.2
processes | `$ ipcs -p` |  | LFS201 Lab 3.2
processes | `$ ps aux | grep -e 11737 -e 11044` | `-e` option is to match a pattern; also `--regexp` | LFS201 Lab 3.2
signals | `$ kill -l` | shows a list of signals with their numbers | LFS201 4.5
documentation, signals | `$ man 7 signal` | documentation for signals | LFS201 4.5
signals | `$ kill 1991` | sends a **SIGTERM (15)** to PID 1991, which can be handled | LFS201 4.6 
signals | `$ kill -9 1991` | sends a **SIGKILL (9)** which cannot be ignored and causes termination with extreme prejudice | LFS201 4.6 
signals | `$ kill -SIGKILL 1991` | sends a **SIGKILL (9)** which cannot be ignored and causes termination with extreme prejudice | LFS201 4.6
signals | `$ kill ...` | used to send any all signals, even benign informative ones | LFS201 4.6
signals | `$ killall bash` | kills all processes with the name **bash** using command name instead of PID | LFS201 4.7
signals | `$ killall -9 bash` | kills all processes with the name **bash** | LFS201 4.7
signals | `$ killall -SIGKILL bash` | kills all processes with the name **bash** | LFS201 4.7
signals | `$ pkill [-signal] [options] [pattern]` | generic syntax; sends a signal to a process using selection criteria | LFS201 4.7
signals | `$ pkill -u libby foobar`| kill all of **libby**'s processes with a name of **foobar** | LFS201 4.7
signals | `$ pkill -HUP rsyslogd`| makes **rsyslog** re-read its config file  | LFS201 4.7
packages | `$ gcc -o signals signals.c` | compiles the signals.c source code | LFS201 Lab 4.1
packages | `$ rpmbuild --rebuild` | rebuild a binary package | LFS201 5.6
apps, packages | `$ gnome-software` | opens Application Installer, a sort Linux app store | LFS201 5.15  
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
packages | `$ yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]` | remove cached information | LFS201 8.5
packages | `$ cat CentOS-Base.repo` | example of a respository file | LFS201 8.7
packages | `$ sudo yum search KEYWORD` | search for packages with keyword in name | LFS201 8.8
packages | `$ sudo yum list "*KEYWORD*"` | tells what is installed and what else is available | LFS201 8.8
packages | `$ sudo yum info KEYWORD` | get information about a package | LFS201 8.8
packages | `$ yum info "libc\*"` | wildcards can be used for most **yum** commands | LFS201 8.8
packages | `$ sudo yum list [installed | updates | available ]` | list all packages, or just those installed, available, or updates that have not yet been installed | LFS201 8.8
packages | `$ sudo yum grouplist` | show information about package groups installed or available, etc. | LFS201 8.8
packages | `$ sudo yum groupinfo GROUP1` | show information about package groups installed or available, etc. | LFS201 8.8
packages | `$ sudo yum groupinfo "Development Tools"` | show information about the "Development Tools" package group | LFS201 8.8
packages | `$ sudo yum provides ...` as in `$ sudo yum provides "/logrotate.conf"` | ShowS packages that contain a certain file name | LFS201 8.8
packages | `$ sudo yum install yum-plugin-verify` | install the **yum-plugin-verify** package | LFS201 8.9
packages | `$ sudo yum verify [package]` | verifies a package, giving the most information | LFS201 8.9
packages | `$ sudo yum verify-rpm [package]` | mimics **rpm -V** exactly | LFS201 8.9
packages | `$ sudo yum verify-all [package]` | lists all differences, including configuration files | LFS201 8.9
packages | `$ sudo yum install package1 [package2]` | installs one or more packages from repositories, resolving and installing any necessary dependencies | LFS201 8.10
packages | `$ sudo yum localinstall package-file` | installs from a local RPM; will attempt to resolve dependencies by accessing remote repos | LFS201 8.10
packages | `$ rpm -i package-file` | installs from a local RPM but will not attempt to resolve dependencies by accessing remote repos | LFS201 8.10
packages | `$ sudo yum groupinstall group-name` or `$ sudo yum install @group-name` | installs a specific software group from a repository, resolving and installing any necessary dependencies for each package in the group | LFS201 8.10
packages | `$ sudo yum remove package1 [package2]` | removes packages from the system | LFS201 8.10
packages | `$ sudo yum update [package]` | updates a package from a repository | LFS201 8.10
packages | `$ sudo find /etc -name "*.rpm*"` | search for old configuration files | LFS201 8.10
packages | `$ sudo yum list "yum-plugin*"` | lists plugins | LFS201 8.11
packages | `$ sudo yum repolist` | shows a list of all enabled repositories | LFS201 8.11
packages | `$ sudo yum shell [text-file]` | initiates an interactive shell in which to run multiple **yum** commands | LFS201 8.11
packages | `$ sudo yum install --downloadonly package` | download package but do not install it | LFS201 8.11
packages | `/var/cache/yum` | location of downloaded package by default | LFS201 8.11
packages | `$ sudo yum history` | view the history of **yum** commands and even undo or redo previous commands | LFS201 8.11
packages | `$ dnf ...` | next generation replacement for **yum** | LFS201 8.12
packages | `$ yumex` | launches graphical **yum** interface | LFS201 8.13
packages | `$ sudo yum list updates` | checks to see if there are any available updates for your system | LFS201 Lab 8.1
packages | `$ sudo yum check-update` | checks to see if there are any available updates for your system | LFS201 Lab 8.1
packages | `$ sudo yum update docker.x86_64` | updates a particular package | LFS201 Lab 8.1
packages | `$ sudo yum update` | updates a particular package | LFS201 Lab 8.1
packages | `$ sudo yum list installed "kernel*"` | list all installed kernel-related packages | LFS201 Lab 8.1
packages | `$ sudo yum list "kernel*"` | list all installed or available kernel-related packages | LFS201 Lab 8.1
packages | `$ sudo yum list` | show a complete list of available packages | LFS201 Lab 8.1
packages | `$ sudo yum install httpd-devel` | install the httpd-devel package, or anything else you might not have installed yet | LFS201 Lab 8.1
packages | `$ sudo yum search bash` | find all packages that contain a reference to **bash** in their name or description | LFS201 Lab 8.2
packages | `$ sudo yum list installed "bash*"` | find nstalled and available **bash** packages | LFS201 Lab 8.2
packages | `$ sudo yum list installed "*bash*"` | find nstalled and available **bash** packages | LFS201 Lab 8.2
packages | `$ sudo yum list available "bash*"` | find nstalled and available **bash** packages | LFS201 Lab 8.2
packages | `$ sudo yum list available "*bash*"` | find nstalled and available **bash** packages | LFS201 Lab 8.2
packages | `$ sudo yum info "bash*"` | find package information for **bash** | LFS201 Lab 8.2
packages | `$ sudo yum deplist bash` | find dependencies for the **bash** package | LFS201 Lab 8.2
packages | `$ yum grouplist` | list all package groups available on your system | LFS201 Lab 8.3
packages | `$ yum groupinfo "Backup Client"` | identify the Backup Client group and generate the information about this group | LFS201 Lab 8.3
packages | `$ sudo yum groupinstall "Backup Client"` | install the Backup Client group | LFS201 Lab 8.3
packages | `$ sudo yum groupremove "Backup Client"` | remove the Backup Client package group | LFS201 Lab 8.3
packages | `$ sudo yum install webmin` | install the webmin package | LFS201 Lab 8.4
monitoring, system | `$ sudo tail -f /var/log/messages` | view new messages continually | LFS201 11.5
monitoring, system | `$ sudo tail -f /var/log/syslog`  (or `$ sudo telinit 3`) | view new messages continually | LFS201 11.5
monitoring, system | `$ dmesg -w`  (or `$ sudo telinit 5`) | view new messages continually | LFS201 11.5
monitoring, system | `$ cat /proc/sys/kernel/threads-max` | view maximum number of threads allowed on the system | LFS201 11.10
monitoring, system | `$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'` | change maximum number of threads | LFS201 11.10
monitoring, system | `$ sudo sysctl kernel.threads-max=100000` | change maximum number of threads using **sysctl** | LFS201 11.10
monitoring, system | `$ sar <options> <interval> <count>` | generic sar (Systems Activity Reporter) usage | LFS201 11.13
monitoring, system | `$ sar -B 3 3 ` | using sar to get paging statistics | LFS201 11.13
monitoring, system | `$ sar -b 3 3` | using sar to get I/O and transfer rate statistics | LFS201 11.13
monitoring, system | `$ gnome-system-monitor` | launch graphical monitor | LFS201 Lab 11.1
monitoring, system | `$ stress -c 8 -i 4 -m 6 -t 20s` | stress test the system | LFS201 Lab 11.1
monitoring, processes | `$ ps aux` | shows all processes | LFS201 12.6
monitoring, processes | `$ ps auxf` | adding **f** flag shows how processes relate by ancestry | LFS201 12.7
monitoring, processes | `$ ps -elf` | show processes in UNIX option format | LFS201 12.8
monitoring, processes | `$ ps -o pid,uid,cputime,pmem,command` | specific the fields in the output | LFS201 12.9
monitoring, processes | `$ pstree` | show processes in tree form | LFS201 12.10
monitoring, processes | `$ pstree -aAp 2408` | shows the process tree for PID 2408 | LFS201 12.10
monitoring, processes | `$ ls -l /proc/10530/task` | see child processes of 10530 | LFS201 12.10
monitoring, processes | `$ ps -ef` | shows every process on the system using standard syntax | LFS201 Lab 12.1
monitoring, processes | `$ ps aux` | shows every process on the system using BSD syntax | LFS201 Lab 12.1
monitoring, processes | `$ ps axo "%p %n %c"` | AIX format; show only the process ID, priority, nice value, and the process command name | LFS201 Lab 12.1
monitoring, processes | `$ ps axo pid,pri,ni,cmd` | standard format; show only the process ID, priority, nice value, and the process command line | LFS201 Lab 12.1
monitoring, processes | `$ ps -o pid,pri,ni,cmd` | show only the process ID, priority, nice value, and the process command line | LFS201 Lab 12.1
monitoring, processes | `$ renice 15 -p 22171` | renice process 22171 to 15 | LFS201 Lab 12.1
monitoring, processes | `$ nice -n 10 bash` | start bash session using the nice command | LFS201 Lab 12.1
monitoring, processes | `$ dd if=/dev/urandom of=/dev/null &` | start a background process which reads from /dev/urandom and writes to /dev/null | LFS201 Lab 12.2
monitoring, processes | `$ ps -C dd -o pid,cmd,stat` | check process state of **dd**; **-C bash** for command list (instead of using **grep**) | LFS201 Lab 12.2
monitoring, memory | `$ free -m` | shows summary of memory usage | LFS201 13.5
monitoring, memory | `$ vmstat <options> <delay> <count>` | show detailed information about memory, I/O, and other activities | LFS201 13.7
monitoring, memory | `$ vmstat -S m -a 2 4` | show report at a 2 s interval 4 times | LFS201 13.7
monitoring, memory | `$ vmstat -s -S m` | use -s option to see a table of memory statistics and certain event counters | LFS201 13.7
monitoring, memory | `$ vmstat -d` | use -d option to see a table of disk statistics | LFS201 13.7 
monitoring, memory | `$ vmstat -p /dev/sdb1 2 4` | use -p option to get quick stats on one partition (sda1 or sda2 in my case) | LFS201 13.7
monitoring, system | `$ sudo /sbin/swapoff -a` | turn off all swap | LFS201 Lab 13.1
monitoring, system | `$ sudo /sbin/swapon -a` | turn on all swap | LFS201 Lab 13.1
monitoring, system | `$ stress -m 8 -t 10s` | keep 2 GB memory busy for 10 seconds | LFS201 Lab 13.1
monitoring, system | `$ dmesg` | print or control the kernel ring buffer (view messages) | LFS201 Lab 13.1
monitoring, i/o | `$ iostat` | shows a default I/O report | LFS201 14.5
monitoring, i/o | `$ iostat -k` | use -k option to show results in KB instead of blocks | LFS201 14.6
monitoring, i/o | `$ iostat -m` | use -m option to get results in MB | LFS201 14.6
monitoring, i/o | `$ iostat -N` | use -N option to show by device name (or -d for a somewhat different format) | LFS201 14.6
monitoring, i/o | `$ iostat -xk` | use -x option to see a more detailed report | LFS201 14.7
monitoring, i/o | `$ sudo iotop` | shows table of I/O usage | LFS201 14.8
monitoring, i/o | `$ sudo iotop -o` | use -o (or --only) option to show only processes or threads actually doing I/O | LFS201 14.8
monitoring, i/o | `$ ionice [-c class] [-n priority] [-p pid] [COMMAND [ARGS] ]` | usage syntax | LFS201 14.9
monitoring, i/o | `$ sudo ionice -c 2 -n 3 -p 30078` | change best effort to priority 3 for process 30078 | LFS201 14.9
monitoring, i/o | `$ sudo ionice -c 2 -n 6 -p 47` | change the priority of process 47 from be/7 to be/6 | LFS201 14.9
monitoring, performance, i/o, testing | `$ bonnie++ ...` | benchmarking application | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ time sudo bonnie++ -n 0 -u 0 -r 100 -f -b -d /mnt` | run a system test using **bonnie++** | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ bon_csv2html < bonnie++.out > bonnie++.html` | convert **bonnie++.out** to HTML | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ bon_csv2txt < bonnie++.out > bonnie++.txt` | convert **bonnie++.out** to plain text | LFS201 Lab 14.1
monitoring, performance, i/o, testing | `$ fs_mark -d /tmp -n 1000 -s 10240` | test filesystem by creating 1000 files, each 10 KB in size, and then performing an **fsync** to flush out to disk | LFS201 Lab 14.2
monitoring, performance, i/o, testing | `$ iostat -x -d /dev/sda 2 20` | gather i/o statistics while running **fs_mark** test | LFS201 Lab 14.2
monitoring, performance, i/o, testing | `$ mount -o remount,barrier=1 /tmp` | change **mount** options for **ext3** or **ext4** to improve performance | LFS201 Lab 14.2
monitoring, performance, i/o, testing | `$ mount -o remount,journal_async_commit /tmp` | change **mount** options for **ext4** to improve performance | LFS201 Lab 14.2
i/o | `$ cat /sys/block/sda/queue/scheduler` | shows which I/O schedulers are available on `/dev/sda` | LFS201 15.5
i/o | `$ cat /sys/block/<device>/queue/rotational` | check whether a device is an SSD | LFS201 15.6
i/o | `$ echo noop > /sys/block/sda/queue/scheduler` | change I/O scheduler to noop | LFS201 15.7
i/o | `$ echo 3 > /proc/sys/vm/drop_caches` | flush cached pages of memory | LFS201 Lab 15.1
filesystem | `$ cat /proc/filesystems` | shows currently registered filessytem types | LFS201 16.10
filesystem | `$ dd if=/dev/zero of=junk bs=1M count=512` | create 512MB file of zeros | LFS201 16.10
filesystem | `$ sudo /sbin/mkfs.xfs junk` | create xfs filesystem | LFS201 16.10
filesystem | `$ sudo mount junk /mnt` | mount filesystem | LFS201 16.10
filesystem | `$ lsmod | less` | shows mounted filesystems | LFS201 16.10
filesystem | `$ df -h` | display filesystem disk space usage in human-readable form | LFS201 16.10
partitions | `$ sudo fdisk -l /dev/sda` | list partition table | LFS201 17.5
partitions | `$ ls -l /dev` | show current available disk device nodes | LFS201 17.10
partitions | `$ sudo blkid /dev/sda*` | report on block devices matching the pattern| LFS201 17.12
partitions | `$ sudo blkid` | ?? | LFS201 17.12
partitions | `$ sudo blkid -L root` | convert LABEL to 'root'(?)| LFS201 17.12
partitions | `$ lsblk` | presents block device information in a tree format | LFS201 17.13
partitions | `$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1` | backup **MBR** on the first disk | LFS201 17.15
partitions | `$ sudo dd if=mbrbackup of=/dev/sda bs=512 count=1` | restore the **MBR** | LFS201 17.15
partitions | `x7:/tmp>sudo sgdisk --backup=/tmp/sda_backup /dev/sda` | backup GPT partition | LFS201 17.16
files, partitions | `x7:/tmp>sudo file sda_backup` or `$ file myfile.txt` | determines file type | LFS201 17.16
partitions| `$ sudo fdisk /dev/sdb` | start **fdisk** on `/dev/sdb` | LFS201 17.17
partitions | `$ sudo partprobe -s` | try and read in the revised partition table | LFS201 17.17
partitions | `$ cat /proc/partitions` | examine partitions the OS is aware of | LFS201 17.17
apps, files | `$ dd if=/dev/zero of=imagefile bs=1M count=1024` | create a file full of zeros 1 GB in length | LFS201 Lab 17.1
partitions | `$ mkfs.ext4 imagefile` | put a filesystem on 'imagefile' | LFS201 Lab 17.1
partitions | `$ mkdir mntpoint` | create directory for mounting a filesystem | LFS201 Lab 17.1
partitions | `$ sudo mount -o loop imagefile mntpoint` | mount filesystem 'imagefile' on `mntpoint` | LFS201 Lab 17.1
partitions | `$ sudo umount mntpoint` | unmount the filesystem | LFS201 Lab 17.1
partitions | `$ sudo fdisk -C 130 imagefile` | format and partition 'imagefile' w/ a phony number of cylinders | LFS201 Lab 17.2
partitions | `$ losetup -a` | see already in-use loop devices | LFS201 Lab 17.3
partitions | `$ sudo losetup -f` | finds the first free loop device | LFS201 Lab 17.3
partitions | `$ sudo losetup /dev/loop1 imagefile` | associate 'imagefile' with the loop device | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 mklabel msdos` | create a disk partition label on the loop device | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256` | create a primary partition from 0-256 MB | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512` | create a primary partition from 256-512 MB | LFS201 Lab 17.3
partitions | `$ sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024` | create a primary partition from 512-1024 MB | LFS201 Lab 17.3
partitions | `$ sudo fdisk -l /dev/loop1` | check the partition table | LFS201 Lab 17.3
partitions | `$ ls -l /dev/loop1*` | see device nodes on the loop device | LFS201 Lab 17.3
partitions | `$ sudo mkfs.ext3 /dev/loop1p1` | put ext3 filesystem on partition | LFS201 Lab 17.3
partitions | `$ sudo mkfs.ext4 /dev/loop1p2` | put ext4 filesystem on partition | LFS201 Lab 17.3
partitions | `$ sudo mkfs.vfat /dev/loop1p3` | put vfat filesystem on partition | LFS201 Lab 17.3
directories, partitions | `$ mkdir mnt1 mnt2 mnt3` | create three directories for three partitions | LFS201 Lab 17.3
partitions | `$ sudo mount /dev/loop1p1 mnt1` | mount first loop device to `mnt1` | LFS201 Lab 17.3
partitions | `$ sudo mount /dev/loop1p2 mnt2` | mount second loop device to `mnt2` | LFS201 Lab 17.3
partitions | `$ sudo mount /dev/loop1p3 mnt3` | mount third loop device to `mnt3` | LFS201 Lab 17.3
filesystem, partitions | `$ df -Th` | show availability of newly mounted filesystems (in this lab) | LFS201 Lab 17.3
partitions | `$ sudo umount mnt1 mnt2 mnt3` | unmount the three filesystems | LFS201 Lab 17.3
directories, partitions | `$ rmdir mnt1 mnt2 mnt3` | delete the mount points | LFS201 Lab 17.3
partitions | `$ sudo losetup -d /dev/loop1` | kill the loop device | LFS201 Lab 17.3
files | `$ chattr [+|-|=mode] filename` | change attributes for a file | LFS201 18.4
files | `$ lsattr <filename>` | display attributes for a file | LFS201 18.4
filesystem | `$ mkfs [-t fstype] [options] [device-file]` | generic **mkfs** syntax | LFS201 18.5
filesystem | `$ sudo mkfs -t ext4 /dev/sda10` | make an ext4 fs on `/dev/sda10` | LFS201 18.5
filesystem | `$ sudo mkfs.ext4 /dev/sda10` | make an ext4 fs on `/dev/sda10` | LFS201 18.5
filesystem | `$ fsck [-t fstype] [options] [device-file]` | generic **fsck** syntax | LFS201 18.6
filesystem | `$ sudo fsck -t ext4 /dev/sda10` | check ext4 filesystem on `/dev/sda10` | LFS201 18.6
filesystem | `$ sudo fsck.ext4 /dev/sda10` | check ext4 filesystem on `/dev/sda10` | LFS201 18.6
filesystem | `$ sudo fsck /dev/sda10`  | check filesystem on `/dev/sda10` | LFS201 18.6
filesystem | `$ sudo touch /forcefsck` then `$ sudo reboot` | force a check of mounted filesystems at boot, which allows **fsck** to check the root FS | LFS201 18.6
filesystem | `$ sudo mount -t ext /dev/sdb4 /home` | mount an ext4 filesystem on the `/dev/sdb4` partition at the position `/home` | LFS201 18.7
filesystem | `$ mount [options] <source> <directory>` | generic **mount** syntax | LFS201 18.10
filesystem | `$ sudo mount /dev/sda2 /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount LABEL=home /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount -L home /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount UUID=26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount -U 26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home` | mount a partition at `/home` | LFS201 18.10
filesystem | `$ sudo mount -o remount,ro /myfs` | remounts a FS with a read-only attribute | LFS201 18.11
filesystem | `$ mount -a` | mount all partitions in `/etc/fstab` | LFS201 18.11
filesystem | `$ umount [device-file | mount-point]` | generic umount syntax | LFS201 18.12
filesystem | `$ sudo umount /home` | unmount the `/home` filesystem | LFS201 18.12
filesystem | `$ sudo umount /dev/sda3` | unmount the `/dev/sda3` device | LFS201 18.12
filesystem | `$ fuser [options]` | show which users are using the FS | LFS201 18.12
filesystem | `$ lsof` | list open files, shows which files are being used | LFS201 18.12
filesystem | `$ sudo mount -t nfs myserver.com:/shdir /mnt/shdir` | mount **NFS** so system won't hang when unavailable(?) | LFS201 18.13
filesystem | `$ autofs` | service countrol for the **automount** daemon | LFS201 18.13
filesystem | `$ automount` | manages **autofs** mount points | LFS201 18.13
filesystem | `$ sudo mount /usr/src` | ?? | LFS201 18.14
filesystem | `$ sudo mount LABEL=src /usr/src` | ?? | LFS201 18.14
filesystem | `$ sudo systemctl daemon-reload` and `$ sudo systemctl restart local-fs.target` | restart after editing `/etc/fstab` | LFS201 18.15
filesystem | `$ mount` | show currently mounted filesystems | LFS201 18.17
filesystem | `$ dumpe2fs <source>` | show detailed information about filesystem | LFS201 18.18
files | `$ cat /etc/hosts > /tmp/appendit` | append contents of `/etc/hosts` to `/tmp/appendit` | LFS201 Lab 18.1
files | `$ diff /etc/hosts /tmp/appendit` | compare contents of the two files | LFS201 Lab 18.1
files | `$ sudo chattr -V -v 1 +a /tmp/appendit` | add append-only attribute to `/tmp/appendit` (verbose with version) | LFS201 Lab 18.1
files | `$ lsattr appendit` | show file's extended attributes | LFS201 Lab 18.1
files | `$ sudo chattr -V -v 3 +i appendit` | set the immutable attribute on `/tmp/appendit` | LFS201 Lab 18.1
files | `$ sudo chattr -V -v 4 -ai appendit` | remove extended attributes from `/tmp/appendit` | LFS201 Lab 18.1
filesystem, partitions | `$ sudo fdisk /dev/sda` | create a new partition on the system | LFS201 Lab 18.2
filesystem, partitions | `$ sudo partprobe -s` | inform operating system about partition table changes | LFS201 Lab 18.2
filesystem, partitions | `$ cat /proc/partitions` | shows partitions on the system | LFS201 Lab 18.2
filesystem | `$ sudo mkfs -t ext4 -v /dev/sda3` | create filesystem with block size of 1024 | LFS201 Lab 18.2
filesystem | `$ sudo mkfs -t ext4 -b 2048 -v /dev/sda3` | create filesytem with block size of 2048 | LFS201 Lab 18.2
filesystem | `$ sudo mkfs -t ext4 -b 4096 -v /dev/sda3` | create filesystem with block size of 4096 | LFS201 Lab 18.2
filesystem | `$ sudo mount /dev/sda3 /mnt/tempdir` | mount the partition | LFS201 Lab 18.2
filesystem | `$ df -Th` | view disk information **-T** for filesytem type, **-h** for human-readable | LFS201 Lab 18.2
filesystem | `$ mount | grep tempdir` | view mounted filesystems | LFS201 Lab 18.2
filesystem | `$ sudo umount /mnt/tempdir` | unmount the filesystem | LFS201 Lab 18.2
filesystem | `$ sudo mount -r /dev/sda3 /mnt/tmpdir` | mount the filesystem as read-only | LFS201 Lab 18.2
filesystem | `$ sudo mount -o ro /dev/sda3 /mnt/tempdir` | mount the filesystem as read-only | LFS201 Lab 18.2
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
filesystem | `$ cat /proc/swaps` | show information about current swap space | LFS201 Lab 19.1
filesystem | `$ dd if=/dev/zero of=swpfile bs=1M count=1024` | create file of 1GB file to use as a swap space | LFS201 Lab 19.1
filesystem | `$ mkswap swpfile` | use the swpfile for swap space | LFS201 Lab 19.1
filesystem | `$ sudo swapon swpfile` | activate the swap space | LFS201 Lab 19.1
filesystem | `$ sudo swapoff swpfile` | deactivate the swap space | LFS201 Lab 19.1
filesystem | `$ sudo rm swpfile` | remove the swap file used for swap space | LFS201 Lab 19.1
filesystem | `$ sudo quotacheck -u /mnt/tempdir` | initialize quotas on the /mnt/tempdir filesystem | LFS201 Lab 19.2
filesystem | `$ sudo quotaon -u /mnt/tempdir` | turn quota checking on | LFS201 Lab 19.2
filesystem | `$ sudo edquota -u student` | opens up the quota settings for this user; soft and hard quotas can be set by block or inode | LFS201 Lab 19.2
filesystem | `$ sudo dumpe2fs /dev/sda1 | grep superblock` | shows superblock locations | LFS201 20.9
filesystem | `$ dumpe2fs ...` | scan filesystem information | LFS201 20.11
filesystem | `$ tune2fs ...` | change filesystem parameters | LFS201 20.12
filesystem | `$ sudo tune2fs -c 25 /dev/sda1` | change the maximum number of mounts between filesystem checks (max-mount-count) | LFS201 20.12
filesystem | `$ sudo tune2fs -i 10 /dev/sda1` | change the time interval between checks (interval-between-checks) to 10 days | LFS201 20.12
filesystem | `$ sudo tune2fs -l /dev/sda1` | list the contents of the superblock, including the current values of parameters which can be changed | LFS201 20.12
filesystem | `$ sudo e4defrag TARGET` | generic syntax | LFS201 Lab 20.1
filesystem | `$ sudo e4defrag -c /var/log` | analyze and report on fragmentation of `/var/log` | LFS201 Lab 20.1
filesystem | `$ tune2fs -C 30 imagefile` | change `maximum mount count` to 30 | LFS201 Lab 20.2
filesystem | `$ tune2fs -i 3w imagefile` | change `Check interval` to three weeks | LFS201 Lab 20.2
filesystem | `$ tune2fs -m 10 imagefile` | change percentage of blocks reserved to 10% | LFS201 Lab 20.2
filesystem | `$ xfsdump ...` | backup **XFS** system | LFS201 21.5
filesystem | `$ xfsrestore ...` | restore **XFS** system | LFS201 21.5
filesystem | `$ xfs_quota` | can be used to set per-directory quotas | LFS201 21.5
filesystem | `$ xfs_freeze` | can be used to quiesce the filesystem to allow for a snapshot tool | LFS201 21.5
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
encryption | `$ sudo cryptsetup luksClose /dev/mapper/crypt-partition` | removes the association(?) | LFS201 22.9
unknown | `$ sudo losetup -d /dev/loop2` | terminates the loopback device | LFS201 22.9
unknown | `$ losetup -f` | only shows original loopback device | LFS201 22.9
files | `$ rm loop-partition` | removes file | LFS201 22.9
filesystem, partitions | `$ sudo mkswap /dev/sda2` | use `/dev/sda2` as a swap partition | LFS201 Lab 22.2
filesystem, partitions | `$ sudo swapon -a` | activate all swap partitions | LFS201 Lab 22.2
lvm | `$ vgcreate ...` | creates volume groups | LFS201 23.6
lvm | `$ vgextend ...` | adds to volume groups | LFS201 23.6
lvm | `$ vgreduce ...` | shrinks volume groups | LFS201 23.6
lvm | `$ pvcreate ...` | converts a partition to a physical volume | LFS201 23.6
lvm | `$ pvdisplay ...` | shows the physical volumes being used | LFS201 23.6
lvm | `$ pvmove ...` | moves the data from one physical volume within the volume group to others | LFS201 23.6
lvm | `$ pvremove ...` | remove a partition from a physical volume | LFS201 23.6
documentation, lvm | `$ man lvm` | show a full list of LVM utilities | LFS201 23.6
lvm | `$ ls -lF /sbin/lv*` | show logical volume utilities | LFS201 23.7
lvm | `$ lvcreate ...` | creates logical volumes from within volume groups | LFS201 23.8
lvm | `$ lvdisplay ...` | reports on available logical volumes | LFS201 23.8
lvm | `$ sudo pvcreate /dev/sdb1` | initializes a PV so that it is recognized as belonging to LVM | LFS201 23.8
lvm | `$ sudo pvcreate /dev/sdc1` | initializes a PV so that it is recognized as belonging to LVM | LFS201 23.8
lvm | `$ sudo vgcreate -s 16M vg /dev/sdb1` | creates a new VG on block devices | LFS201 23.8
lvm | `$ sudo vgextend vg /dev/sdc1` | adds one or more PVs to a VG | LFS201 23.8
lvm | `$ sudo lvcreate -L 50G -n mylvm vg` | creates a logical volume | LFS201 23.8
lvm | `$ pvdisplay /dev/sda5` | shows physical volumes; leave off physical volume name to list all | LFS201 23.9
lvm | `*$ vgdisplay /dev/vg0` | shows volume groups; leave off volume group name to list all | LFS201 23.9
lvm | `*$ lvdisplay /dev/vg0/lvm1` | shows logical volumes; leave off logical volume name to list all | LFS201 23.9
lvm | `$ sudo lvresive -r -L 20 GB /dev/VG/mylvm` | resizes the volume, where **-r** also resizes the filesystem | LFS201 23.10
lvm | 1 `$ sudo lvextend -L +500M /dev/vg/mylvm` | grow a logical volume with ext4 filesytem | LFS201 23.11
lvm | 2 `$ sudo resize2fs /dev/vg/mylvm` | grow a logical volume with ext4 filesytem | LFS201 23.11
lvm | 1 `$ sudo umount /mylvm` | shrink the filesystem | LFS201 23.11
lvm | 2 `$ sudo fsck -f /dev/vg/mylvm` | shrink the filesystem | LFS201 23.11
lvm | 3 `$ sudo resize2fs /dev/vg/mylvm 200M` | shrink the filesystem | LFS201 23.11
lvm | 4 `$ sudo lvreduce -L 200M /dev/vg/mylvm` | shrink the filesystem | LFS201 23.11
lvm | 5 `$ sudo mount /dev/vg/mylvm` | shrink the filesystem | LFS201 23.11
lvm | `$ sudo lvextend -r -L +100M /dev/vg/mylvm` | expand in one step (without **resize2fs** directly) | LFS201 23.11
lvm | `$ sudo lvreduce -r -L -100M /dev/vg/mylvm` | shrink in one step (without **resize2fs** directly) | LFS201 23.11
lvm | 1 `$ sudo pvmove /dev/sdc1` | reduce a volume group | LFS201 23.11
lvm | 2 `$ sudo vgreduce vg /dev/sdc1` | reduce a volume group | LFS201 23.11
lvm | 1 `$ sudo lvcreate -l 128 -s -n mysnap /dev/vg/mylvm` | create a snapshot of an existing logical volume | LFS201 23.12
lvm | 2 `$ mkdir /mysnap` | make a mount point | LFS201 23.12
lvm | 3 `$ mount -o ro /dev/vg/mysnap /mysnap` | mount the snapshot | LFS201 23.12
lvm | 4 `$ sudo umount /mysnap` | unmount the snapshot | LFS201 23.12
lvm | 5 `$ sudo lvremove /dev/vg/mysnap` | remove the snapshot | LFS201 23.12
raid | `$ sudo mdadm -S` | stop RAID | LFS201 24.6
raid | 1 `$ sudo fdisk /dev/sdb; sudo fdisk /dev/sdc` | create partitions | LFS201 24.6
raid | 2 `$ sudo mdadm --create /dev/md0 --level=1 --raid-disks=2 /dev/sdbX /dev/sdcX` or `$ mdadm --create /dev/md0 ... /dev/sdb1 /dev/sdc1` | set up array | LFS201 24.6
raid | 3 `$ sudo mkfs.ext4 /dev/md0` | format array | LFS201 24.6
raid | 4 `$ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"` | add array to configuration | LFS201 24.6
raid | 5 `$ sudo mkdir /myraid` | create RAID directory | LFS201 24.6
raid | 6 `$ sudo mount /dev/md0 /myraid` | mount RAID directory | LFS201 24.6
raid | 7 `$ sudo vim /etc/fstab` | add a line (`/dev/md0 /myraid ext4 defaults 0 2`) for the mount point | LFS201 24.6
raid | `$ cat /proc/mdstat` | check RAID status | LFS201 24.6
raid | `$ sudo mdadm -S /dev/md0` | stop RAID device | LFS201 24.6
raid | `$ sudo mdadm --detail /dev/md0` | monitor RAID device | LFS201 24.7
raid | `$ cat /pro/mdstat` | monitor RAID device | LFS201 24.7
raid | `$ sudo systemctl start mdmonitor` | start monitoring RAID device with **mdmonitor** | LFS201 24.7
raid | `$ sudo systemctl enable mdmonitor` | ensure **mdmonitor** starts at boot | LFS201 24.7
raid | `$ sudo mdadm --create /dev/md0 -l 5 -n3 -x 1 /dev/sda8 /dev/sda9 /dev/sda10 /dev/sda11` | create a hot spare when creating the RAID array (** -x 1** switch) | LFS201 24.8
raid | `$ sudo mdadm --fail /dev/md0 /dev/sdb2` | test redundancy and hot spare of the array | LFS201 24.8
raid | `$ sudo mdadm --remove /dev/md0 /dev/sdb2` | remove faulty drive in test or failure situation | LFS201 24.8
raid | `$ sudo mdadm --add /dev/md0 /dev/sde2` | add new drive in test or failure situation | LFS201 24.8
boot, kernel | `linux  /boot/vmlinuz-4.19.0 root=UUID=7ef4e747-afae-48e3-90b4-9be8be8d0258 ro quiet \ crashkernel=384M-:128M` | kernel command line | LFS201 25.6
boot, kernel | `linux16 /boot/vmlinuz-4.19.4 root=LABEL=RHEL7 LANG=en_US.UTF-8 ro rhgb quiet` | kernel command line | LFS201 25.6
boot, kernel | `$ cat /proc/cmdline` | see what command line a system was booted with | LFS201 25.6
boot, kernel | `$ man bootparam` | kernel boot parameter documentation | LFS201 25.7
boot, kernel | `$ sysctl -a` | shows current values | LFS201 25.8
boot, kernel | `$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'` | sets kernel runtime values | LFS201 25.8
boot, kernel | `$ sudo sysctl net.ipv4.ip_forward=1` | sets kernel runtime values with the **sysctl** interface | LFS201 25.8
boot, kernel | `$ man 8 sysctl` | get **sysctl** help | LFS201 25.8
boot, kernel | `$ man 2 sysctl` | get information about using the `sysctl()` function from programs | LFS201 25.8
boot, kernel | `$ man sysctl.conf` | for **sysctl.conf** help | LFS201 25.8
boot, kernel | `$ sudo sysctl -p` | causes immediate digestion of the file, setting all parameters as found; this is also part of the boot process | LFS201 25.8
apps, kernel | `$ sudo sysctl net.ipv4.icmp_echo_ignore_all=1` | cause system not to respond to pings | LFS201 Lab 25.1
kernel, processes | `# echo 27275 > /proc/sys/kernel/pid_max` | chance max PID as root | LFS201 Lab 25.2
kernel, modules | `$ lsmod` | list loaded modules | LFS201 26.6
kernel, modules | `$ sudo /sbin/rmmod <module_name>` | remove a module | LFS201 26.6
kernel, modules | `$ sudo /sbin/insmod <pathto>/<module_name.ko>` | load a module | LFS201 26.6
kernel, modules | `$ sudo /sbin/modprobe -r <module_name>` | unload a module | LFS201 26.7
system | `$ uname -r` | gives the current kernel version  | LFS201 26.7
system | `$ uname ...` | prints system information | LFS201 26.7
kernel, modules | `$ depmod ...` | generate or update the file `/lib/modules/$(uname -r)/modules.dep` | LFS201 26.7
kernel, modules | `$ /sbin/modinfo <my_module>` | find out about kernel modules | LFS201 26.9
kernel, modules | `$ /sbin/modinfo <pathto>/<my_module.ko>` | find out about kernel modules | LFS201 26.9
kernel, modules | `$ sudo /sbin/insmod <pathto>/e1000e.ko debug=2 copybreak=256` | load module specifying parameter values | LFS201 26.10
kernel, modules | `$ sudo /sbin/modprobe e1000e debug=2 copybreak=256` | load module specifying parameter values when the module is in the proper system location | LFS201 26.10
devices | `$ sudo mknod [-m mode] /dev/name <type> <major> <minor>` | generic syntax to create a device node | LFS201 27.4
devices | `$ sudo mknod -m 666 /dev/mycdrv c 254 1` | creating a device node example | LFS201 27.4
devices | `$ ls -l /dev/sda*` | view major and minor numbers for sda* devices | LFS201 27.5
devices | `$ stat /dev/sda1` | returns device information for `/dev/sda1` | LFS201 27.5
devices | `$ cat /usr/lib/udev/rules.d/99-fitbit.rules` | read rules for a Fitbit device, for example | LFS201 27.12
virtualization | `$ ls -lF /usr/bin/virt*` | see which utilities are using *libvirt* (?) | LFS201 28.14
virtualization | `$ qemu-img --help | grep formats:` | get list of supported formats | LFS201 28.17
virtualization | `$ qemu-img convert -O vmdk myvm.qcow2 myvm.vmdk` | translate between disk image file formats | LFS201 28.17
virtualization | `$ sudo systemctl start libvirtd` | start **libvirtd** | LFS201 Lab 28.2
virtualization | `$ sudo virt-manager` | start **virt-manager** GUI | LFS201 Lab 28.2
containers, docker | `$ sudo systemctl start docker` |  start **docker** service | LFS201 Lab 29.1
containers, docker | `$ systemctl status docker` | verify **docker** is running properly | LFS201 Lab 29.1
containers, docker | `$ docker search apache` | search for the **httpd** container | LFS201 Lab 29.1
containers, docker | `$ sudo docker pull docker.io/httpd` | retrieve the container | LFS201 Lab 29.1
containers, docker | `$ sudo docker images` | list the installed containers | LFS201 Lab 29.1
containers, docker | `$ sudo docker images --all` | list all components associate with **docker** images | LFS201 Lab 29.1
containers, docker | `$ sudo docker run httpd` | start the **httpd docker** container | LFS201 Lab 29.1
containers, docker | `$ sudo docker ps` | list running **docker** containers | LFS201 Lab 29.1
containers, docker | `$ sudo docker kill 27d8ec644776` | kill **docker** container | LFS201 Lab 29.1
containers, docker | `$ sudo docker stop b936b0afeb23` | stop **docker** container | LFS201 Lab 29.1
containers, docker | `$ sudo docker rmi -f docker.io/httpd` | remove **httpd** image | LFS201 Lab 29.1
containers, docker | `$ sudo systemctl stop docker` | stop **docker** service | LFS201 Lab 29.1
accounts | `$ sudo useradd dexter` | create an account for user **dexter** | LFS201 30.6
accounts | `$ sudo useradd -s /bin/csh -m -k /etc/skel -c "Bullwinkle J Moose" bmoose` | create a user account and use options to overrule defaults | LFS201 30.6
accounts | `$ sudo userdel morgan` | delete the user **morgan** | LFS201 30.7
accounts | `$ sudo usermod -L dexter` | lock **dexter**'s account | LFS201 30.7
accounts | `$ sudo chage -E 2014-09-11 morgan` | lock **morgan**'s account by changing the expiration date to a date in the past | LFS201 30.8
accounts | `$ passwd ...` | used to change passwords | LFS201 30.8
accounts | `$ vipw ...` | used to change account information | LFS201 30.8
accounts | `$ sudo usermod -U dexter` | unlocks **dexter**'s account | LFS201 30.8
accounts | `$ chage [-m mindays] [-M maxdays] [-d lastday] [-I inactive] [-E expiredate] [-W warndays] user` | generic syntax for chage | LFS201 30.13
accounts | `$ sudo chage -l dexter` | list account aging information for dexter | LFS201 30.13
accounts | `$ sudo chage -m 14 -M 30 kevlin` | set the mindays and maxdays, respectively, for kevlin | LFS201 30.13
accounts | `$ sudo chage -E 2012-4-1 morgan` | set the password expiration for morgan to 2012-4-1 | LFS201 30.13
accounts | `$ sudo chage -d 0 USERNAME` | force a user to change their password at next login | LFS201 30.13
accounts | `$ chage -l MY_USERNAME` | list accoung aging information for myself | LFS201 30.13
accounts | `$ bash -r` | invokes a **restricted shell** (some distros use **rbash**) | LFS201 30.14
accounts | 1 `$ cd /bin ; sudo ln -s bash rbash` | set up restricted account; create symbolic link from bash to rbash | LFS201 30.15
accounts | 2 `$ cd /bin ; sudo ln bash rbash` | set up restricted account; create a link from bash to rbash | LFS201 30.15
accounts | 3 `$ cd /bin ; sudo cp bash rbash` | set up restricted account; copies bash to rbash?? | LFS201 30.15
accounts, system | `$ auditd ...` | can be used to log all commands executed as root | LFS201 30.16  
accounts, network | `$ ssh farflung.com` | login to site assuming an existing user account | LFS201 30.17
accounts, network | `$ ssh root@farflung.com` | login to site as root | LFS201 30.17
accounts, network | `$ ssh -l root farflung.com` | login to site as root | LFS201 30.17
accounts, network | `$ scp file.txt farflung.com:/tmp` | copy file from current dir to remote one | LFS201 30.17
accounts, network | `$ scp file.txt student@farflung.com/home/student` | copy file from current dir to remote home dir | LFS201 30.17
accounts, network | `$ scp -r some_dir farflung.com:/tmp/some_dir` | copy local dir to remote dir | LFS201 30.17
accounts, network | `$ for machines in node1 node2 node3 ; do (ssh $machines some_command &) ; done` | run a command on multiple machines simultaneously | LFS201 30.17
accounts, network | `$ ssh-keygen` | create private and public encryption keys | LFS201 30.18
accounts, apps | `$ sudo grep student /etc/passwd /etc/shadow` | grep two files for 'student' | LFS201 Lab 30.1
network, system | `$ sudo service sshd restart` | start **sshd** service | LFS201 Lab 30.1
network, system | `$ sudo systemctl restart sshd.service` | start **sshd** service | LFS201 Lab 30.1
accounts | `$ sudo groupadd -r -g 215 staff` | creates a system group (**-r**) and assigns it the GID "215" and name "staff" (?) | LFS201 31.5
accounts | `$ sudo groupmod -g 101  blah` | changes the group ID of "blah" (?) | LFS201 31.5
accounts | `$ sudo groupdel newgroup` | deletes the group "newgroup" | LFS201 31.5
accounts | `$ sudo usermod -G student,group1,group2 student` | assign "student" to the comma-separated list of groups | LFS201 31.5
accounts | `$ vigr ...` | make changes to `/etc/group` | LFS201 31.5
accounts | `$ useradd -G <group name>` | adds a user to an existing group when creating the user | LFS201 31.8
accounts | `$ groups [USER1 USER2 ...]` | identify group membership for users | LFS201 31.7
accounts | `$ id -Gn [USER1 USER2 ...]` | identify group membership for users | LFS201 31.7
accounts | `$ groups` or `$ id -Gn` | shows current user group membership | LFS201 31.7
accounts | `$ id` | shows current user UID, GID, and groups | LFS201 31.7
accounts, files, permissions | `$ chmod ...` | changes file permissions | LFS201 32.7
accounts, files, permissions | `$ chomod uo+x,g-w a_file` | adds execution access for user and other and removes write access for group; symbolic form | LFS201 32.7
accounts, files, permissions | `$ chmod 755 a_file` | gives all access to the user and read/execute to groups and other; octal form | LFS201 32.8
accounts, files, permissions | `$ chown ...` | changes file ownership; only the superuser can do this | LFS201 32.9
accounts, files, permissions | `$ chown wally somefile` | makes wally the owner of the file | LFS201 32.9
accounts, files, permissions | `$ chgrp ...` | changes group ownership; you can only change group ownership to groups you are a member of | LFS201 32.9
accounts, files, permissions | `$ chgrp cleavers somefile` | makes cleavers the group owner of the file | LFS201 32.9
accounts, files, permissions | `$ chown wally:cleavers somefile` | changes both owner and group at the same time | LFS201 32.9
accounts, files, permissions | `$ chown -R wally:cleavers ./` | changes owner and group recursively | LFS201 32.9
accounts, files, permissions | `$ chown -R wally:wally subdir` | changes owner and group recursively | LFS201 32.9
accounts, files, permissions | `$ umask` | shows which permissions should be denied (0002 on my machine) | LFS201 32.10
accounts, files, permissions | `$ umask 0022` | changes the umask value | LFS201 32.10
accounts, files, permissions | `$ getfacl <file|directory>` | see ACLs | LFS201 32.13
accounts, files, permissions | `$ getfacl test` | see ACLs for test file | LFS201 32.13
accounts, files, permissions | `$ setfacl <options> <permissions> <file|directory>` | sets ACLs | LFS201 32.13
accounts, files, permissions | `$ setfacl -m u:isabelle:rx /home/stephane/file1` | modifies ACL of a file or directory | LFS201 32.13
accounts, files, permissions | `$ setfacl -x u:isabelle /home/stephane/file` | removes ACL entries | LFS201 32.13
accounts, files, permissions | `$ setfacl -m d:u:isabelle:rx somedir` | sets default on a directory | LFS201 32.13
accounts, auth | **libpam** | checks for files in `/etc/pam.d` after being called by an app using PAM | LFS201 33.5
accounts, auth | **system-auth** | involved in authorization | LFS201 33.5
accounts, auth | **LDAP** | enables distributed directory services | LFS201 33.8
accounts, auth | **system-config-authentication** | utility involved in PAM and/or LDAP | LFS201 33.8
accounts, auth | **authconfig-tui** | utility involved in PAM and/or LDAP | LFS201 33.8
accounts, auth | **openldap-clients** | involved in configuring LDAP with PAM | LFS201 33.8
accounts, auth | **pam ldap** | involved in configuring LDAP with PAM | LFS201 33.8
accounts, auth | **nss-pam-ldapd** | involved in configuring LDAP with PAM | LFS201 33.8
accounts, auth | `$ sudo pam_tally2 -u rocky` | see how many failed logins there are for user rocky | LFS201 Lab 33.1
accounts, auth | `$ sudo pam_tally2 -u rocky -r` | reset the failed login counter for user rocky | LFS201 Lab 33.1
networks, security | `$ firewall-cmd --help` | has lots of information | LFS201 36.9
networks, security | `$ sudo systemctl [enable/disable] firewalld` | start **firewalld** | LFS201 36.10
networks, security | `$ sudo systemctl [start/stop] firewalld` | stop **firewalld** | LFS201 36.10
networks, security | `$ sudo systemctl status firewalld` | shows the current state of **firewalld** | LFS201 36.10
networks, security | `$ sudo firewall-cmd --state` | shows the current state of **firewalld** | LFS201 36.10
networks, security | `$ sudo sysctl net.ipv4.ip_forward=1` | turn on **ip forwarding** | LFS201 36.10
networks, security | `$ echo 1 > /proc/sys/net/ipv4/ip_forward` | turn on **ip forwarding** | LFS201 36.10
networks, security | 1 `/etc/sysctl.conf` | add **net.ipv4.ip_forward=1** to make **ip forwarding** persistent | LFS201 36.10
networks, security | 2 `$ sudo sysctl -p` | to read in the new setting without rebooting (or just reboot) | LFS201 36.10
networks, security | `$ sudo firewall-cmd --get-default-zone` | get the default zone | LFS201 36.12
networks, security | `$ sudo firewall-cmd --get-active-zones` | obtain a list of zones currently being used | LFS201 36.12
networks, security | `$ sudo firewall-cmd --get-zones` | list all available zones | LFS201 36.12
networks, security | `$ sudo firewall-cmd --set-default-zone=trusted` | change the default zone to **trusted** | LFS201 36.12
networks, security | `$ sudo firewall-cmd --set-default-zone=public` | change the default zone back to **public** | LFS201 36.12
networks, security | `$ sudo firewall-cmd --zone=internal --change-interface=eno1` | assign an interface temporarily to a particular zone | LFS201 36.12
networks, security | `$ sudo firewall-cmd --permanent --zone=internal --change-interface=eno1` | assign an interface to a particular zone permanently, which creates `/etc/firewalld/zones/internal.xml` | LFS201 36.12
networks, security | `$ sudo firewall-cmd --get-zone-of-interface=eno1` | ascertain the zone associated with a particular interface | LFS201 36.12
networks, security | `$ sudo firewall-cmd --zone=public --list-all` | get all details about a particular zone public (default, active) | LFS201 36.12
networks, security | `$ sudo firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0/24` | assign anyone with an IP address of 192.168.1.x to the **trusted** zone | LFS201 36.13
networks, security | `$ sudo firewall-cmd --permanent --zone=trusted --list-sources` | list the sources bound to a zone | LFS`201 36.13
networks, security | `$ sudo firewall-cmd --get-services` | see all the services available | LFS201 36.14
networks, security | `$ sudo firewall-cmd --list-services --zone=public` | see services currently accessible in a particular zon`e | LFS201 36.14
networks, security | `$ sudo firewall-cmd --permanent --zone=home --add-service=dhcp` | add a service to a zone | LFS201 36.14
networks, security | `$ sudo firewall-cmd --reload` | reload firewall rules and keep state information. Current permanent con`figuration will become new runtime configuration | LFS201 36.14
networks, security | `$ sudo firewall-cmd --zone=home --add-port=21/tcp` | add TCP port to home zone services | LFS201 36.15
networks, security | `$ sudo firewall-cmd --zone=home --list-ports` | check ports for home zone | LFS201 36.15
networks, security | `$ grep " 21/tcp" /etc/services` | ascertain that port 21 corresponds to ftp | LFS201 36.15
networks, security | `$ firewall-config` | start firewall GUI | LFS201 Lab 36.4
startup/shutdown | `$ update-grub ...` or `$ grub2-mkconfig ...` | modify `grub.cfg` | LFS201 38.4
startup/shutdown | `$ sudo grub2-install /dev/sda` | install GRUB for version 2 (read the **man** first!!) | LFS201 38.6
startup/shutdown | `$ sudo systemctl start gdm` | start graphical user interface (GUI) from command line | LFS201 Lab 38.1
