Chapter 30: User Account Management
-----------------------------------

[30.3: Learning Objectives](#303-learning-objectives)  
[30.4: User Accounts](#304-user-accounts)  
[30.5: Attributes of a User Account](#305-attributes-of-a-user-account)  
[30.6: Creating User Accounts with useradd](#306-creating-user-accounts-with-useradd)  
[30.7: Modifying and Deleting User Accounts](#307:-modifying-and-deleting-user-accounts)  
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
    * username: the unique name assigned to each user
    * password: either the hashed password assigned to the user if `/etc/shadow` is not used or a placeholder, "x", if `/etc/shadow` is used
    * user identification number (UID): a unique number assigned to the user account; the UID is used by the system for a variety of purposes, including a determination of user privileges and activity tracking
    * group identification number (GID): indicates the primary, principal, or default group of the user
    * comment or GECOS information: a defined method to use the comment field for contact information (full name, email, office, contact number); (don't worry about what GECOS means, it is a very old term)
    * home directory: for most users, this is a unique directory that offers a working area for the user; normally, this directory is owned by the user, and except for root will be found on the system somewhere under `/home`
    * login shell: normally, this is a shell program such as `/bin/bash` or `/bin/csh`; sometimes, however, an alternative program is referenced here for special cases; in general, this field will accept any executable
  
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
* defaults can be overruled by using **useradd** options as in:
    * `$ sudo useradd -s /bin/csh -m -k /etc/skel -c "Bullwinkle J Moose" bmoose`
  
### 30.7: Modifying and Deleting User Accounts
----
* `$ sudo userdel morgan` delete the user **morgan**
    * all references will be removed from `/etc/passwd`, `/etc/shadow`, and `/etc/group`
    * it does not delete the home directory, usually `/home/morgan`
    * the **-r** option will cause the home directory to be removed, too, but other files on the system owned by the user will remain
* **usermod** is used to change characteristics of a user account, such as group memberships, home directory, login name, password, default shell, user id, etc.
* `$ sudo usermod -L dexter` locks **dexter**'s account 
  
### 30.8: Locked Accounts
----
* **locked** accounts can run programs but can never login and have no valid password associated with them
* **bin**, **daemon**, and **sys**, for example, are locked system accounts, with entries in `/etc/passwd` like: **daemon:x:2:2:daemon:/sbin:/sbin/nologin** (44 on my system)
* the **nologin** shell returns the following if a locker user tries to login to the system: "This account is currently not available." or whatever message may be stored in `/etc/nologin.txt`
* ways to lock an account:
    * `$ sudo usermod -L dexter` locks **dexter**'s account; do this when someone leaves the organization or takes an extended leave of absense
    * `$ sudo chage -E 2014-09-11 morgan` lock **morgan**'s account by changing the expiration date to a date in the past
    * replace the user's hashed password with `!!` or another invalid string in `/etc/shadow`
    * use **passwd**
    * use **vipw** to change last field in `/etc/passwd` to `/sbin/nologin`
* `$ sudo usermod -U dexter` unlocks **dexter**'s account
  
### 30.9: User IDs and /etc/passwd
----
* UID < 1000 is special and belongs to the system, usually
* UID >= 1000 include normal user accounts
* the value is defined in `/etc/login.defs`; mine:
    ```
    # Min/max values for automatic uid selection in useradd
    #
    UID_MIN                  1000
    UID_MAX                 60000
    # System accounts
    SYS_UID_MIN               201
    SYS_UID_MAX               999
    ```
* by default, the GID is the same number as the UID
* **Primary Group ID**s are sometimes called **User Private Groups** (**UPG**)
* don't edit `/etc/passwd`, `/etc/group`, or `/etc/shadow` directly; use utilities such as **usermod** or **vipw**
  
### 30.10: Why Use /etc/shadow?
----
* `/etc/shadow` enables password aging on a per user basis and maintains greater security of hashed passwords
    * default permissions of `/etc/passwd` are **644**, so anyone can read the file
    * the problem is that that hashed passwords can possibly be determined by utilities like **Crack** and **John the Ripper**
    * `/etc/shadow` has permission settings of **400**, so only root can read the file
* ? Isn't it pretty easy to just use **sudo**? I guess that needs to be locked down somewhat, too.
  
### 30.11: /etc/shadow
----
* the file `/etc/shadow` contains one line per user, like:
    ```
    daemon:*:16141:0:99999:7:::
    .....
    beav:$6$iCZyCnBJH9rmq7P.$RYNm10Jg3wrhAtUnahBZ/mTMg.RzQE6iBXyqaXHvxxbKTYqj.d
       9wpoQFuRp7fPEE3hMK3W2gcIYhiXa9MIA9w1:16316:0:99999:7:::
    ```
* the colon-separated fields are:
    * username: unique user name
    * password: the hashed (sha512) value of the password; "$6$" followed by an 8 character salt value, followed by a "$" and an 88 character password hash
    * lastchange: days since Jan 1,1970 that password was last changed
    * mindays: minimum days before password can be changed
    * maxdays: maximum days after which password must be changed
    * warn: days before password expires that the user is warned
    * grace: days after password expires that account is disabled
    * expire: date that account is/will be disabled
    * reserved: reserved field.
  
### 30.12: Password Management
----
* **passwd** is used to change passwords
    * normal user can change their own; cannot set bad passwords
    * root user can change any and is not prompted the current one; can set bad passwords
* the password choice is examined by **pam_craklib.so**
  
### 30.13: chage: Password Aging
----
* periodic password change is considered a good practice, but it it's too often, user's write down their passwords, which is probably worse
* **chage** is used for password aging; only root can use it except for `$ chage -l USERNAME`
* `$ chage [-m mindays] [-M maxdays] [-d lastday] [-I inactive] [-E expiredate] [-W warndays] user` generic syntax for chage
* examples:
    * `$ sudo chage -l dexter` list account aging information for dexter
    * `$ sudo chage -m 14 -M 30 kevlin` set the mindays and maxdays, respectively, for kevlin
    * `$ sudo chage -E 2012-4-1 morgan` set the password expiration for morgan to 2012-4-1
    * `$ sudo chage -d 0 USERNAME` force a user to change their password at next login
* normal users can run `$ chage -l USERNAME` for themselves; mine:
    ```
    Last password change					: never
    Password expires					: never
    Password inactive					: never
    Account expires						: never
    Minimum number of days between password change		: 0
    Maximum number of days between password change		: 99999
    Number of days of warning before password expires	: 7
    ```
  
### 30.14: Restricted shell
----
* `$ bash -r` invokes a **restricted shell** (some distros use **rbash**)
* a restricted shell functions in a more tightly controlled environment than a standard shell, but otherwise functions normally; for instance, it:
    * prevents the user from using cd to change directories
    * prevents the user from redefining the following environment variables: **SHELL**, **ENV**, and **PATH**
    * does not permit the user to specify the absolute path or executable command names starting from `/`
    * prevents the user from redirecting input and/or output
* because the restricted shell executes `$HOME/.bash_profile` without restriction, the user must have neither write nor execute permission on the `/home` directory
* ? why?
  
### 30.15: Restricted Accounts
----
* when a user needs access, a restricted user account can be useful; a **restricted account**, or **limited account**:
    * uses the restricted shell
    * limits available system programs and user applications
    * limits system resources
    * limits access times
    * limits access locations
* how to set it up:
    1. `$ cd /bin ; sudo ln -s bash rbash` create symbolic link from bash to rbash
    2. `$ cd /bin ; sudo ln bash rbash` create a link from bash to rbash
    3. `$ cd /bin ; sudo cp bash rbash` copies bash to rbash??
    4. use `/bin/bash` as the shell in `/etc/passwd`
  
### 30.16: The root Account
----
* use only for admin purposes and only when absolutely necessary
* `/etc/ssh/sshd_config` configures **ssh**
* `/etc/securetty` file and **pam_securetty.so** file are used with **PAM** (**P**luggable **A**uthentication **M**odules)
* **auditd** can be used to log all commands executed as root
  
### 30.17: SSH
----
* **SSH** (**S**ecure **SH**ell) allows remote access; examples:
    * `$ ssh farflung.com` login to site assuming an existing user account
    * `$ ssh root@farflung.com` login to site as root
    * `$ ssh -l root farflung.com` login to site as root
* **scp** is used to securely copy files over the network; examples:
    * `$ scp file.txt farflung.com:/tmp` copies file from current dir to remote one
    * `$ scp file.txt student@farflung.com/home/student` copies file from current dir to remote home dir
    * `$ scp -r some_dir farflung.com:/tmp/some_dir` copies local dir to remote dir
* `$ for machines in node1 node2 node3 ; do (ssh $machines some_command &) ; done` (I think) to run a command on multiple machines simultaneously
  
### 30.18: SSH Configuration Files
----
* `~/.ssh` SSH configuration directory; contains:
    * **id_rsa**: user's private encryption key
    * **id_rsa.pub**: user's public encryption key
    * **authorized_keys**: list of public keys that are permitted to login
    * **known_hosts**: list of hosts from which logins have been allowed in the past
    * **config**: configuration file for specifying various options
* `$ ssh-keygen` create private and public encryption keys
* the public key can be given to any machine with which you want to permit password-less access; it should also be added to your `~/.ssh/authorized_keys` file, together with all the public keys from other users who have accounts on your machine and you want to permit password-less access to their accounts
* `~.ssh/authorized_keys` contains information about users and machines
* `~.ssh/known_hosts` only contains information about computer nodes
  
### 30.19: Remote Graphical Login
----
* use VNC (Virtual Network Computing), such as **tigervnc**
* examples follow, but I'm not making notes
  
### 30.20: Creating, Modifying, and Removing a New User Account Demo
----
See video.
  
### Lab 30.1: Working with User Accounts
----
1. Examine `/etc/passwd` and `/etc/shadow`, comparing the fields in each file, especially for the normal user account. What is the same and what is different?
    * similar: they are both formatted similarly and have user accounts
    * different: they store different kinds of information
    * solution: `$ sudo grep student /etc/passwd /etc/shadow` grep two files for 'student'
2. Create a `user1` account using **useradd**.
    * `$ sudo useradd user1`
3. Login as `user1` using **ssh** . You can just do this with: `$ ssh user1@localhost` It should fail because you need a password for `user1` ; it was never established.
    * it does fail
    * solution: you might need to start **sshd** service first, as in `$ sudo service sshd restart` or `$ sudo systemctl restart sshd.service`
4. Set the password for `user1` to `user1`pw and then try to login again as `user1`.
    * `$ sudo passwd user1`
5. Look at the new records which were created in the `/etc/passwd`, `/etc/group`, and the `/etc/shadow` files.
    * `user1:x:1001:1001::/home/user1:/bin/bash`
    * `user1:x:1001:`
    * `user1:$6$RwwPtAbU$UG3tSWMVG9MIEsiOPHbJM7783osdGoTJoFz/fjIIfZomB0Pee8w/TVzzHXIEy960tIIIJAGLlpIHtaI.K4vWy0:18048:0:99999:7:::`
6. Look at the `/etc/default/useradd` file and see what the current defaults are set to. Also look at the `/etc/login.defs` file.  Here's the former:
    ```
    # useradd defaults file
    GROUP=100
    HOME=/home
    INACTIVE=-1
    EXPIRE=
    SHELL=/bin/bash
    SKEL=/etc/skel
    CREATE_MAIL_SPOOL=yes
    ```
7. Create a user account for `user2` which will use the **Korn** shell (**ksh**) as its default shell. (if you do not have `/bin/ksh` install it or use the C shell at `/bin/csh`.) Set the password to `user2pw`.
    * `$ sudo useradd -s /bin/ksh user2`
    * `$ sudo passwd user2`
8. Look at `/etc/shadow`. What is the current expiration date for the `user1` account?
    * expiration is the penultimate field; there is no expiration set
9. Use **chage** to set the account expiration date of `user1` to December 1, 2013. Look at `/etc/shadow` to see what the new expiration date is.
    * `$ sudo chage -E 2013-12-01 user1`
    * `...pIHtaI.K4vWy0:18048:0:99999:7::16040:` (16040)
10. Use usermod to lock the `user1` account. Look at `/etc/shadow` and see what has changed about `user1`’s password. Reset the password to `userp1` on the account to complete this exercise.
    * `$ sudo usermod -L user1`
    * `user1:!$6$RwwPtAbU$UG3tSWM...`
  
### Lab 30.2: Restricted Shells and Accounts
----
1. Start a restricted still in your current window with: `$ bash -r` Try elementary options such as resetting the path or changing directories.
    * `$ echo hello > hello.txt`
        > bash: hello.txt: restricted: cannot redirect output  
        > bash: /dev/null: restricted: cannot redirect output  
    * `$ mkdir new_dir` (it did create the new directory, though)
        > bash: /dev/null: restricted: cannot redirect output  

2. Set up a restricted account and verify its restricted nature, then clean up.
    * (I didn't to this)
    * `# sudo ln /bin/bash /bin/rbash`
    * `# sudo useradd -s /bin/rbash fool`
    * `# sudo passwd fool`
    * `# sudo su - fool`
    * `[fool@c7 ~]$ cd /tmp`
        > -rbash: cd: restricted  
    * `[fool@c7 ~]$ PATH=$PATH:/tmp`
        > -rbash: PATH: readonly variable  
    * `[fool@c7 ~]$ exit`
        > logout  
    * `# sudo userdel -r fool`
    * `# sudo rm /bin/rbash`
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
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

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
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
