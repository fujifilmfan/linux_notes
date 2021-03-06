Chapter 41: Linux Security Modules
----------------------------------

[41.1: Linux Security Modules](#411-linux-security-modules)  
[41.2: Chapter 41 Introduction](#412-chapter-41-introduction)  
[41.3: Learning Objectives](#413-learning-objectives)  
[41.4: What Are Linux Security Modules?](#414-what-are-linux-security-modules)  
[41.5: LSM Choices](#415-lsm-choices)  
[41.6: SELinux Overview](#416-selinux-overview)  
[41.7: SELinux Modes](#417-selinux-modes)   
[41.8: SELinux Policies](#418-selinux-policies)  
[41.9: Context Utilities](#419-context-utilities)  
[41.10: SELinux and Standard Command Line Tools](#4110-selinux-and-standard-command-line-tools)  
[41.11: SELinux Context Inheritance and Preservation](#4111-selinux-context-inheritance-and-preservation)  
[41.12: restorecon](#4112-restorecon)  
[41.13: semanage](#4113-semanage)  
[41.14: Using SELinux Booleans](#4114-using-selinux-booleans)  
[41.15: getsebool and setsebool](#4115-getsebool-and-setsebool)  
[41.16.a: Troubleshooting Tools I](#4116a-troubleshooting-tools-i)  
[41.16.b: Troubleshooting Tools II](#4116b-troubleshooting-tools-ii)  
[41.17: Additional Online Resources](#4117-additional-online-resources)  
[41.18: AppArmor](#4118-apparmor)  
[41.19: Checking Status](#4119-checking-status)  
[41.20: Modes and Profiles](#4120-modes-and-profiles)  
[41.21: AppArmor Utilities](#4121-apparmor-utilities)   
[Lab 41.1: SELinux: Contexts](#lab-411-selinux-contexts)  
[Lab 41.2: Explore the apparmor Security](#lab-412-explore-the-apparmor-security)  
[Paths and Commands](#paths-and-commands)  
  
### 41.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Understand how the Linux Security Module framework works and how it is* deployed. ​
* List the various LSM implementations available.
* Delineate the main features of SELinux.
* Explain the different modes and policies available.
* Grasp the importance of contexts and how to get and set them.
* Know how to use the important SELinux utility programs.
* Gain some familiarity with AppArmor.
  
### 41.4: What Are Linux Security Modules?
----
* the idea of **L**inux **S**ecurity **M**odules (**LSM**) is to implement mandatory access controls over the variety of requests made to the kernel, but to do so in a way that:
    1. minimizes changes to the kernel
    2. minimizes overhead on the kernel
    3. permits flexibility and choice between different implementations, each of which is presented as a self-contained LSM
* the basic idea is to **hook system calls**; insert code whenever an application requests a transition to kernel (system) mode in order to accomplish work that requires enhanced abilities
  
### 41.5: LSM Choices
----
* LSM was incorporated into the Linux kernel in 2003
* it provides alternatives to **SELinux**
* enhanced security models:
    * SELinux
    * AppArmor
    * Smack
    * Tomoyo
* only one can be used at a time, as they potentialy modify the same parts of the kernel
  
### 41.6: SELinux Overview
----
* background:
    * originally developed by the NSA
    * integral to RHEL for a long time
* it's a set of security rules that are used to determine which processes can access which files, directories, ports, and other items on the system
* it works with these three conceptual quantities:
    1. **Contexts**: labels to files, processes and ports; a name used by a rule to define how users, processes, files, and ports interact with each other; describe allowed actions (default is to deny access); examples of contexts are SELinux user, role and type
    1. **Rules**: describe access control in terms of contexts, processes, files, ports, users, etc.
    1. **Policies**: a set of **rules** that describe what system-wide access control decisions should be made by SELinux
  
### 41.7: SELinux Modes
----
* SELinux can be run under one of the three following modes:  
    * **Enforcing**: all SELinux code is operative and access is denied according to policy; all violations are audited and logged
    * **Permissive**: enables SELinux code, but only audits and warns about operations that would be denied in enforcing mode
    * **Disabled**: completely disables SELinux kernel and application code, leaving the system without any of its protections
* modes are selected and explained in `/etc/selinux/config`; that file is often linked from `/etc/sysconfig/selinux`
* **sestatus** used to display the current SELinux mode and policy
* `$ getenforce` show current SELinux mode
* `$ sudo setenforce Permissive` set SELinux mode to permissive
    * **setenforce** can switch between permissive and enforcing modes on the fly but will not allow you to disable SELinux completely
* to disable SELinux:
    * configuration file: edit `/etc/selinux/config` and set **SELINUX=disabled`
    * kernel parameter: add **selinux=0** to the kernel paramter list when rebooting
* don't disable SELinux on a system in which SELinux will be re-enabled; use **permissive** mode instead to avoid relabeling the entire filesystem (time-consuming)
  
### 41.8: SELinux Policies
----
* `/etc/selinux/config` also sets the **SELinux policy**
* multiple policies are allowed, but only one can be active at a time
* changing policies might require reboot and relabeling of filesystem contents
* policy contents are installed under `/etc/selinux/[SELINUXTYPE]`
* the most common policies are:
    * **targeted**: the default policy in which SELinux is more restricted to targeted processes; user processes and **init** processes are not targeted; SELinux enforces memory restrictions for all processes, which reduces the vulnerability to buffer overflow attacks
    * **minimum**: a modification of the targeted policy where only selected processes are protected
    * **MLS**: **M**ulti-**L**evel **S**ecurity policy is much more restrictive; all processes are placed in fine-grained security domains with particular policies
* my system has "targeted" and a "final" directory with restricted access and no files, it seems
  
### 41.9: Context Utilities
----
* contexts (labels) are used to describe access rules
* four SELinux contexts:
    * **user**
    * **role**
    * **type**: most commonly used context; labels end with **_t** as in **kernel_t**
    * **level**
* use the **-Z** option to see context:
    * `$ ls -Z` list files and show context
    * `$ ps auZ` show processes with context
* use **chcon** to change context:
    * `$ chcon -t etc_t somefile`
    * `$ chcon --reference somefile so`
  
### 41.10: SELinux and Standard Command Line Tools
----
* many command line functions were extended to support SELinux, often using the **Z** parameter
* examples: **ls**, **ps**, **cp**, **mv**, **mkdir**
  
### 41.11: SELinux Context Inheritance and Preservation
----
* newly created files inherit the context from their parent directory
* when moving files, the context of the source directory may be preserved, causing problems
* here's an example:
    * `$ cd /tmp/`
    * `$ touch tmpfile`
    * `$ ls -Z tmpfile`
        > -rw-rw-r--. peter peter unconfined_u:object_r:user_tmp_t:s0 tmpfile  
    * `$ cd`
    * `$ touch homefile`
    * `$ ls -Z homefile`
        > -rw-rw-r--. peter peter unconfined_u:object_r:user_home_t:s0 homefile  
    * `$ mv /tmp/tmpfile .`
    * `$ ls -Z`
        > -rw-rw-r--. peter peter unconfined_u:object_r:user_home_t:s0 homefile  
        > -rw-rw-r--. peter peter unconfined_u:object_r:user_tmp_t:s0 tmpfile  
* classical example: moving files to the `DocumentRoot` directory of an **httpd** server that were created at `/tmp`, for example, will make them unaccessible to the **httpd** server until the context is adjusted
  
### 41.12: restorecon
----
* **restorecon** resets file contexts based on parent directory settings; example:
    * `$ ls -Z`
        > -rw-rw-r--. peter peter unconfined_u:object_r:user_home_t:s0 homefile  
        > -rw-rw-r--. peter peter unconfined_u:object_**r:user_tmp_t**:s0 tmpfile  
    * `$ restorecon -Rv /home/peter` resets the default SELinux context recursively for all files at the home directory
        > restorecon reset /home/peter/tmpfile context \  
        > unconfined_u:object_r:user_tmp_t:s0->unconfined_u:object_r:user_home_t:s0   
    * `$ ls -Z`
        > -rw-rw-r--. peter peter unconfined_u:object_r:user_home_t:s0 homefile  
        > -rw-rw-r--. peter peter unconfined_u:object_r:**user_home_t**:s0 tmpfile  
  
### 41.13: semanage
----
* **semanage** is used to change and display the default context of files and directories; it does not apply the settings to existing objects; example:
    * [root@rhel7 /]`# mkdir /virtualHosts`
    * [root@rhel7 /]`# ls -Z`
        > ...  
        > drwxr-xr-x. root root unconfined_u:object_r:**default_t**:s0 virtualHosts  
    * [root@rhel7 /]`# semanage fcontext -a -t httpd_sys_content_t /virtualHosts`
    * [root@rhel7 /]`# ls -Z`
        > ...  
        > drwxr-xr-x. root root unconfined_u:object_r:default_t:s0 virtualHosts  
    * [root@rhel7 /]`# restorecon -RFv /virtualHosts`
        > restorecon reset /virtualHosts context  
        > unconfined_u:object_r:default_t:s0->system_u:object_r:httpd_sys_content_t:s0  
    * [root@rhel7 /]`# ls -Z`
        > drwxr-xr-x. root root system_u:object_r:**httpd_sys_content_t**:s0 virtualHosts  
  
### 41.14: Using SELinux Booleans
----
* SELinux policy behavior can be configured at runtime without rewriting the policy
* do this by configuring SELinux Booleans, which are policy parameters that can be enabled and disabled:
    * **getsebool** - to see booleans
    * **setsebool** - to set booleans
    * **semanage boolean -i** - to see persistent boolean settings.
  
### 41.15: getsebool and setsebool
----
* `$ getsebool -a` prints the boolean name and current status; simpler output than what is given by **semanage**
* `$ setsebool` changes boolean status; use **-P** parameter to make the changes persistent
* an example of non-persistent change using **setsebool**:
    * `$ getsebool ssh_chroot_rw_homedirs`
        > ssh_chroot_rw_homedirs --> off  
    * `$ sudo setsebool ssh_chroot_rw_homedirs on`
    * `$ getsebool ssh_chroot_rw_homedirs`
        > ssh_chroot_rw_homedirs --> on  
    * `$ sudo reboot`
        > ...  
    * `$ getsebool ssh_chroot_rw_homedirs`
        > ssh_chroot_rw_homedirs --> off  
* An example of persistent change using **setsebool -P**:
    * `$ getsebool ssh_chroot_rw_homedirs`
        > ssh_chroot_rw_homedirs --> off  
    * `$ sudo setsebool -P ssh_chroot_rw_homedirs on`
    * `$ getsebool ssh_chroot_rw_homedirs`
        > ssh_chroot_rw_homedirs --> on  
    * `$ sudo reboot`
        > ...  
    * `$ getsebool ssh_chroot_rw_homedirs`
        > ssh_chroot_rw_homedirs --> on  
  
### 41.16: Troubleshooting Tools
----
* SELinux comes with a set of tools that collect issues at run time, log these issues and propose solutions to prevent same issues from happening again
* these utilities are provided by the **setroubleshoot-server** package
* example usage:
    * [root@rhel7 ~]`# echo 'File created at /root' > rootfile`
    * [root@rhel7 ~]`# mv rootfile /var/www/html/`
    * [root@rhel7 ~]`# wget -O - localhost/rootfile`
        ```
        --2014-11-21 13:42:04--  http://localhost/rootfile
        Resolving localhost (localhost)... ::1, 127.0.0.1
        Connecting to localhost (localhost)|::1|:80... connected.
        HTTP request sent, awaiting response... 403 Forbidden
        2014-11-21 13:42:04 ERROR 403: Forbidden.
        ```
    * [root@rhel7 ~]`# tail /var/log/messages`
        ```
        Nov 21 13:42:04 rhel7 setroubleshoot: Plugin Exception restorecon
        Nov 21 13:42:04 rhel7 setroubleshoot: SELinux is preventing /usr/sbin/httpd from getattr access on the file .
        For complete SELinux messages. run sealert -l d51d34f9-91d5-4219-ad1e-5531e61a2dc3
        Nov 21 13:42:04 rhel7 python: SELinux is preventing /usr/sbin/httpd from getattr access on the file .
        *****  Plugin catchall (100. confidence) suggests  **************************
        If you believe that httpd should be allowed getattr access on the  file by default.
        Then you should report this as a bug.
        You can generate a local policy module to allow this access.
        Do allow this access for now by executing
        # grep httpd /var/log/audit/audit.log | audit2allow -M mypol
        # semodule -i mypol.pp
        ```
    * [root@rhel7 ~]`# sealert -l d51d34f9-91d5-4219-ad1e-5531e61a2dc3`
        ```
        SELinux is preventing /usr/sbin/httpd from getattr access on the file .
        *****  Plugin catchall (100. confidence) suggests  **************************
        If you believe that httpd should be allowed getattr access on the  file by default.
        Then you should report this as a bug.
        You can generate a local policy module to allow this access.
        Do allow this access for now by executing:
        # grep httpd /var/log/audit/audit.log | audit2allow -M mypol
        # semodule -i mypol.pp
        Additional Information:
        Source Context system_u:system_r:httpd_t:s0
        Target Context unconfined_u:object_r:admin_home_t:s0
        Target Objects [ file ]
        Source httpd
        Source Path /usr/sbin/httpd
        Port <Unknown>
        Host rhel7
        Source RPM Packages httpd-2.4.6-18.el7_0.x86_64
        Target RPM Packages
        Policy RPM selinux-policy-3.12.1-153.el7_0.11.noarch
        Selinux Enabled True
        Policy Type targeted
        Enforcing Mode Enforcing
        Host Name rhel7
        Platform Linux rhel7 3.10.0-123.9.3.el7.x86_64 #1 SMP Thu
        Oct 30 00:16:40 EDT 2014 x86_64 x86_64
        Alert Count 2
        First Seen 2014-11-21 12:34:13 CET
        Last Seen 2014-11-21 13:42:04 CET
        Local ID d51d34f9-91d5-4219-ad1e-5531e61a2dc3

        Raw Audit Messages
        type=AVC msg=audit(1416573724.395:1598): avc: denied { getattr } for pid=20180 comm="httpd"
        path="/var/www/html/rootfile" dev="dm-0" ino=70624441 scontext=system_u:system_r:httpd_t:s0
        tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file

        type=SYSCALL msg=audit(1416573724.395:1598): arch=x86_64 syscall=lstat success=no exit=EACCES
        a0=7f2896ed0578 a1=7fffcc64fb30 a2=7fffcc64fb30 a3=0 items=0 ppid=20178 pid=20180
        auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48 egid=48 sgid=48 fsgid=48 tty=(none)
        ses=4294967295 comm=httpd exe=/usr/sbin/httpd subj=system_u:system_r:httpd_t:s0 key=(null)

        Hash: httpd,httpd_t,admin_home_t,file,getattr
        ```
* RHEL suggests `$ grep httpd /var/log/audit/audit.log |  audit2allow -M mypol`
    * `$ audit2allow ...` generates SELinux policy rules from logs of denied operations
    * `$ audit2why ...` translates SELinux audit messages into a description of why the access was denied
* continuing with previous example:
    * [root@rhel7 ~]`# restorecon -Rv /var/www/html/`
        > restorecon reset rootfile context  
        > unconfined_u:object_r:admin_home_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0  
    * [root@rhel7 ~]`# wget -q -O - localhost/rootfile`
        > File created at /root  
  
### 41.17: Additional Online Resources
----
* [SELinux User's and Administrator's Guide](https://docs.fedoraproject.org/en-US/Fedora/25/html/SELinux_Users_and_Administrators_Guide/)
* [Red Hat Enterprise Linux 6 Security-Enhanced Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/security-enhanced_linux/index)
  
### 41.18: AppArmor
----
AppArmor:  
* is an alternative to SELinux
* provides Mandatory Access Control (MAC)
* allows administrators to associate a security profile to a program which restricts its capabilities
* is considered easier (by some but not all) to use than SELinux
* is considered filesystem-neutral (no security labels required).
* supplements the traditional UNIX Discretionary Access Control (DAC) model by providing (MAC)
* has a learning mode, in which violations of the profile are logged, but not prevented, which can then be turned into a profile
  
### 41.19: Checking Status
----
* `$ sudo systemctl [start|stop|restart|status] apparmor` change or inquire about the current state of AppArmor operation
​* `$ sudo systemctl [enable|disable} apparmor` cause AppArmor to be loaded or not loaded at boot
* `$ sudo apparmor_status` see the current AppArmor status
* **profiles** and **processes** are in either enforce or complain mode, directly analogous to SELinux's **enforcing** and **permissive** modes
  
### 41.20: Modes and Profiles
----
* processes can be run in either of the two modes:
    * **Enforce Mode**: applications are prevented from acting in ways which are restricted
        * attempted violations are reported to the system logging files
        * default mode
        * a profile can be set to this mode with **aa-enforce**
    * **Complain Mode**: policies are not enforced
        * attempted policy violations are reported
        * also called the learning mode
        * a profile can be set to this mode with **aa-complain**
* profiles are stored in `/etc/apparmor.d`
* full documentation on what can go in these files ​can be obtained by doing `$ man apparmor.d`
  
### 41.21: AppArmor Utilities
----
* `$ rpm -qil apparmor-utils | grep bin​` view AppArmor utilities on OpenSUSE

**AppArmor Utilities**
Program         | Use 
-------         | ---
apparmor_status | Show status of all profiles and processes with profiles
apparmor_notify | Show a summary for AppArmor log messages
complain        | Set a specified profile to complain mode
enforce         | Set a specified profile to enforce mode
disable         | Unload a specified profile from the current kernel and prevent from being loaded on system startup
logprof         | Scan log files, and, if AppArmor events that are not covered by existing profiles have been recorded, suggest how to take into account, and, if approved, modify and reload
easyprof        | Help set up a basic AppArmor profile for a program
  
### Lab 41.1: SELinux: Contexts
----
1. Verify **SELinux** is enabled and in **enforcing** mode, by executing **getenforce** and **sestatus**. If not, edit `/etc/selinux/config`, reboot, and check again.
    * setting SELinux to enforcing mode in `/etc/selinux/config` prevented login completely
2. Install the **httpd** package (if not already present) which provides the Apache web server, and then verify that it is working:
    * `$ sudo yum install httpd`
    * `$ elinks http:/localhost` (or another browser, graphical or otherwise)
3. As superuser, create a small file in `/var/www/html`:
    * `$ sudo sh -c "echo file1 > /var/www/html/file1.html"`
4. Verify you can see it:
    * `$ elinks -dump http://localhost/file1.html`
        > file1  
    * Now create another small file in root’s home directory and move it to `/var/www/html`. (Do not copy it, move it!) Then try and view it:
    * `$ sudo cd /root`
    * `$ sudo sh -c "echo file2 > file2.html"`
    * `$ sudo mv file2.html /var/www.html`
    * `$ elinks -dump http://localhost/file2.html`
        ```
        Forbidden
        You don't have permission to access /file2.html on this server.
        ```
5. Examine the security contexts:
    * `$ cd /var/www/html`
    * `$ ls -Z file*html`
        ```
        -rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 file1.html
        -rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 file2.html
        ```
6. Change the offending context and view again:
    * `$ sudo chcon -t httpd_sys_content_t file2.html`
    * `$ elinks http://localhost/file2.html`
        > file2  
  
### Lab 41.2: Explore the apparmor Security
----
"This exercise can only be performed on a system (such as **Ubuntu**) where **AppArmor** is installed."
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
security, system | `/etc/selinux/config` | SELinux config file in which modes are selected and explained; also used to set the SELinux policy | LFS201 41.7
security, system | `/etc/sysconfig/selinux` | contains symbolic link to `/etc/selinux/config` | LFS201 41.7
security, system | `/etc/selinux/[SELINUXTYPE]` | stores SELinux policies | LFS201 41.8
security, system | `/etc/apparmor.d` | contains AppArmor profiles | LFS201 41.20
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
security, system | `$ sestatus` | used to display the current SELinux mode and policy | LFS201 41.7
security, system | `$ getenforce` | show current SELinux mode | LFS201 41.7
security, system | `$ sudo setenforce Permissive` | set SELinux mode to permissive | LFS201 41.7
security, system | `$ ls -Z` | list files and show SELinux context | LFS201 41.9
security, system | `$ ps auZ` | show processes with SELinux context | LFS201 41.9
security, system | `$ chcon -t etc_t somefile` | change SELinux context | LFS201 41.9
security, system | `$ chcon --reference somefile so` | change SELinux context | LFS201 41.9
security, system | `$ restorecon ...` | resets file contexts based on parent directory settings | LFS201 41.12
security, system | `$ restorecon -Rv /home/peter` | resets the default SELinux context recursively for all files at the home directory | LFS201 41.12
security, system | `$ semanage ...` | used to change and display the default context of files and directories; does not apply the settings to existing objects | LFS201 41.13
security, system | `# semanage fcontext -a -t httpd_sys_content_t /virtualHosts` | change context default file context | LFS201 41.13
security, system | `# restorecon -RFv /virtualHosts` | apply context change to existing directory | LFS201 41.13
security, system | `$ getsebool ...` | show SELinux booleans | LFS201 41.14
security, system | `$ setsebool ...` | set SELinux booleans | LFS201 41.14
security, system | `$ semanage boolean -i` | show persistent SELinux boolean settings | LFS201 41.14
security, system | `$ getsebool -a` | prints the boolean name and current status; simpler output than what is given by **semanage** | LFS201 41.15
security, system | `$ setsebool` | changes boolean status; use **-P** parameter to make the changes persistent | LFS201 41.15
security, system | `$ getsebool ssh_chroot_rw_homedirs` | show boolean name and current status | LFS201 41.15
security, system | `$ sudo setsebool ssh_chroot_rw_homedirs on` | change boolean status non-persistently | LFS201 41.15
security, system | `$ sudo setsebool -P ssh_chroot_rw_homedirs on` | change boolean status persistently | LFS201 41.15
security, system | `# sealert -l d51d34f9-91d5-4219-ad1e-5531e61a2dc3` | view SELinux alert details(?) | LFS201 41.16
security, system | `$ audit2allow ...` | generates SELinux policy rules from logs of denied operations | LFS201 41.16
security, system | `$ audit2why ...` | translates SELinux audit messages into a description of why the access was denied | LFS201 41.16
security, system | `# restorecon -Rv /var/www/html/` | apply context change to directory | LFS201 41.16
security, system | `$ sudo systemctl [start|stop|restart|status] apparmor` | change or inquire about the current state of AppArmor operation | LFS201 41.19
​security, system | `$ sudo systemctl [enable|disable} apparmor` | cause AppArmor to be loaded or not loaded at boot | LFS201 41.19
security, system | `$ sudo apparmor_status` | see the current AppArmor status | LFS201 41.19
security, system | `$ aa-enforce ...` | set an AppArmor profile to enforce mode | LFS201 41.20
security, system | `$ aa-complain ...` | set an AppArmor profile to complain mode | LFS201 41.20
security, system | `$ man apparmor.d` | documention for AppArmor, including what can go in a profile | LFS201 41.20
security, system | `$ rpm -qil apparmor-utils | grep bin​` | view AppArmor utilities on OpenSUSE | LFS201 41.21
  