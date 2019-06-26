User and Group Management
-----

### Chapters: LFS101: 12, 18, LFS201: 30, 31, 32, 33

useradd -m to add home dir

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


* show failed command attempts due to security - `$ cat /var/log/secure`
* edit the sudoers file - `$ sudo visudo` (`who where = (as_whom) what`), better to add a file to `/etc/sudoers.d` (research this)
* create private and public encryption keys - `$ ssh-keygen` 
 
### Passwords
----
* create a user account for user2 which will use the **Korn** shell (**ksh**) as its default shell - `$ sudo useradd -s /bin/ksh user2`, `$ sudo passwd user2`
* what is the current expiration date for the user2 account? - look in penultimate field of `$ /etc/shadow`, no expiration
* set the account expiration date of user2 to December 1, 2013, verify - `$ sudo chage -E 2013-12-1 user2`, `$ cat /etc/shadow`
* use lock the user2 account, verify - `$ sudo usermod -L user2`, `$ cat /etc/shadow`
* reset the password for user2 - `$ sudo passwd user2`
* make user2 have to change the password on next login - 
  
### Groups
----
* show my groups - `$ groups`
* show my IDs - `$ id`
* confirm my group ID in two places - `$ grep -e student /etc/group /etc/passwd`
* create two new user accounts, rocky and bullwinkle, and make sure they have home directories - `$ sudo useradd -m rocky`, `$ sudo passwd ...`, `$ sudo useradd -m bullwinkle`, `$ sudo passwd ...`, `$ ls -al /home`
* create two new groups, `friends` and `bosses` (with a GID of 490) - `$ sudo groupadd friends`, `$ sudo groupadd -g 490 bosses`
* confirm the new group IDs - `$ cat /etc/group` or `$ grep -e friends -e bosses /etc/group`
* change the bosses group ID to 500 (if not used) - `$ sudo groupmod -g 500 bosses`
* add rocky to both new groups; add bullwinkle to group friends - `$ sudo usermod -G friends,bosses rocky`, `$ sudo usermod -G friends bullwinkle`
* see how the groups have changed - `$ cat /etc/group` or `$ grep -e friends -e bosses /etc/group`
* shows the groups for both rocky and bullwinkle - `$ groups rocky,bullwinkle`
* show the IDs for both rocky and bullwinkle - `$ id rocky`, `$ id bullwinkle`
* show just the group IDs for rocky and bullwinkle - `$ id -G rocky`, `$ id -G bullwinkle` (try to optimize)
* show the group names for rocky and bullwinkle - `$ id -Gn rocky`, `$ id -Gn bullwinkle` (try to optimize)
* create a new user and add them to friends - `$ sudo useradd -G friends -m moose`
* is the new user also part of their default group?
* login as rocky, create a directory called `somedir` with execute privileges for all, and set the group ownership to 'bosses' - `$ ssh rocky@localhost`, `$ chmod a+x .`, `$ mkdir ~/somedir`, `$ chgrp bosses somedir`
* login as `bullwinkle` and try to create a file in `/home/rocky/somedir` called `somefile` using the **touch** command; can you do this? - `$ ssh bullwinkle@localhost`, `$ touch /home/rocky/somedir/somefile`, no
* add 'bullwinkle' to the 'bosses' group and try again - `$ sudo usermod -a -G bosses bullwinkle`, `$ ssh bullwinkle@localhost`, `$ touch /home/rocky/somedir/somefile`, `$ ls -al /home/rocky/somedir` (need to log out and log in again)
* delete the users and the groups - `$ sudo userdel ...`, `$ sudo groupdel friends`
* create a system group with name staff and GID 215 - `$ sudo groupadd -r -g 215 staff`

### chown and chgrp 
----
* change user ownership of a file - `$ sudo chown <user> somefile`
* change group ownership of a file - `$ sudo chgrp <group> somefile`
* change both the user and group ownership of a file at the same time - `$ sudo <user>:<group> somefile` (you need to be a member of the group, too)
* change both the user and group ownership of a file recursively - `$ chown -R <user>:<group> ./` or `$ chown -R <user>:<group> subdir`
 
### umask
----
* create an empty file - `touch /tmp/afile`
* show current umask - `$ umask`
* change the umask so that new files will have 777 permissions - `$ umask ...` (depends on current value)
* change the umask so that new files will have 755 permissions - `$ umask ...` (depends on current value)
* change the umask so that new files will have 222 permissions - `$ umask ...` (depends on current value)

### ACLs
----
* create a file using your usual user name and look at it's ACL properties - ` $ getfacl somefile`
* create a new user, login as that user, and try to add a line to the file you created in the first step - `$ sudo useradd <user>`, `$ sudo passwd <user>`, `$ sudo su - <user>`, `$ echo another line > /tmp/afile`
* make the file writeable by the new user using ACLs and try again - `$ setfacl -m u:<user>:rw /tmp/afile`, `$ getfacl /tmp/afile`, `$ echo ...`
* check the permissions - have they changed?
* make the file not readable by the new user using ACLs and try again - `$ setfacl -m u:<user>:w /tmp/afile`, `$ echo another line > /tmp/afile`
* remove the ACL entries - `$ setfacl -x u:<user> /tmp/afile`
* create a directory for sharing files and set the default ACL so that another user can read and write files there - `$ setfacl -m d:u:<user>:rw somedir`
* clean up  - `$ rm /tmp/afile`, `$ sudo userdel -r <user>`

### Extras
* Lab 30.2: restricted shells
* 30.15: setup a restricted account
* 31.5: `$ vigr ...` can edit `/etc/group`; it's generally symbolically linked to the **vipw** utility
* Lab 32.1: chmod
* Lab 33.1: PAM configuration
    * configure `/etc/pam.d/sshd` to deny login after three failed attempts - add `auth required pam_tally2.so deny=3 onerr=fail` and `account required pam_tally2.so`
    * check to see how many failed logins there are for the user - `$ sudo pam_tally2 -u rocky`
    * reset the failed login counter - `$ sudo pam_tally2 -u rocky -r`