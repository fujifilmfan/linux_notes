Chapter 42: Local System Security
---------------------------------

[42.1: Local System Security](#421-local-system-security)  
[42.2: Chapter 42 Introduction](#422-chapter-42-introduction)  
[42.3: Learning Objectives](#423-learning-objectives)  
[42.4: Local System Security](#424-local-system-security)  
[42.5: Creating a Security Policy](#425-creating-a-security-policy)  
[42.6: What to Include in the Policy](#426-what-to-include-in-the-policy)  
[42.7: What Risks to Assess](#427-what-risks-to-assess)  
[42.8: Choosing a Security Philosophy](#428-choosing-a-security-philosophy)  
[42.9: Some General Security Guidelines](#429-some-general-security-guidelines)  
[42.10: Updates and Security](#4210-updates-and-security)  
[42.11: Hardware Accessibility and Vulnerability](#4211-hardware-accessibility-and-vulnerability)  
[42.12: Hardware Access Guidelines](#4212-hardware-access-guidelines)  
[42.13: Protection of BIOS](#4213-protection-of-bios)  
[42.14: Protecting the Boot Loader with Passwords](#4214-protecting-the-boot-loader-with-passwords)  
[42.15: Using Secure Mounting Options](#4215-using-secure-mounting-options)  
[42.16: setuid and setgid](#4216-setuid-and-setgid)  
[42.17: Setting the setuid/setgid Bits](#4217-setting-the-setuidsetgid-bits)  
[Lab 42.1: Security and Mount Options](#lab-421-security-and-mount-options)  
[Lab 42.2: More on setuid and Scripts](#lab-422-more-on-setuid-and-scripts)  
[Paths and Commands](#paths-and-commands)  
  
### 42.3: Learning Objectives
----
By the end of this chapter, you should be able to:  
* Assess system security risks.
* Fashion and implement sound computer security policies and procedures.
* Efficiently protect BIOS and the boot loader with passwords.
* Use appropriate **mount** options, **setuid**, and **setgid** to enhance security.
  
### 42.4: Local System Security
----
* security can be defined in terms of the system's ability to regularly do what it is supposed to do, integrity and correctness of the system, and ensuring that the system is only available to those authorized to use it
* the four areas we need to protect include physical, local, remote, and personnel
  
### 42.5: Creating a Security Policy
----
* a security policy should be:
    * be simple and easy to understand
    * get constantly updated
    * be in the form of a written document in addition to online documentation if needed
    * describe both policies and procedures
    * specify enforcement actions
    * specify actions to take in response to a security breach
  
### 42.6: What to Include in the Policy
----
* a security policy should cover:
    * confidentiality
    * data Integrity
    * availability
    * consistency
    * control
    * audit
  
### 42.7: What Risks to Assess
----
* risk analysis is based on the following three questions:
    * What do I want to protect?
    * What am I protecting it against?
    * How much time, personnel, and money is needed to provide adequate protection?
  
### 42.8: Choosing a Security Philosophy
----
* there are two basic philosophies found in use in most computing environments:
    * anything not expressly permitted is denied (more common)
    * anything not expressly forbidden is permitted (implies a high degree of assumed trust)
  
### 42.9: Some General Security Guidelines
----
* some general guidelines to remember when developing security philosophies are:
    1. **the human factor is the weakest link** - you must educate your users and keep them happy; the largest percentage of break-ins are internal and are not often malicious
    2. **no computing environment is invulnerable** - the only secure system is one that is not connected to anything, locked in a secure room and turned off
    3. **paranoia is a good thing** - be suspicious, be vigilant and persevere when securing a computer; check process, users, and look for anything out of the ordinary
* users should never put the current directory in their path, i.e, do not do something in `~/.bashrc` like **PATH=./:$PATH**
    * a malicious person could substitute for a program with one of the same name, that could do harmful things, such as a script named **ls** that contains just the line `/bin/rm -rf $HOME`
    * if you were to go to the directory that contains this file and type **ls**, you would wipe out your home directory
    * ? but such a file could be put anywhere else in the PATH and do the same thing, right?
  
### 42.10: Updates and Security
----
* **any system which is not fully updated should be considered vulnerable**
    * most attacks exploit known security holes and are deployed in the time period between revelation of a problem and patches being applied
    * **Zero Day** exploits are actually much rarer, where an attacker uses a security hole that either has not been discovered yet or for which a fix has not been released
* don't be hesitant because Windows and MacOS updates sometimes seem to introduce more problems than they solve: security **regressions** in Linux are extremely rare
  
### 42.11: Hardware Accessibility and Vulnerability
----
* any time hardware is physically accessible security can be compromised by:
    * key logging
    * network sniffing
    * booting with a live or rescue disk
    * remounting and modifying disk content
* physical access to a system makes it possible for attackers to easily leverage several attack vectors, in a way that makes all operating system level recommendations irrelevant
* thus, a security policy needs to start with securing physical access to servers and workstations
  
### 42.12: Hardware Access Guidelines
----
* steps include:
    * locking down workstations and servers
    * protecting your network links against access by people you do not trust
    * protecting your keyboards where passwords are entered to ensure the keyboards cannot be tampered with
    * configuring password protection of the BIOS in such a way that the system cannot be booted with a live or rescue CD/DVD or USB key (might not necessary for single-user computers or those in a home environment)
* if sensitive information is on your system that requires careful protection, either it should not be there, or it should be better protected by following the above guidelines
  
### 42.13: Protection of BIOS
----
* the BIOS:
    * is the lowest level of security
    * should be protected by use of a password
    * should be updated and current
* securing the BIOS only matters if someone can gain physical access to the machine
* unnecessary BIOS updates can render a system useless, and most updates have nothing to do with security, so be careful with those
  
### 42.14: Protecting the Boot Loader with Passwords
----
* using a bootloader password alone will stop a user from editing the bootloader configuration during the boot process
* it will not prevent a user from booting from an alternative boot media such as optical disks or pendrives, so use with BIOS password for full protection
* do not edit `grub.cfg` directly, but edit system configuration files in `/etc/grub.d` and then run **update-grub** or **grub2-mkconfig** and save the new config file (see LFS201 38)
* example documentation: [Grub 2 Password Protection](https://help.ubuntu.com/community/Grub2/Passwords)
  
### 42.15: Using Secure Mounting Options
----
* **mount** option that enhance security:
    * **nodev**: do not interpret character or block special devices on the filesystem
    * **nosuid**: the **set-user-identifier** or **set-group-identifier** bits are not to take effect
    * **noexec**: restrict direct execution of any binaries on the mounted filesystem
    * **ro**: mount the filesystem in **read-only** mode as in `$ mount -o ro,noexec,nodev /dev/sda2 /mymountpt` or in `/etc/fstab`: `/dev/sda2 /mymountpt  ext4 ro,noexec,nodev 0 0`
  
### 42.16: setuid and setgid
----
* programs normally run with the privileges of the user rather than the owner
* setting the **setuid** (**set u**ser **ID**) flag on an executable file can give the program the access rights of the **owner** rather than the **user**
* setting the **setgid** bit can give the privileges of the group rather than the user
    * using the **setgid** setting on the directory changes this so that files created in the directory are group owned by the group owner of the directory
* this is generally a **bad idea** and should be avoided in most circumstances; write a **daemon** program with lesser privileges instead
    * some distros have disabled this ability entirely

### 42.17: Setting the setuid/setgid Bits
----
* `$ chmod u+s somefile` 'does the setuid'
* `$ chmod g+s somefile` 'does the setgid'
* `$ chmod g+s somedir` creates a shared directory; files created here are group owned by the group owner of the directory
* you can't effectively change the **setuid** on a shell script file; in fact, it won't do anything unless you actually change the **setuid** bit on the shell itself, which would be a terrible security hole
  
### Lab 42.1: Security and Mount Options
----
  
### Lab 42.2: More on setuid and Scripts
----
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
security, system | to `/etc/fstab` add `/dev/sda2 /mymountpt  ext4 ro,noexec,nodev 0 0` | mount the filesystem in **read-only** mode | LFS201 42.15
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
security, system | `$ mount -o ro,noexec,nodev /dev/sda2 /mymountpt` | mount the filesystem in **read-only** mode | LFS201 42.15
accounts, security, system | `$ chmod u+s somefile` | 'does the setuid' | LFS201 42.17
accounts, security, system | `$ chmod g+s somefile` | 'does the setgid' | LFS201 42.17
accounts, security, system | `$ chmod g+s somedir` | creates a shared directory; files created here are group owned by the group owner of the directory | LFS201 42.17
  