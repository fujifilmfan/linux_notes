User and Group Management
-----

### Chapters: LFS101: 12, 18, LFS201: 30, 31, 32, 33

* show current user - `$ whoami`
* show info about current user - `$ id`
* show group membership of current user - `$ groups`
* show logged-on users - `$ who` or `$ who -a`
* remove user from group - `$ sudo /usr/sbin/usermod -G rjsquirrel rjsquirrel` (any groups not in list will be removed)
* add user bjmoose - `$ sudo useradd bjmoose`
* login as bjmoose with ssh - `$ ssh bjmoose@localhost` (fails); also, start sshd with `$ sudo service sshd restart` or `$ sudo systemctl restart sshd.service`
* look at the new records which were created in the `/etc/passwd`, `/etc/group`, and the `/etc/shadow` files
* look at the `/etc/default/useradd` file and see what the current defaults are set to; also look at the `/etc/login.defs` file
* add user bmoose w/ zsh shell and a comment - `$ sudo useradd -s /bin/zsh -m -k /etc/skel -c "Bullwinkle J Moose" bmoose`
* give bjmoose a password - `$ sudo passwd bjmoose`
* view password aging for bjmoose - `$ sudo chage --list bjmoose` or `$ sudo chage -l bjmoose`
* force bjmoose to change password at next login - `$ sudo chage -d 0 bjmoose`
* modify the expiration date for the user, setting it to be something that has passed, and check to see what has changed - `$ sudo chage -E 2014-31-12 newuser` or `$ sudo chage -E $(date -d -2days +%Y-%m-%d) bjmoose`
* try logging in as bjmoose - `S su - bjmoose`
* give bjmoose sudo privileges - add `newuser      ALL=(ALL)     ALL` to `/etc/sudoers` or create a file in `/etc/sudoers.d/bjmoose` with that line
* login as bjmoose and execute a command requiring root privilege, like `$ ls /root` - `$ su - bjmoose` or `$ sudo su bjmoose` or `$ ssh bjmoose@localhost`
* lock bjmoose's account - `$ sudo usermod -L bjmoose` or `$ sudo chage -E 2014-09-11 bjmoose`
* unlock bjmoose's account - `$ sudo usermod -U bjmoose`
* show info about another user - `$ id <other_user>`
* show group membership of another user - `$ groups <other_user>`
* add user to existing group - `$ sudo /usr/sbin/usermod -a -G anewgroup rjsquirrel` (-a for append)
* delete user bjmoose - `$ userdel bjmoose` or `$ userdel -r bjmoose` to remove home dir, too
* add a group anewgroup - `$ sudo /usr/sbin/groupadd anewgroup`  
* delete a group anewgroup - `$ sudo /usr/sbin/groupdel anewgroup`  
* start a restricted shell - `$ bash -r`
### 12.4: File Permissions
----
#### File Ownership
* every file is associated with a user who is the **owner**
* every file also associated with a **group**
  
Command | Usage
`chown` | Used to change user ownership of a file or directory
`chgrp` | Used to change group ownership
`chmod` | Used to change the permissions on the file, which can be done separately for **owner**, **group** and the rest of the world (often named as **other**.)
  
* `$ chmod uo+x,g-w somefile`  
* show failed command attempts due to security - `$ cat /var/log/secure`
* edit the sudoers file - `$ sudo visudo` (`who where = (as_whom) what`), better to add a file to `/etc/sudoers.d` (research this)

* create private and public encryption keys - `$ ssh-keygen` 
  
  
### Lab 30.1: Working with User Accounts
----
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


### Ch.30
* Lab 30.1
    7. Create a user account for user2 which will use the Korn shell (ksh) as its default shell. (if you do not have /bin/ksh install it or use the C shell at /bin/csh.) Set the password to user2pw.
    ```
    7. $ sudo useradd -s /bin/ksh user2
    $ sudo passwd user2
    Changing password for user user2.
    New password:
    ```
    8. Look at /etc/shadow. What is the current expiration date for the user1 account?
    ```
    8. $ sudo grep user1 /etc/shadow
    user1:$6$OBE1mPMw$CIc7urbQ9ZSnyiniVOeJxKqLFu8fz4whfEexVem2TFpucuwRN1CCHZ
    19XGhj4qVujslRIS.P4aCXd/y1U4utv.:16372:0:99999:7:::
    There should be no expiration date.
    ```
    9. Use chage to set the account expiration date of user1 to December 1, 2013.
    Look at /etc/shadow to see what the new expiration date is.
    ```
    9. $ sudo chage -E 2013-12-1 user1
    $ sudo grep user1 /etc/shadow
        user1:$6$OBE1mPMw$CIc7urbQ9ZSnyiniVOeJxKqLFu8fz4whfEexVem2TFpucuwRN1CCHZ
    19XGhj4qVujslRIS.P4aCXd/y1U4utv.:16372:0:99999:7::16040:
    ```
    10. Use usermod to lock the user1 account.
    Look at /etc/shadow and see what has changed about user1’s password. Reset the password to userp1 on the account to complete this exercise.
    ```
    10. $ sudo usermod -L user1
    $ sudo passwd user1
    ```
  
### 31.5: Group Management
----
* `$ groupadd ...` adds a new group
* `$ groupdel ...` removes a group
* `$ groupmod ...` modifies a group's properties
* `$ usermod ...` can be used to modify a user's group memberships
* `$ vigr ...` can edit `/etc/group`; it's generally symbolically linked to the **vipw** utility
* examples:
    * `$ sudo groupadd -r -g 215 staff` creates a system group (**-r**) and assigns it the GID "215" and name "staff" (?)
    * `$ sudo groupmod -g 101  blah` changes the group ID of "blah" (?)
    * `$ sudo groupdel newgroup` deletes the group "newgroup"
    * `$ sudo usermod -G student,group1,group2 student` assign "student" to the comma-separated list of groups; the user will be removed from any groups not listed unless **-a** option is used
    * `$ useradd -G <group name>` adds a user to an existing group when creating the user
  
### 31.6: User Private Groups
----
* the idea of UPGs is that each user has their own group, but other users can be added, so they are not guaranteed to be private
* user created with **useradd** have primary GID = UID, and the group name is the same as the user name
* `/etc/profile` contains system-wide environment and startup programs
    * **umask** set to 002 for all users created with UPG, so user files are created with permissions 664 and directories with 775
    * see Chapter 32 for notes on **umask**
* `/etc/bashrc` contains system-wide functions and aliases
  
### 31.7: Group Membership
----
* a user has one **primary** group, listed in `/etc/passwd` and in `/etc/group`
*  a user may belong to 0 - 15 **secondary** groups
* the primary group is the GID that is used whenever the user creates files or directories
* `$ groups [USER1 USER2 ...]` identify group membership for users
* `$ id -Gn [USER1 USER2 ...]` identify group membership for users
* `$ groups` or `$ id` to see my own group membership
* examples from my machine:
    * `$ groups`
        ```
        student
        ```
    * `$ id`
        ```
        uid=1000(student) gid=1000(student) groups=1000(student)
        ```
    * `$ groups student`
        ```
        student : student
        ```
    * `$ id student`
        ```
        uid=1000(student) gid=1000(student) groups=1000(student)
        ```
    * `$ id -G student`
        ```
        1000
        ```
    * `$ id -n student`
        ```
        id: cannot print only names or real IDs in default format
        ```
    * `$ id -Gn student`
        ```
        student
        ```
  
### 31.9: Lab 31.1: Working with Groups
----
1. Create two new user accounts (`rocky` and `bullwinkle` in the below) and make sure they have home directories.
    * `$ sudo useradd rocky`
    * `$ sudo useradd bullwinkle`
2. Create two new groups, `friends` and `bosses` (with a GID of 490). Look at the `/etc/group` file. See what GID was given to each new group.
    * `$ sudo groupadd friends`
    * `$ sudo groupadd -g 490 bosses`
    * `$ cat /etc/group`
        ```
        friends:x:1003:
        bosses:x:490:
        ```
3. Add `rocky` to both new groups. Add `bullwinkle` to group `friends`. Look in the `/etc/group` file to see how it changed.
    * `$ sudo usermod -G friends,bosses rocky`
    * `$ sudo usermod -G friends bullwinkle`
4. Login as `rocky`. Create a directory called `somedir` and set the group ownership to `bosses`. (Using **chgrp** which will be discussed in the next session.) (You will probably need to add execute privileges for all on `rocky`’s home directory.)
    * `$ sudo chmod +x rocky`
    * `$ mkdir bosses`
    * `$ chgrp bosses somedir`
5. Login as `bullwinkle` and try to create a file in `/home/rocky/somedir` called `somefile` using the **touch** command. Can you do this? No, because of the group ownership and the `chmod a+x` on the directory.
    * "Permission denied"
6. Add `bullwinkle` to the `bosses` group and try again. Note you will have to log out and log back in again for the new group membership to be effective.
    * `$ sudo usermod -G bosses bullwinkle` (this took bullwinkle out of 'friends' - remember to list all groups or use **-a** for append)
    * it worked; the computer CPU was >400% usage with three VM users logged in, though, so the process was really slow; I should have done `$ ssh rocky@localhost`!

### Ch.31
* Lab 31.1
    1. Create two new user accounts (rocky and bullwinkle in the below) and make sure they have home directories.
    ```
    1. $ sudo useradd -m rocky
    $ sudo useradd -m bullwinkle $ sudo passwd rocky
        Enter new UNIX password:
        Retype new UNIX password:
        passwd: password updated successfully
        $ sudo passwd bullwinkle
        Enter new UNIX password:
        Retype new UNIX password:
        passwd: password updated successfully
    $ ls -l /home
        total 12
        drwxr-xr-x  2 bullwinkle bullwinkle 4096 Oct 30 09:39 bullwinkle
        drwxr-xr-x  2 rocky      rocky      4096 Oct 30 09:39 rocky
        drwxr-xr-x 20 student    student    4096 Oct 30 09:18 student
    ```
    2. Create two new groups, friends and bosses (with a GID of 490). Look at the /etc/group file. See what GID was
    given to each new group.
    ```
    2. $ sudo groupadd friends
    $ sudo groupadd -g 490 bosses
    $ grep -e friends -e bosses /etc/group
        friends:x:1003:
        bosses:x:490:
    ```
    3. Add rocky to both new groups.
    Add bullwinkle to group friends.
    Look in the /etc/group file to see how it changed.
    ```
    3. $ sudo usermod -G friends,bosses rocky $ sudo usermod -G friends bullwinkle
        $ grep -e rocky -e bullwinkle /etc/group
    rocky:x:1001:
    bullwinkle:x:1002:
    friends:x:1003:rocky,bullwinkle
    bosses:x:490:rocky
    $ groups rocky bullwinkle
    rocky : rocky friends bosses
    bullwinkle : bullwinkle friends
    ```
    4. Login as rocky. Create a directory called somedir and set the group ownership to bosses. (Using chgrp which will be discussed in the next session.)
    (You will probably need to add execute privileges for all on rocky’s home directory.)
    ```
    4. $ ssh rocky@localhost
    $ cd ~
    $ mkdir somedir
    $ chgrp bosses somedir
    $ ls -l
    total 16
    -rw-r--r-- 1 rocky rocky  8980 Oct  4  2013 examples.desktop
    drwxrwxr-x 2 rocky bosses 4096 Oct 30 09:53 somedir
    $ chmod a+x .
    ```
    5. Login as bullwinkle and try to create a file in /home/rocky/somedir called somefile using the touch command.
    Can you do this? No, because of the group ownership and the chmod a+x on the directory.
    ```
    5. $ ssh bullwinkle@localhost
    $ touch /home/rocky/somedir/somefile
    touch: cannot touch /home/rocky/somedir/somefile: Permission denied
    $ exit
    ```
    6. Add bullwinkle to the bosses group and try again. Note you will have to log out and log back in again for the new
    group membership to be effective. do the following:
    ```
    6. $ sudo usermod -a -G bosses bullwinkle
    $ ssh bullwinkle@localhost
    $ touch /home/rocky/somedir/somefile
    $ ls -al /home/rocky/somedir
    (note ownership of files)
    ```
### 32.9: chown and chgrp 
----
* **chown** is used to change file ownership; only the superuser can do this
    * `$ chown wally somefile` makes wally the owner of the file
* **chgrp** is used to change group ownership; you can only change group ownership to groups you are a member of
    * `$ chgrp cleavers somefile` makes cleavers the group owner of the file
* more examples:
    * `$ chown wally:cleavers somefile` changes both owner and group at the same time
    * `$ chown -R wally:cleavers ./` changes owner and group recursively
    * `$ chown -R wally:wally subdir` changes owner and group recursively
  
### 32.10: umask
----
* defaults when creating a file:
    * read/write for owner, group, and world (**0666**) for files
    * read/write/execute for everyone (**0777**) for directories
* however, it often ends up as **664** for the file and **775** for the directory due to the **umask**
* `$ umask` shows which permissions should be denied (0002 on my machine)
    * it's not exactly subtraction - "technically, the mask is negated (its bitwise compliment is taken) and this value is then applied to the default permissions using a logical AND operation" [Linux umask command](https://www.computerhope.com/unix/uumask.htm)
    * 0666 & ~002 = 0664 and 0777 & ~002 = 0775
* `$ umask 0022` changes the umask value
  
### 32.11: umask Demo
----
  
### 32.12: Filesystem ACLs
----
* **ACL**s (**A**ccess **C**ontrol **L**ists) extend the simpler user / group / world and read / write / execute model
* certain privileges can be granted to users or groups without using 777 permissions
* use the option **-acl** when mounting
  
### 32.13: Getting and Setting ACLs
----
* `$ getfacl <file|directory>` see ACLs
    * `$ getfacl test`
        ```
        # file: test
        # owner: student
        # group: student
        user::rw-
        group::rw-
        other::rw-
        ```
* `$ setfacl <options> <permissions> <file|directory>` set ACLs
    * `$ setfacl -m u:isabelle:rx /home/stephane/file1` modifies ACL of a file or directory
    * `$ setfacl -x u:isabelle /home/stephane/file` removes ACL entries
    * `$ setfacl -m d:u:isabelle:rx somedir` sets default on a directory
* new files inherit the default ACL (if set) from the directory they reside in; `$ mv ...` and `$ cp -p ...` preserve ACLs
  
### Lab 32.1: Using chmod
----
It is possible to either give permissions directly, or add or subtract permissions. The syntax is pretty obvious. Try the following examples:  
* `$ chmod u=r,g=w,o=x afile`
* `$ chmod u=+w,g=-w,o=+rw afile`
* `$ chmod ug=rwx,o=-rw afile`
After each step do:  
* `$ ls -l afile`
to see how the permissions took, and try some variations.  
  
### Lab 32.2: umask
----
Create an empty file with:  
* `$ touch afile`
* `$ ls -l afile`
    > -rw-rw-r-- 1 coop coop 0 Jul 26 12:43 afile  
which shows it is created by default with both read and write permissions for owner and group, but only read for world.
In fact, at the operating system level the default permissions given when creating a file or directory are actually read/write for owner, group **and** world (0666); the default values have actually been modified by the current umask.
If you just type **umask** you get the current value:
* `$ umask`
    > 0002  
which is the most conventional value set by system administrators for users. This value is combined with the file creation permissions to get the actual result; i.e.,
`0666 & ~002 = 0664; i.e., rw-rw-r--`
Try modifying the umask and creating new files and see the resulting permissions, as in:
* `$ umask 0022`
* `$ touch afile2`
* `$ umask 0666`
* `$ touch afile3`
* `$ ls -l afile*`
  
### Lab 32.3: Using Access Control Lists
----
1. Create a file using your usual user name and run **getfacl** on it to see its properties.
    * ` $ getfacl somefile`
        ```
        # file: somefile
        # owner: student
        # group: student
        user::rw-
        group::rw-
        other::r--
        ```
2. Create a new user account with default properties (or reuse one from previous exercises.
    * I'll use rocky
3. Login as that user and try to add a line to the file you created in the first step. This should fail.
    * "Permission denied"
4. Use **setfacl** to make the file writeable by the new user and try again.
    * `$ setfacl -m u:rocky:rw /home/student/somefile`
        ```
        # file: somefile
        # owner: student
        # group: student
        user::rw-
        user:rocky:rw-
        group::rw-
        mask::rw-
        other::r--
        ```
    * writing still "Permission denied" for rocky
5. Use **setfacl** to make the file not readable by the new user and try again.
6. Clean up as necessary
    * `$ sudo userdel -r rocky` **-r** for home directory also
  

### Ch.32
* Lab 32.2
    1. Create an empty file with
    ```
    $ touch afile
    $ ls -l afile
    -rw-rw-r-- 1 coop coop 0 Jul 26 12:43 afile
    which shows it is created by default with both read and write permissions for owner and group, but only read for world.
    In fact, at the operating system level the default permissions given when creating a file or directory are actually read/write for
    owner, group and world (0666); the default values have actually been modified by the current umask.
    ```
    2. If you just type umask you get the current value
    ```
    $ umask
    0002
    which is the most conventional value set by system administrators for users. This value is combined with the file creation permissions to get the actual result; i.e.,
    0666 & ~002 = 0664; i.e., rw-rw-r--
    ```
    3. Try modifying the umask and creating new files and see the resulting permissions, as in:
    ```
    $ umask 0022
    $ touch afile2
    $ umask 0666
    $ touch afile3
    $ ls -l afile*
    ```
* Lab 32.3
    1. Create a file using your usual user name and run getfacl on it to see its properties.
    ```
    1. In window 1:
        $ echo This is a file > /tmp/afile
        $ getfacl /tmp/afile
        getfacl: Removing leading '/' from absolute path names
        # file: tmp/afile
        # owner: coop
        # group: coop
        user::rw-
        group::rw-
        other::r--
    ```
    2. Create a new user account with default properties (or reuse one from previous exercises.
    ```
    2. In window 1:
        $ sudo useradd fool
        $ sudo passwd fool
        ...
    ```
    3. Login as that user and try to add a line to the file you created in the first step. This should fail.
    ```
    3. In window 2:
        $ sudo su - fool
        $ echo another line > /tmp/afile
        -bash: /tmp/afile: Permission denied
    ```
    4. User setfacl to make the file writeable by the new user and try again.
    ```
    4. In window 1:
        $ setfacl -m u:fool:rw /tmp/afile
        $ getfacl /tmp/afile
        getfacl: Removing leading '/' from absolute path names
        # file: tmp/afile
        # owner: coop
        # group: coop
        user::rw-
        user:fool:rw-
        group::rw-
        mask::rwx
        other::r--
    In window 2:
    $ echo another line > /tmp/afile
    ```
    5. User setfacl to make the file not readable by the new user and try again.
    ```
    5. In window 1:
    $ setfacl -m u:fool:w /tmp/afile
    In window 2:
    $ echo another line > /tmp/afile
    -bash: /tmp/afile: Permission denied
    ```
    6. Clean up as necessary
    ```
    6. Cleaning up:
    $ rm /tmp/afile
    $ sudo userdel -r fool
    ```
### Lab 33.1: PAM Configuration
----
1. Edit `/etc/pam.d/sshd` and configure it to deny login after three failed attempts. Hint: add the following two lines to `/etc/pam.d/sshd`:
    * `auth required pam_tally2.so deny=3 onerr=fail`
    * `account required pam_tally2.so`
    * done
2. Try to login three times as a particular user (who has an account) while mistyping the password.
    * done
3. Try to login as the same user with the correct password.
    * still "Permission denied" as expected
4. Check to see how many failed logins there are for the user.
    * `$ sudo pam_tally2 -u rocky`
5. Reset the failed login counter.
    * `$ sudo pam_tally2 -u rocky -r`
6. Check again to see how many failed logins there are.
    ```
    Login           Failures Latest failure     From
    rocky               0 
    ```
7. Try to login again with the correct password.
    * works fine


### Extras
* Lab 30.2: restricted shells
* 30.15: setup a restricted account 
* Lab 32.1: chmod

* Lab 33.1: PAM configuration

