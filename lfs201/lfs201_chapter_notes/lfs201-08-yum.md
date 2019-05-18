Chapter 8: yum
--------------


[Paths and Commands](#paths-and-commands)  
  
### .3: Learning Objectives
----
By the end of this chapter, you should be able to:
* 


Chapter 8: yum
-----
### Introduction 8
The **yum** program provides a higher level of intelligent services for using the underlying **rpm** program. It can automatically resolve dependencies when installing, updating and removing packages. It accesses external software **repositories**, synchronizing with them and retrieving and installing software as needed.

### Goals 8
By the end of this chapter, you should be able to: 
* Discuss package installers and their characteristics.
* Explain what **yum** is.
* Configure **yum** to use repositories.
* Discuss the queries **yum** can be used for.
* Verify, install, remove, and upgrade packages using **yum**.

### Notes 8

#### 8.3 Package Installers
The higher-level package management systems:
* Can use both local and remote **repositories** as a source to install and update binary as well as source software packages.
* Are used to automate the install, upgrade, and removal of software packages.
* Resolve dependencies automatically.
* Save time because there is no need to either download packages manually or search out dependency information separately.

#### 8.4 What Is yum?
* yum is used by the majority of distributions that use **rpm**, including **RHEL**, **CentOS**, **Scientific Linux**, and **Fedora**
* yum caches info and databases to speed performance; to remove cached information:
`$ yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]`
* yum has a number of modular extensions (plugins) and companion programs that can be found under `/usr/bin/yum*` and `/usr/sbin/yum*`

#### 8.5 Configuring yum to Use Repositories
Repository configuration files are kept in `/etc/yum.repos.d/` and have a **.repo** extension.

#### 8.6 Repository Files
* A very simple repository file might look like:  
**[repo-name]**  
**name=Description of the repository**  
**baseurl=http://somesystem.com/path/to/repo**  
**enabled=1**  
**gpgcheck=1**  
* Here's an example:  
`$ cat CentOS-Base.repo`
> [base]
> name=CentOS-$releasever - Base
> mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
> #baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
> gpgcheck=1
> gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

One can toggle the use of a particular repository on or off by changing the value of enabled to 0 or 1, or using the **--disablerepo=somerepo** and **--enablerepo=somerepo** options when using **yum**. One can (but should not) also turn off integrity checking with the **gpgcheck** variable.

#### 8.7 Queries
* Search for packages with keyword in name:  
`$ sudo yum search keyword` # tells about packages
> Loaded plugins: fastestmirror, langpacks  
> Loading mirror speeds from cached hostfile  
>  * base: repos.lax.quadranet.com  
>  * epel: mirrors.develooper.com  
>  * extras: mirror.sjc02.svwh.net  
>  * updates: centos.sonn.com  
> ============================================= N/S matched: keyword ==============================================  
> perl-B-Keywords.noarch : Lists of reserved barewords and symbol names  
> perl-Devel-Declare.x86_64 : Adding keywords to perl, in perl  
> rednotebook.noarch : Daily journal with calendar, templates and keyword searching  
>   
>   Name and summary matches only, use "search all" for everything.  
>   
`$ sudo yum list "*keyword*"` # tells what is installed and what else is available  
> Loaded plugins: fastestmirror, langpacks  
> Loading mirror speeds from cached hostfile  
>  * base: repos.lax.quadranet.com  
>  * epel: mirrors.develooper.com  
>  * extras: mirror.sjc02.svwh.net  
>  * updates: centos.sonn.com  
> Available Packages  
> perl-B-Keywords.noarch  
* Display information about a package:  
`$ sudo yum info keyword`  
> Loaded plugins: fastestmirror, langpacks  
> Loading mirror speeds from cached hostfile  
>  * base: repos.lax.quadranet.com  
>  * epel: mirrors.develooper.com  
>  * extras: mirror.sjc02.svwh.net  
>  * updates: centos.sonn.com  
> Error: No matching Packages to list  
Information includes size, version, what repository it came from, a source URL, and a longer description. Wildcards can be given, as in **yum info "libc\*"**, for this and most **yum** commands. Note that the package need not be installed, unlike queries made with **rpm -q**.  
* List all packages, or just those installed, available, or updates that have not yet been installed:  
`$ sudo yum list [installed | updates | available ]`  
* Show information about package groups installed or available, etc.:  
`$ sudo yum grouplist`  
`$ sudo yum groupinfo group1` like `$ sudo yum groupinfo "Development Tools"`  
* Show packages that contain a certain file name:  
`$ sudo yum provides` as in `$ sudo yum provides "/logrotate.conf"`  

#### 8.8 Verifying Pacakges
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

#### 8.9 Installing/Removing/Upgrading Packages
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

#### 8.10 Additional Commands
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

#### 8.11 dnf
* **dnf** is intended to be a next generation replacement for **yum**
* not widely adopted
* [Part II. Package Management](https://docs-old.fedoraproject.org/en-US/Fedora/24/html/System_Administrators_Guide/part-Package_Management.html)

#### 8.12 Using the yum and dnf Utilities Demo
* `$ yumex` for "Yum Extender" (yumex-dnf on Fedora), launches graphical yum interface
* EPEL: Extra Packages for Enterprise Linux

### Labs 8

#### Lab 8.1 Basic YUM Commands
1. Check to see if there are any available updates for your system.
* My solution:  
`$ sudo yum list updates`  
* Lab solution (in addition to above):  
`$ sudo yum check-update`  

2. Update a particular package.
* My solution:  
`$ sudo yum update docker.x86_64`  
`$ sudo yum update`  
  
3. List all installed kernel-related packages, and list all installed or available ones.
* My solution:  
* Lab solution:  
`$ sudo yum list installed "kernel*"`  
`$ sudo yum list "kernel*"`  

4. Install the httpd-devel package, or anything else you might not have installed yet. Doing a simple:
`$ sudo yum list`  
will let you see a complete list; you may want to give a wildcard argument to narrow the list.  
* My solution:  
`$ sudo yum install httpd-devel`  
  
#### Lab 8.2 Using yum to Find Information about a Package
 Using yum (and not rpm directly), find:  
1. All packages that contain a reference to bash in their name or description.  
* My solution:  
`$ sudo yum search bash`  

2. Installed and available bash packages.  
* My solution:  
`$ sudo yum list installed "bash*"`  
`$ sudo yum list installed "*bash*"`  
`$ sudo yum list available "bash*"`  
`$ sudo yum list available "*bash*"`  
  
3. The package information for bash.  
* My solution:  
`$ sudo yum info "bash*"`  

4. The dependencies for the bash package.  
* My solution:  
* Correct solution:  
`$ sudo yum deplist bash`  
  
Try the commands you used above both as root and as a regular user. Do you notice any difference?  
* Not really
  
#### Lab 8.3 Managing Groups of Packages with **yum**
1. Use the following command to list all package groups available on your system:  
`$ yum grouplist`  

2. Identify the Backup Client group and generate the information about this group using the command  
`$ yum groupinfo "Backup Client"`  

3. Install using:  
`$ sudo yum groupinstall "Backup Client"`  

4. Identify a package group that’s currently installed on your system and that you don’t need. Remove it using yum groupremove as in:  
`$ sudo yum groupremove "Backup Client"`  
Note you will be prompted to confirm removal so you can safely type the command to see how it works.
You may find that the groupremove does not remove everything that was installed; whether this is a bug or a feature can be discussed.  
  
#### Lab 8.4 Adding a New **yum** Repository
According to its authors (at http://www.webmin.com/index.htm):  
> “Webmin is a web-based interface for system administration for Unix. Using any modern web browser, you can setup user accounts, Apache, DNS, file sharing and much more. Webmin removes the need to manually edit Unix configuration files like /etc/passwd, and lets you manage a system from the console or remotely.”  
We are going to create a repository for installation and upgrade. While we could simply go the download page and get the current rpm, that would not automatically give us any upgrades.  
1. Create a new repository file called webmin.repo in the /etc/yum.repos.d directory. It should contain the following:  
  [Webmin]  
  name=Webmin Distribution Neutral  
  baseurl=http://download.webmin.com/download/yum  
  mirrorlist=http://download.webmin.com/download/yum/mirrorlist  
  enabled=1  
  gpgcheck=0  
(Note you can also cut and paste the contents from http://www.webmin.com/download.html.)  
2. Install the webmin package.  
`$ sudo yum install webmin`  





### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
#### 8.4 What Is yum?
`/usr/bin/yum*` and `/usr/sbin/yum*`  
#### 8.5 Configuring yum to Use Repositories
`/etc/yum.repos.d/` (8.5)  

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------

#### 8.4 What Is yum?
`$ yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]`  
#### 8.6 Repository Files
`$ cat CentOS-Base.repo`  
#### 8.7 Queries
`$ sudo yum search keyword`  
`$ sudo yum list "*keyword*"`  
`$ sudo yum info keyword` or `$ yum info "libc*"`  
`$ sudo yum list [installed | updates | available ]`  
`$ sudo yum grouplist`  
`$ sudo yum groupinfo group1`  
`$ sudo yum groupinfo "Development Tools"` 
`$ sudo yum provides`  
`$ sudo yum provides "/logrotate.conf"`  
#### 8.8 Verifying Pacakges
`$ sudo yum install yum-plugin-verify`  
`$ sudo yum verify [package]`  
`$ sudo yum verify-rpm [package]`  
`$ sudo yum verify-all [package]`  
#### 8.9 Installing/Removing/Upgrading Packages
`$ sudo yum install package1 [package2]`  
`$ sudo yum localinstall package-file`  
`$ sudo yum groupinstall group-name` or `$ sudo yum install @group-name`  
`$ sudo yum remove package1 [package2]`  
`$ sudo yum update [package]`  
`$ sudo find /etc -name "*.rpm*"`  
#### 8.10 Additional Commands
`$ sudo yum list "yum-plugin*"`  
`$ sudo yum repolist`  
`$ sudo yum shell [text-file]`  
`$ sudo yum install --downloadonly package`  
`$ sudo yum history`  
#### 8.12 Using the yum and dnf Utilities Demo
`$ yumex`  
#### Lab 8.2 Using yum to Find Information about a Package
`$ sudo yum deplist bash`  
#### Lab 8.3 Managing Groups of Packages with **yum**
`$ yum grouplist`  
`$ yum groupinfo "Backup Client"`  
`$ sudo yum groupinstall "Backup Client"`  
`$ sudo yum groupremove "Backup Client"`  
#### Lab 8.4 Adding a New **yum** Repository
`$ sudo yum install webmin`  