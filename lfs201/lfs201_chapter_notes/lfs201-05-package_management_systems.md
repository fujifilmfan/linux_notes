Chapter 5: Package Management Systems
-------------------------------------

[5.3: Learning Objectives](#53-learning-objectives)  
[5.4: Software Packaging Concepts](#54-software-packaging-concepts)  
[5.5: Why Use Packages](#55-why-use-packages)  
[5.6: Package Types](#56-package-types)  
[5.7: Available Package Management Systems](#57-available-package-management-systems)  
[5.8: Packaging Tool Levels and Varieties](#58-packaging-tool-levels-and-varieties)  
[5.9: Package Sources](#59-package-sources)  
[5.10: Creating Software Packages](#510-creating-software-packages)  
[5.11: Revision Control System](#511-revision-control-system)  
[5.12: Available Source Control Systems](#512-available-source-control-systems)  
[5.13: The Linux Kernel and the Birth of git](#513-the-linux-kernel-and-the-birth-of-git)  
[5.14: How git Works](#514-how-git-works)  
[5.15: Package Management Systems Demo](#515-package-management-systems-demo)  
[Lab 5.1: Version Control with git](#lab-51-version-control-with-git)  
[Paths and Commands](#paths-and-commands)  
  
### 5.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain why software package management systems should be used.
* Understand the role of both binary and source packages.
* List the main available package management systems.
* Understand why two levels of utilities are needed; one that deals with just bare packages, and one that deals with dependencies among packages.
* Explain how creating your own package enhances the control you have over exactly what goes in software and how it is installed.
* Understand the role of source control systems and git in particular.

### 5.4: Software Packaging Concepts
----
**Package management systems** supply the tools that allow system administrators to automate installing, upgrading, configuring and removing software packages in a known, predictable and consistent manner. These systems:
* Gather and compress associated software files into a single package (archive), which may require one or more other packages to be installed first.​
* Allow for easy software installation or removal.​
* Can verify file integrity via an internal database.​
* Can authenticate the origin of packages.​
* Facilitate upgrades.​
* Group packages by logical features.​
* Manage dependencies between packages.
A given package may contain executable files, data files, documentation, installation scripts and configuration files. Also included are **metadata** attributes such as version numbers, checksums, vendor information, dependencies, descriptions, etc.  
Upon installation, all that information is stored locally into an internal database which can be conveniently queried

### 5.5: Why Use Packages
----
Features include:
* Automation:  No need for manual installs and upgrades.
* Scalability:  Install packages on one system, or 10,000 systems.
* Repeatability and predictability.
* Security and auditing.

### 5.6: Package Types
----
Packages come in several different types:
* **Binary** packages contain files ready for deployment, including executable files and libraries. These are architecture-dependent and must be compiled for each type of machine.
* **Source** packages are used to generate binary packages; one should always be able to rebuild a binary package (for example, by using `rpmbuild --rebuild` on **RPM**-based systems) from the source package. One source package can be used for multiple architectures.
* **Architecture-independent** packages contain files and scripts that run under script interpreters, as well as documentation and configuration files.
* **Meta-packages** are essentially groups of associated packages that collect everything needed to install a relatively large subsystem, such as a desktop environment, or an office suite, etc.
Binary packages are the ones that system administrators have to deal with most of the time.  
On 64-bit systems that can run 32-bit programs, one may have two binary packages installed for a given program, perhaps one with **x86_64** or **amd64** in its name, and the other with **i386** or **i686** in its name.  
Source packages can be helpful in keeping track of changes and source code used to come up with binary packages. 

### 5.7: Available Package Management Systems
----
There are two very common package management systems:  
* **RPM** (**Red Hat Package Manager**)
    * This system is used by all Red Hat-derived distributions, such as **Red Hat Enterprise Linux**, **CentOS**, and **Scientific Linux**, as well as by **SUSE** and its related community **openSUSE** distribution.
* **dpkg** (**Debian Package**)
    * This system is used by all **Debian**-derived distributions ,including **Debian**, **Ubuntu**, and **Linux Mint**.
There are other package management systems, such as **portage/emerge** used by **Gentoo**, **pacman**** used by **Arch**, and specialized ones used by **Embedded Linux** systems and **Android**.

### 5.8: Packaging Tool Levels and Varieties
----
Two levels to packaging systems:
1. **Low Level Utility**
* installs or removes a single package or list of packages; dependencies not fully handled
    * if another package needs to be installed, first installation will fail
    * if the package is needed by another package, removal will fail
    * the **rpm** and **dpkg** utilities play this role for the packaging systems that use them

2. **High Level Utility**
* solves the dependency problems
    * if another package or group of packages needs to be installed before software can be installed, such needs will be satisfied
    * if removing a package interferes with another installed package, the administrator will be given the choice of either aborting, or removing all affected software
    * the **yum**, **dnf**, and **zypper** utilities (and more recently, **PackageKit**) take care of the dependency resolution for rpm systems, and **apt-get** and **apt-cache** and other utilities take care of it for dpkg systems

### 5.9: Package Sources
----
* every distribution has one or more package **repositories** where system utilities go to obtain software and to update with new versions
* there are always other, external repositories, which can be added to the standard distribution-supported list; sometimes, these are closely associated with the distribution, and only rarely produce significant problems; an example would be the **EPEL** (**E**xtra **Pa**ckages for **E**nterprise **L**inux) set of version-dependent repositories, which fit well with **RHEL** since their source is **Fedora** and the maintainers are close to **Red Hat**. 
* when a package is updated in the main repository, dependent packages may not be updated in the external one, which can lead to one form of **dependency hell** 

### 5.10: Creating Software Packages
----
* Building your own package allows you to control exactly what goes in the software and exactly how it is installed; can automate tasks like:
    * creating needed symbolic links
    * creating directories as needed
    * setting permissions
    * anything that can be scripted
* mechanisms of how to build **.rpm** or **.deb** packages will not be discussed

### 5.11: Revision Control System
----
* **Source Control Systems** (or Revision Control Systems) coordinate cooperative development

### 5.12: Available Source Control Systems
----
Some products released under **GPL** license:

**Product**| **URL**
-----------| -------
RCS        | http://www.gnu.org/software/rcs
CVS        | http://ximbiot.com/cvs/wiki 
Subversion | http://subversion.tigris.org 
git        | http://www.kernel.org/pub/software/scm/git
GNU Arch   | http://www.gnu.org/software/gnu-arch 
Monotone   | http://www.monotone.ca
Mercurial  | http://mercurial-scm.org/
PRCS       | http://prcs.sourceforge.net

Git arose from the Linux kernel development community  

### 5.13: The Linux Kernel and the Birth of git
----
* see the section for details
* original author was Linus Torvalds
* links:
    * source code: http://www.kernel.org/pub/software/scm/git/
    * documentation: http://www.kernel.org/pub/software/scm/git/docs/

### 5.14: How git Works
----
* **git** is not a source control management system in the usual sense, and basic units it works with are not files
* two important data structures: an **object** database and a directory cache
* object database contains objects of three varieties:
    * **blobs**: chunks of binary data containing file contents
    * **trees**: sets of blobs including file names and attributes, giving the directory structure
    * **commits**: Changesets describing tree snapshots
* directory cache captures the state of the directory tree

### 5.15: Package Management Systems Demo
----
* `$ gnome-software` opens Application Installer, a sort of Linux app store
### Lab 5.1: Version Control with git
----
Basic git stuff; nothing to note  

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
packages | `$ rpmbuild --rebuild` | rebuild a binary package | LFS201 5.6
apps, packages | `$ gnome-software` | opens Application Installer, a sort Linux app store | LFS201 5.15  
  