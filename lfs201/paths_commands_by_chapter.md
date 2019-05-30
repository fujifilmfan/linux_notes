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
