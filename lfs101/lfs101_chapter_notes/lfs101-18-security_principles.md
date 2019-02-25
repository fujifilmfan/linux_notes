Chapter 18: Local Security Principles
-------------------------------------

[18.0: Introduction/ Learning Objectives](#180-introduction-learning-objectives)  
[18.1: Understanding Linux Secuirty](#181-understanding-linux-secuirty)  
[18.2: When Are root Privileges Required?](#182-when-are-root-privileges-required?)  
[18.3: sudo, Process Isolation, Limiting Hardware Access and Keeping Systems` Current](#183-sudo-process-isolation-limiting-hardware-access-and-keeping-systems-current)  
[18.4: Working with passwords](#184-working-with-passwords)
[18.5: Securing the Boot Process and Hardware Resources](#185-securing-the-boot-process-and-hardware-resources)
[18.6: Summary](#186-summary)  
  
### 18.0: Introduction/ Learning Objectives
----
#### Learning Objectives
By the end of this chapter, you should:  
 * Have a good grasp of best practices and tools for making Linux systems as secure as possible.
 * Understand the powers and dangers of using the **root** (**superuser**) account.
 * Use the **sudo** command to perform privileged operations while restricting enhanced powers as much as feasible.
 * Explain the importance of process isolation and hardware access.
 * Work with passwords, including how to set and change them.
 * Describe how to secure the boot process and hardware resources.

### 18.1: Understanding Linux Secuirty
----
#### User Accounts
Seven fields in `/etc/passwd`:  

Field Name                | Details                                                                                                        | Remarks  
----------                | -------                                                                                                        | -------  
Username                  | User login name                                                                                                | Should be between 1 and 32 characters long  
Password                  | User password (or the character **x** if the password is stored in the `/etc/shadow` file) in encrypted format | Is never shown in Linux when it is being typed; this stops prying eyes  
User ID (UID)             | Every user must have a user id (UID)                                                                           | UID 0 is reserved for root user  
User ID (UID) *continued* |                                                                                                                | UID's ranging from 1-99 are reserved for other predefined accounts  
User ID (UID) *continued* |                                                                                                                | UID's ranging from 100-999 are reserved for system accounts and groups  
User ID (UID) *continued* |                                                                                                                | Normal users have UID's of 1000 or greater  
Group ID (GID)            | The primary Group ID (GID); Group Identification Number stored in the `/etc/group` file                        | Is covered in detail in the chapter on Processes  
User Info                 | This field is optional and allows insertion of extra information about the user such as their name             | For example: `Rufus T. Firefly`  
Home Directory            | The absolute path location of user's home directory                                                            | For example: `/home/rtfirefly`  
Shell                     | The absolute location of a user's default shell                                                                | For example: `/bin/bash`  

#### Types of Accounts
Four types of accounts:  
* root  
* System  
* Normal  
* Network  
  
For a safe environment:  
* grant minimum privileges necessary  
* remove inactive accounts; **last** can be used to help identify potentially inactive accounts  

#### Understanding the root Account
* **root** is the most privileged account and has no security restrictions imposed upon it  
* when logged in or acting as **root**, the shell prompt displays **#**  

### 18.2: When Are root Privileges Required?
----
#### Operations Requiring root Privileges
**root** privileges are required to perform operations such as:  
 * Creating, removing and managing user accounts.  
 * Managing software packages.  
 * Removing or modifying system files.  
 * Restarting system services.  

#### Operations Not Requiring root Privileges
* **SUID** (**S**et owner **U**ser **ID** upon execution)
    * a file permission given to a file  
    * allows a user to run a program with permissions of file **owner**, which may be root   
* Examples of operations that do not require root privileges:  

Operations that do not require Root privilege | Examples of this operation  
--------------------------------------------- | --------------------------  
Running a network client                      | Sharing a file over the network  
Using devices such as printers                | Printing over the network  
Operations on files that the user has proper permissions to access | Accessing files that you have access to or sharing data over the network  
Running SUID-root applications                | Executing programs such as **passwd**  

### 18.3: sudo, Process Isolation, Limiting Hardware Access and Keeping Systems Current
----
#### Comparing sudo and su
Differences between **su** and **sudo**:  
* **su**  
   * When elevating privilege, you need to enter the **root** password. Giving the root password to a normal user should **never, ever** be done.  
   * Once a user elevates to the **root** account using **su**, the user can do **anything** that the **root** user can do for as long as the user wants, without being asked again for a password.  
   * The command has limited logging features.  
* **sudo**  
   * When elevating privilege, you need to enter the user’s password and not the **root** password.  
   * Offers more features and is considered more secure and more configurable. Exactly what the user is allowed to do can be precisely controlled and limited. By default the user will either always have to keep giving their password to do further operations with **sudo**, or can avoid doing so for a configurable time interval.  
   * The command has detailed logging features.  

#### sudo Features
* can track unsuccessful attempts at gaining root access
   * authorization is based on config info stored in `/etc/sudoers` file and `/etc/sudoers.d` directory  
   * failed attempt in a log file (usually `/var/log/secure`) would look like this:
```
authentication failure; logname=op uid=0 euid=0 tty=/dev/pts/6 ruser=op rhost= user=op
conversation failed
auth could not identify password for [op]
op : 1 incorrect password attempt ;
TTY=pts/6 ; PWD=/var/log ; USER=root ; COMMAND=/bin/bash
```

#### The sudoers File
* `/etc/sudoers` and `/etc/sudoers.d` used to determine rights and scope  
* unknown user requests and requests to do operations not allowed to the user even with sudo are reported  
* `/etc/sudoers` can be edited with **visudo**  
   * basic structure of entry: `who where = (as_whom) what`  
   * example: `$ sudo cat /etc/sudoers.d/student`
      > student ALL=(ALL) NOPASSWD: ALL
   * preference is to add a file to `/etc/sudoers.d` instead

#### Command Logging
* **sudo** commands and failures are logged in:  
   * `/var/log/auth.log` in Debian
   * `/var/log/messages` and/or `/var/log/secure` on other systems
* entries contain:  
   * calling username  
   * terminal info  
   * working directory  
   * user account invoked  
   * command with arguments  
* example: `$ sudo whoami` results in a log file entry like:
   > Dec 8 14:20:47 server1 sudo: op : TTY=pts/6 PWD=/var/log USER=root COMMAND=/usr/bin/whoami

#### Process Isolation
* **isolation**: one process normally cannot access the resources of another process, even when that process is running with the same user privileges  
* other security mechanisms:  
   * **Control Groups (cgroups)**: allows system admins to group processes and associate finite resources to each cgroup  
   * **Linux Containers (LXC)**: makes it possible to run multiple isolated Linux systems (containers) on a single system by relying on cgroups  
   * **Virtualization**: hardware is emulated in such a way that not only processes can be isolated, but entire systems are run simultaneously as isolated and insulated guests (virtual machines) on one physical host  

#### Hardware Device Access
* Linux limits user access to non-networking hardware devices in a manner that is extremely similar to regular file access using device nodes  
* hard disks, for example, are represented as `/dev/sd*`  
* a root user can read and write to the disk in a **raw** fashion, like `$ echo hello world > /dev/sda1`, but normal users cannot  
* applications write to disks through the filesystem, not through direct access to the device node  

#### Keeping Current
* many of the most successful attack vectors come from exploiting security holes for which fixes are already known but not universally deployed  
* the best practice is to take advantage of your Linux distribution's mechanism for automatic updates and never postpone them; it is extremely rare that such an update will cause new problems  

#### Lab 18.3: Sudo
```
1. Create a new user, using useradd, and give the user an initial password with passwd.
2. Configure this user to be able to use sudo.
3. Login as or switch to this new user and make sure you can execute a command that requires root privilege.

    For example, a trivial command requiring root privilege could be:

    $ ls /root
```
* My solution:  
   * `$ sudo useradd testuser`  
   * `$ sudo passwd testuser`  
   * switch to root and copy student file in /etc/sudoers.d to testuser and edit it with vim (kinda ugly way of doing it - should have used echo or cat or something)  
   * `$ su - testuser`  
   * I was able to run `$ ls /root` as testuser  
* Lab solution:  
```
1. sudo useradd newuser
sudo passwd newuser    
(give the password for this user when prompted)

2. With root privilege, (use sudo visudo) add this line to /etc/sudoers:
newuser      ALL=(ALL)     ALL
Alternatively, create a file named /etc/sudoers.d/newuser with just that one line as content.

3. You can login by doing:
sudo su newuser
or
ssh newuser@localhost
which will require giving newuser's password, and is probably a better solution. Instead of localhost you can give your hostname, IP address or 127.0.0.1. Then as newuser just type:
```

### 18.4: Working with passwords
----
#### How Passwords are Stored
* encrypted passwords used to be in `/etc/passwd`  
* now in `/etc/shadow` with only root access  

#### Password Algorithm
* most commonly **SHA-512**  
* example of using the algorithm: `$ echo -n test | sha512sum`  
   > ee26b0dd4af7e749aa1a8ee3c10ae9923f618980772e473f8819a5d4940e0db27ac185f8a0e1d5f84f88bc887fd67b143732c304cc5fa9ad8e6f57f50028a8ff  -  
   
#### Good Password Practices
Some techniques to secure passwords and data (are these all current??):
* password aging using **chage**  
* **Pluggable Authentication Modules (PAM)** using library called **pam_cracklib.so** or **pam_passwdqc.so**  
* install a password cracking program (like John The Ripper) to detect weak passwords on the system

#### Lab 18.4: Password Aging
```
With the newly created user from the previous exercise, look at the password aging for the user.

Modify the expiration date for the user, setting it to be something that has passed, and check to see what has changed.

When you are finished and wish to delete the newly created account, use userdel, as in:

$ sudo userdel newuser
```
* My solution:  
```
$ chage --list testuser
chage: Permission denied.
[student@centos ~]$ sudo chage --list testuser
Last password change					: Jan 18, 2019
Password expires					: never
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
[student@centos ~]$ sudo chage -E $(date -d -2days +%Y-%m-%d) testuser
[student@centos ~]$ sudo chage --list testuser
Last password change					: Jan 18, 2019
Password expires					: never
Password inactive					: never
Account expires						: Jan 19, 2019
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
[student@centos ~]$ su - testuser
Password: 
Your account has expired; please contact your system administrator
su: User account has expired
[student@centos ~]$ sudo userdel testuser
```
* Lab solution:  
```
chage --list newuser
sudo chage -E 2014-31-12 newuser
chage --list newuser
sudo userdel newuser   

Note: The example solution works in the US. chage -E uses the default date format for the local keyboard setting. 
```

### 18.5: Securing the Boot Process and Hardware Resources
----
#### Requiring Boot Loader Passwords
* use **GRUB** version 2 (reference: https://help.ubuntu.com/community/Grub2/Passwords)  

#### Hardware Vulnerability
Physical access can make all operating system level recommendations irrelevant. Example issues:  
* key logging  
* network sniffing  
* booting with a live or rescue disk  
* remounting and modifying disk content  

Guidelines:  
* lock down workstations and servers  
* protect your network links such that it cannot be accessed by people you do not trust  
* protect your keyboards where passwords are entered to ensure the keyboards cannot be tampered with  
* ensure a password protects the BIOS in such a way that the system cannot be booted with a live or rescue DVD or USB key  

#### Software Vulnerability

### 18.6 Summary
----
You have completed this chapter. Let’s summarize the key concepts covered:  
* The **root** account has authority over the entire system.  
* **root** privileges may be required for tasks, such as restarting services, manually installing packages and managing parts of the filesystem that are outside your home directory.  
* In order to perform any privileged operations such as system-wide changes, you need to use either **su** or **sudo**.  
* Calls to **sudo** trigger a lookup in the `/etc/sudoers` file, or in the `/etc/sudoers.d` directory, which first validates that the calling user is allowed to use **sudo** and that it is being used within permitted scope.  
* One of the most powerful features of **sudo** is its ability to log unsuccessful attempts at gaining root access.  By default, **sudo** commands and failures are logged in `/var/log/auth.log` under the **Debian family and `/var/log/messages` in other distribution families.  
* One process cannot access another process’ resources, even when that process is running with the same user privileges.  
* Using the user credentials, the system verifies the authenticity and identity.  
* The SHA-512 algorithm is typically used to encode passwords. They can be encrypted, but not decrypted.  
* Pluggable Authentication Modules (**PAM**) can be configured to automatically verify that passwords created or modified using the **passwd** utility are strong enough (what is considered strong enough can also be configured).  
* Your IT security policy should start with requirements on how to properly secure physical access to servers and workstations.  
* Keeping your systems updated is an important step in avoiding security attacks.  

PATHS  
18.1  
`/etc/passwd`  
18.3  
`/etc/sudoers`  
`/etc/sudoers.d`  
`/var/log/secure`  
`/var/log/auth.log` (Debian)  
`/var/log/messages` or `/var/log/secure` (other systems)  
18.4  
`/etc/shadow`  

COMMANDS  
18.1  
`$ last`  
18.3  
`$ su`  
`$ sudo`  
18.3  
`$ visudo`  
`$ sudo cat /etc/sudoers.d/student`  
`$ sudo whoami`  
`$ echo hello world > /dev/sda1`  
`$ sudo useradd testuser`  
`$ sudo passwd testuser`  
`$ su - testuser`  
`$ ls /root`  
`$ sudo su newuser`  
`$ ssh newuser@localhost`  
18.4  
`$ echo -n test | sha512sum`  
`$ sudo userdel newuser`  
`$ chage --list testuser`  
`$ sudo chage --list testuser`  
`$ sudo chage -E $(date -d -2days +%Y-%m-%d) testuser`  
`$ sudo chage --list testuser`  
`$ su - testuser`  
`$ sudo userdel testuser`  
`$ chage --list newuser`  
`$ sudo chage -E 2014-31-12 newuser`  
`$ chage --list newuser`  
`$ sudo userdel newuser`  
