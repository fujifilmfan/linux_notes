Packages and Libraries
-----

### Chapters: LFS201: (5), 6, 8

* use ldd to find what shared libraries an executable (like one in ls or xz) requires - `$ ldd /usr/bin/ls /usr/bin/xz`

### rpm - Queries
* rpm - find which version of bash and tar are installed - `$ rpm -q bash`
* rpm - find which package /bin/bash and /usr/bin/tar are from - `$ rpm -qf /bin/bash` # **-f** for file
* rpm - find what files were installed by bash and tar - `$ rpm -ql bash`
* rpm - show information about bash and tar - `$ rpm -qi bash`
* rpm - show both information and files for a package - `$ rpm -qil ...`
* rpm - list all installed packages on this system - `$ rpm -qa`
* rpm - show what installed package provides bash or tar or bzip2 - `$ rpm -q --whatprovides bash`
* rpm - show what installed package provides `/etc/logrotate.conf` - `$ rpm -qf /etc/logrotate.conf`
* rpm - show what packages require bash or tar or bzip2 - `$ rpm -q --whatrequires bash`
* rpm - show what packages bash or tar or bzip2 require - `$ rpm -q --requires bash`
* rpm - verify the bash package - `$ rpm -V bash` # no output when everything is okay
* rpm - find out what package `/etc/logrotate.conf` belongs to and list information about the package - `$ rpm -qil $(rpm -qf /etc/logrotate.conf)`
* rpm - try to remove the package - `$ sudo rpm -e logrotate`

### rpm - Uninstalling packages
* rpm - a successful uninstall produces no output
* rpm - errors occur when the package isn't installed or is required by other packages
* rpm - remove package using a test run - `$ sudo rpm -e --test -vv system-config-lvm`

### rpm - Freshening packages
* rpm - freshen a package - `$ sudo rpm -Fvh *.rpm`
* rpm - create a cpio archive of a downloaded rpm - `$ rpm2cpio foobar.rpm > foobar.cpio`
* rpm - list files in a downloaded rpm - `$  rpm -qilp foobar.rpm`
* rpm - extract files from an rpm without installing the package - `$ rpm2cpio foobar.rpm | cpio --extract --make-directories`

repository file * Here's an example:  
`$ cat (/etc/yum.repos.d/?)CentOS-Base.repo`

### Installing/removing/upgrading packages
* yum - download the dump package to a folder of your choice, but do not install it - `$ sudo yum install --downloadonly <package> --downloaddir=<my_dir>` 
* rpm - show information about dump from the package (rpm) file, not the package database - `$ rpm -qip dump...` 
* rpm - return a list of prerequisites for dump - `$ rpm -qp --requires foo-1.0.0-1.noarch.rpm`
* rpm - install dump - `$ sudo rpm -ivh foo-1.0.0-1.noarch.rpm`  # **-i** is for install, **-v** is for verbose, and **-h** just means print out hash marks while doing to show progress
* rpm - uninstall dump - `$ sudo rpm -e dump` # **-e** is for erase and 'dump' is the package name, not rpm file name
* rpm - upgrade and install a package - `$ sudo rpm -Uvh bash-4.2.45-5.el7_0.4.x86_64.rpm`
* rpm - uninstall dump - `$ sudo rpm -e dump` # **-e** is for erase and 'dump' is the package name, not rpm file name
* yum - install a locally downloaded package and resolve dependencies - `$ sudo yum localinstall <package-file>`
* yum - remove dump - `$ sudo yum remove dump`

### yum
* yum - search for package and see a description - `$ sudo yum search <keyword>`
* yum - show packages that contain the file `bin/ls` - `$ sudo yum provides *bin/ls` or `$ sudo yum whatprovides *bin/ls`
* yum - install the yum-plugin-verify package - `$ sudo yum install yum-plugin-verify`  
* yum - verify a package, giving the most information - `$ sudo yum verify [package]`  
* yum - verify a package, mimicing **rpm -V** exactly - `$ sudo yum verify-rpm [package]`  
* yum - list all differences between packages, including configuration files - `$ sudo yum verify-all [package]`  
* yum - remove the yum-plugin-verify package from the system - `$ sudo yum remove yum-plugin-verify` 
* yum - list plugins - `$ sudo yum list "yum-plugin*"`  
* yum - show a list of all enabled repositories - `$ sudo yum repolist`  
* yum - view the history of yum commands - `$ sudo yum history`
* (`$ sudo yum list [installed | updates | available ]`  )
* yum - check to see if there are any available updates for your system without doing the installations - `$ sudo yum check-update` or `$ sudo yum list updates`
* yum - one of the packages from the above list - `$ sudo yum update bash`
* yum - find all packages that contain a reference to kernel in their name or description - `$ sudo yum search kernel`
* yum - list installed or available kernel-related packages - `$ sudo yum list "kernel*"`
* yum - list installed kernel-related packages - `$ sudo yum list installed "kernel*"`
* yum - list available kernel-related packages - `$ sudo yum list available "kernel*"`
* yum - install the httpd-devel package, or anything else you might not have installed yet - `$ sudo yum list httpd*` or `$ sudo yum install httpd-devel`
* yum - display the package information for bash - `$ sudo yum info bash`
* yum - find the dependencies for the bash package - `$ sudo yum deplist bash`
* yum - list all package groups available on your system - `$ yum grouplist`
* yum - identify the Backup Client group and generate the information about this group - `$ yum groupinfo "Backup Client"`
* yum - install the Backup Client group, resolving and installing any necessary dependencies - `$ sudo yum groupinstall "Backup Client"` or `$ sudo yum install @backup-client`
* yum - remove the Backup Client group using yum groupremove - `$ sudo yum groupremove "Backup Client"`

### Extras
* rpm - install a new kernel - `$ sudo rpm -ivh kernel-{version}.{arch}.rpm`
* rpm - verify all packages on system - `$ rpm -Va` # takes a loooong time
* find package config files with the **.rpmsave** and **.rpmnew** extensions - `$ sudo find /etc -name "*.rpm*"`  
* Lab 6.2: rebuild RPM database
* install one or more packages from repositories, resolving and installing any necessary dependencies - `$ sudo yum install package1 [package2]`  (I already know this)
* yum - verify all packages on the system - `$ sudo yum verify`
* yum - check to see if there are any available updates for your system and do the installations - `$ sudo yum update`

* `$ sudo yum shell [text-file]` (initiate interactive shell)
* Lab 8.4: add a new yum repository

