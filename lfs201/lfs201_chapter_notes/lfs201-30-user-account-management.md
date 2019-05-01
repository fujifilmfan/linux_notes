Chapter 30: User Account Management
-----------------------------------

[30.3: Learning Objectives](#303-learning-objectives)  
[30.4: User Accounts](#304-user-accounts)  
[30.5: Attributes of a User Account](#305-attributes-of-a-user-account)  
[30.6: Creating User Accounts with useradd](#306-creating-user-accounts-with-useradd)  
[30.7:: Modifying and Deleting User Accounts](#307:-modifying-and-deleting-user-accounts)  
[30.8: Locked Accounts](#308-locked-accounts)  
[30.9: User IDs and /etc/passwd](#309-user-ids-and-etcpasswd)  
[30.10: Why Use /etc/shadow?](#3010-why-use-etcshadow)  
[30.11: /etc/shadow](#3011-etcshadow)  
[30.12: Password Management](#3012-password-management)  
[30.13: chage: Password Aging](#3013-chage-password-aging)  
[30.14: Restricted shell](#3014-restricted-shell)  
[30.15: Restricted Accounts](#3015-restricted-accounts)  
[30.16: The root Account](#3016-the-root-account)  
[30.17: SSH](#3017-ssh)  
[30.18: SSH Configuration Files](#3018-ssh-configuration-files)  
[30.19: Remote Graphical Login](#3019-remote-graphical-login)  
[30.20: Creating, Modifying, and Removing a New User Account Demo](#3020-creating,-modifying,-and-removing-a-new-user-account-demo)  
[30.21: Knowledge Check 30](#3021-knowledge-check-30)  
[Lab 30.1: Working with User Accounts](#lab-301-working-with-user-accounts)  
[Lab 30.2: Restricted Shells and Accounts](#lab-302-restricted-shells-and-accounts)  
[Paths and Commands](#paths-and-commands)  
  
### 30.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the purpose of individual user accounts and list their main attributes.
* Create new user accounts and modify existing account properties, as well as remove or lock accounts.
* Understand how user passwords are set, encrypted and stored, and how to require changes in passwords over time for security purposes.
* Explain how restricted shells and restricted accounts work.
* Understand the role of the root account and when to use it.
* Use Secure Shell (**ssh**) and remote logins and commands.
  
### 30.4: User Accounts
----
* reasons for having individual user accounts include:
    * providing each user with their own individualized private space
    * creating particular user accounts for specific dedicated purposes
    * distinguishing privileges among users
* the **root** can do ***anything*** on the system
    * should only be used when absolutely necessary
* the **daemon** account exists for the purpose of allowing processes to run as a user other than root
  
### 30.5: Attributes of a User Account
----
* each user has a line in `/etc/passwd` that describes basic account attributes
* example:
    ```
    beav:x:1000:1000:Theodore Cleaver:/home/beav:/bin/bash
    warden:x:1001:1001:Ward Cleaver:/home/warden:/bin/bash
    dobie:x:1002:1002:Dobie Gillis:/home/dobie:/bin/bash
    ```
* elements in each row:
    * User name: the unique name assigned to each user
    * User password: the password assigned to each user
    * User identification number (UID): a unique number assigned to the user account; the UID is used by the system for a variety of purposes, including a determination of user privileges and activity tracking
    * Group identification number (GID): indicates the primary, principal, or default group of the user
    * Comment or GECOS information: a defined method to use the comment field for contact information (full name, email, office, contact number); (don't worry about what GECOS means, it is a very old term)
    * Home directory: for most users, this is a unique directory that offers a working area for the user; normally, this directory is owned by the user, and except for root will be found on the system somewhere under `/home`
    * Login shell: normally, this is a shell program such as `/bin/bash` or `/bin/csh`; sometimes, however, an alternative program is referenced here for special cases; in general, this field will accept any executable
  
### 30.6: Creating User Accounts with useradd
----
* `$ sudo useradd dexter` creates an account for user dexter, using default algorithms for user and group ID, home directory, and shell choice
* specifically, the following steps execute:
    * the next available UID greater than **UID_MIN** (specified in `/etc/login.defs`) by default is assigned as **dexter**'s **UID**
    * a group called **dexter** with a **GID=UID** is also created and assigned as **dexter**'s primary group
    * a home directory `/home/dexter` is created and owned by **dexter**
    * **dexter**'s login shell will be `/bin/bash`
    * the contents of `/etc/skel` is copied to `/home/dexter`; by default, `/etc/skel` includes startup files for bash and for the X Window system
    * an entry of either **!!**  is placed in the password field of the `/etc/shadow` file for **dexter**'s entry, thus requiring the administrator to assign a password for the account to be usable
* defaults can be overrule by using **useradd** options as in:
    * `$ sudo useradd -s /bin/csh -m -k /etc/skel -c "Bullwinkle J Moose" bmoose`
  
### 30.7:: Modifying and Deleting User Accounts
----
* `$ sudo userdel morgan` delete the user **morgan**
    * all references will be removed from `/etc/passwd`, `/etc/shadow`, and `/etc/group`
    * it does not delete the home directory, usually `/home/morgan`
    * the **-r** option will cause the home directory to be removed, too, but other files on the system owned by the user will remain
* **usermod** is used to change characteristics of a user account, such as group memberships, home directory, login name, password, default shell, user id, etc.
* `$ sudo usermod -L dexter` locks **dexter**'s account 
  
### 30.8: Locked Accounts
----

### 30.9: User IDs and /etc/passwd
----

### 30.10: Why Use /etc/shadow?
----

### 30.11: /etc/shadow
----

### 30.12: Password Management
----

### 30.13: chage: Password Aging
----

### 30.14: Restricted shell
----

### 30.15: Restricted Accounts
----

### 30.16: The root Account
----

### 30.17: SSH
----

### 30.18: SSH Configuration Files
----

### 30.19: Remote Graphical Login
----

### 30.20: Creating, Modifying, and Removing a New User Account Demo
----

### 30.21: Knowledge Check 30
----

### Lab 30.1: Working with User Accounts
----

### Lab 30.2: Restricted Shells and Accounts
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
accounts | `/etc/passwd` | file that describes basic account attributes | LFS201 30.5

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
