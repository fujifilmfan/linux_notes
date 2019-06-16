Packages and Libraries
----------------------
* determine what package (if any) any file on the system is part of
* determine what version is installed
* install and uninstall (erase) packages without leaving debris behind
* verify that a package was installed correctly; this is useful for both troubleshooting and system auditing
* Use ldd to find what shared libraries an executable (like one in /usr/bin/) requires
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
* to verify all packages on system:
    * `$ rpm -Va`
* if you specify one or more package names as arguments, you examine only that package:
    * no output when everything is okay: 
  `$ rpm -V bash`  
    * output indicating that a file's size, checksum, and modification time have changed:
  `$ rpm -V talk`  
  > S.5....T in.ntalkd.8  
    * output indicating that a file is missing:
  `$ rpm -V talk`  
  > missing /usr/bin/talk 
Installing a package is as simple as:  
`$ sudo rpm -ivh foo-1.0.0-1.noarch.rpm`  where the **-i** is for install, **-v** is for verbose, and **-h** just means print out hash marks while doing to show progress.
### 6.12: Uninstalling Packages
----
* a successful uninstall produces no output
* errors occur when the package isn't installed or is required by other packages
`$ sudo rpm -e system-config-lvm` where **-e** is for erase and 'system-config-lvm' is the package name, not rpm file name
`$ sudo rpm -e --test -vv system-config-lvm` where **--test** will tell you whether the install would succeed or fail w/o actually doing it and **-vv** gives more information
* upgrading replaces the original package if installed:
`$ sudo rpm -Uvh bash-4.2.45-5.el7_0.4.x86_64.rpm` # **-U** for Upgrade
### 6.15: Freshening Packages
----
`$ sudo rpm -Fvh *.rpm`# **-F** for Freshen
To install a new kernel do:  
`$ sudo rpm -ivh kernel-{version}.{arch}.rpm` filling in the correct version and architecture names.  
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


### Ch.6
* Lab 6.1
    1. Find out what package the file /etc/logrotate.conf belongs to.
    ```
    $ rpm -qf /etc/logrotate.conf
    logrotate-3.8.6-4.el7.x86_64
    ```
    2. List information about the package including all the files it contains.
    ```
    $ rpm -qil logrotate ...
    Note a fancier form that combines these two steps would be:
    $ rpm -qil $(rpm -qf /etc/logrotate.conf)
    ```
    3. Verify the package installation.
    ```
    $ rpm -V logrotate
    ..?......    /etc/cron.daily/logrotate
    S.5....T.  c /etc/logrotate.conf
    ```
    4. Try to remove the package.
    ```
    $ sudo rpm -e logrotate
    error: Failed dependencies:
    logrotate is needed by (installed) vsftpd-3.0.2-9.el7.x86_64
    logrotate >= 3.5.2 is needed by (installed) rsyslog-7.4.7-7.el7_0.x86_64
    ```



repository file * Here's an example:  
`$ cat (/etc/yum.repos.d/?)CentOS-Base.repo`
queries:
* Search for packages with keyword in name:  
`$ sudo yum search keyword` # tells about packages
`$ sudo yum list "*keyword*"` # tells what is installed and what else is available  
* Display information about a package:  
`$ sudo yum info keyword`  
* List all packages, or just those installed, available, or updates that have not yet been installed:  
`$ sudo yum list [installed | updates | available ]`  
* Show information about package groups installed or available, etc.:  
`$ sudo yum grouplist`  
`$ sudo yum groupinfo group1` like `$ sudo yum groupinfo "Development Tools"`  
* Show packages that contain a certain file name:  
`$ sudo yum provides` as in `$ sudo yum provides "/logrotate.conf"`  
* Package verification requires installation of the **yum-plugin-verify** package (a yum plugin, not an executable - plugins extend the possible set of commands and arguments yum can take); to install:
`$ sudo yum install yum-plugin-verify`  
* To verify a package, giving the most information:
`$ sudo yum verify [package]`  
* To mimic **rpm -V** exactly:
`$ sudo yum verify-rpm [package]`  
* To list all differences, including configuration files:
`$ sudo yum verify-all [package]`  
* Without arguments, the above commands will verify all packages installed on the system.
* By default, the verification commands ignore configuration files which may change through normal and safe usage. There are some other options: see man yum-verify.
  
### 8.10: Installing/Removing/Upgrading Packages
----
* Install one or more packages from repositories, resolving and installing any necessary dependencies:  
`$ sudo yum install package1 [package2]`  
* Install from a local rpm:  
`$ sudo yum localinstall package-file`; this will attempt to resolve dependencies by accessing remote repositories unlike `$ rpm -i package-file`  
* Install a specific software group from a repository, resolving and installing any necessary dependencies for each package in the group:  
`$ sudo yum groupinstall group-name` or `$ sudo yum install @group-name`  
* Remove packages from the system:  
`$ sudo yum remove package1 [package2]`  
    * One must be careful with package removal, as yum will not only remove requested packages, but all packages that depend on them! This may not be what you want, so never run yum remove with the -y option, which assumes automatic confirmation of removal.  
* Update a package from a repository:  
`$ sudo yum update [package]`  
    * If no package name is given, all packages are updated.  
* During installation (or update), if a package has a configuration file which is updated, it will rename the old configuration file with an **.rpmsave** extension. If the old configuration file will still work with the new software, it will name the new configuration file with an **.rpmnew** extension. You can search for these filename extensions (almost always in the /etc subdirectory tree) to see if you need to do any reconciliation, by doing:  
`$ sudo find /etc -name "*.rpm*"`  
  
### 8.11: Additional Commands
----
* List plugins:  
    `$ sudo yum list "yum-plugin*"`  
* Show a list of all enabled repositories:  
    `$ sudo yum repolist`  
* Initiate an interactive shell in which to run multiple YUM commands:  
    `$ sudo yum shell [text-file]`  
    * If **text-file** is given, yum will read and execute commands from that file instead of from the terminal.
* Download packages, but do not install them; just store them under the /var/cache/yum directory, or another directory you can specify:  
    `$ sudo yum install --downloadonly package`  
    * or you can type "d" instead of "y" or "n" when prompted after issuing an install command. The package(s) will be downloaded under `/var/cache/yum` in a location depending on the repository from which the download proceeds, unless the **--downloaddir=** option is used. Any other necessary packages will also be downloaded to satisfy dependencies.  
* You can view the history of yum commands, and, with the correct options, even undo or redo previous commands:  
    `$ sudo yum history`  
  




### Ch.8
* Lab 8.1
    1. Check to see if there are any available updates for your system.
    ```
    $ sudo yum update
    $ sudo yum check-update $ sudo yum list updates
    Only the first form will try to do the installations.
    ```
    2. Update a particular package.
    ```
    $ sudo yum update bash
    ```
    3. List all installed kernel-related packages, and list all installed or available ones.
    ```
    sudo yum list installed "kernel*"
    $ sudo yum list "kernel*"
    ```
    4. Install the httpd-devel package, or anything else you might not have installed yet. Doing a simple:
    $ sudo yum list
    will let you see a complete list; you may want to give a wildcard argument to narrow the list.
    ```
    $ sudo yum install httpd-devel
    ```
* Lab 8.2
    1. All packages that contain a reference to bash in their name or description.
    ```
    $ sudo yum search bash
    ```
    2. Installed and available bash packages.
    ```
    $ sudo yum list bash
    `$ sudo yum list installed "bash*"`  
    `$ sudo yum list installed "*bash*"`  
    `$ sudo yum list available "bash*"`  
    `$ sudo yum list available "*bash*"` 
    ```
    3. The package information for bash.
    ```
    $ sudo yum info bash
    ```
    4. The dependencies for the bash package.
    ```
    $ sudo yum deplist bash
    ```
* Lab 8.3
    1. Use the following command to list all package groups available on your system:
    > $ yum grouplist
    2. Identify the Backup Client group and generate the information about this group using the command $ yum groupinfo "Backup Client"
    3. Install using:
    > $ sudo yum groupinstall "Backup Client"
    4. Identify a package group that’s currently installed on your system and that you don’t need. Remove it using yum groupremove as in:
    > $ sudo yum groupremove "Backup Client"
    Note you will be prompted to confirm removal so you can safely type the command to see how it works.
    You may find that the groupremove does not remove everything that was installed; whether this is a bug or a feature can be discussed.

### Extras
* Lab 6.2: rebuild RPM database
* Lab 8.4: add a new yum repository

