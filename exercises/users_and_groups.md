User and Group Management
-----

### Chapters: LFS101: 12, 18, LFS201: 30, 31, 32, 33

### Current user info
* show current user
* show info about current user
* show group membership of current user
* show logged-on users - `$ who` or `$ who -a`
* show info about another user - `$ id <other_user>`
* show just group IDs of another user - `$ id -G <other_user>` (-u for just user ID; add a -n for names instead of numbers)
* show group membership of another user - `$ groups <other_user>`

### Add user
* add user rocky - `$ sudo useradd -m rocky` (-m to add home dir)
* login as rocky with ssh - `$ ssh rocky@localhost` (fails); also, start sshd with `$ sudo service sshd restart` or `$ sudo systemctl restart sshd.service`
* show failed command attempts due to security - `$ cat /var/log/secure`
* look at the new records which were created in the `/etc/passwd`, `/etc/group`, and the `/etc/shadow` files
* look at the `/etc/default/useradd` file and see what the current defaults are set to; also look at the `/etc/login.defs` file
* add user bullwinkle w/ zsh shell and a comment - `$ sudo useradd -s /bin/zsh -m -c "Bullwinkle J Moose" bullwinkle`

### Passwords and privileges
* give rocky and bullwinkle a password - `$ sudo passwd rocky`
* view password aging for rocky - `$ sudo chage --list rocky` or `$ sudo chage -l rocky`
* force rocky to change password at next login - `$ sudo chage -d 0 rocky`
* modify the expiration date for the user account, setting it to be something that has passed, and check to see what has changed - `$ sudo chage -E 2014-31-12 rocky` or `$ sudo chage -E $(date -d -2days +%Y-%m-%d) rocky`, `$ sudo chage -l rocky`
* lock rocky's account - `$ sudo usermod -L rocky` (shows '!' in `/etc/shadow`)
* unlock rocky's account - `$ sudo usermod -U rocky`
* give rocky sudo privileges - create a file in `/etc/sudoers.d/rocky` with `rocky  ALL=(ALL)  ALL` or add that line to `/etc/sudoers` (use `$ sudo visudo`) (`$ pkexec visudo` if I botch `/etc/sudoers`)
* login as rocky and execute a command requiring root privilege, like `$ ls /root` - `$ su - rocky` or `$ sudo su rocky` or `$ ssh rocky@localhost`

### Groups
* create two new user accounts, rocky and bullwinkle if they don't already exist
* create two new groups, `friends` and `bosses` (with a GID of 490) - `$ sudo groupadd friends`, `$ sudo groupadd -g 490 bosses`
* confirm the new group IDs - `$ cat /etc/group` or `$ grep -e friends -e bosses /etc/group`
* change the bosses group ID to 500 (if not used) - `$ sudo groupmod -g 500 bosses`
* add rocky to both new groups; add bullwinkle to group friends - `$ sudo usermod -aG friends,bosses rocky`, `$ sudo usermod -aG friends bullwinkle`
* see how the groups have changed - `$ cat /etc/group` or `$ grep -e friends -e bosses /etc/group`
* shows the groups for both rocky and bullwinkle - `$ groups rocky,bullwinkle`
* show the IDs for both rocky and bullwinkle - `$ id rocky`, `$ id bullwinkle`
* show just the group IDs for rocky and bullwinkle - `$ id -G rocky`, `$ id -G bullwinkle` (try to optimize)
* show the group names for rocky and bullwinkle - `$ id -Gn rocky`, `$ id -Gn bullwinkle` (try to optimize)
* create boris and add him to friends - `$ sudo useradd -G friends -m boris`
* is the new user also part of their default group? - yes
* remove boris from the friends group - `$ sudo usermod -G boris,characters boris`

### Delete user
 

* create private and public encryption keys - `$ ssh-keygen` 

### Groups
* login as rocky, create a directory called `somedir` with execute privileges for all, and set the group ownership to 'bosses' - `$ ssh rocky@localhost`, `$ chmod a+x .`, `$ mkdir ~/somedir`, `$ chgrp bosses somedir`
* login as `bullwinkle` and try to create a file in `/home/rocky/somedir` called `somefile` using the **touch** command; can you do this? - `$ ssh bullwinkle@localhost`, `$ touch /home/rocky/somedir/somefile`, no
* add 'bullwinkle' to the 'bosses' group and try again - `$ sudo usermod -a -G bosses bullwinkle`, `$ ssh bullwinkle@localhost`, `$ touch /home/rocky/somedir/somefile`, `$ ls -al /home/rocky/somedir` (need to log out and log in again)
* delete the users and the groups - `$ sudo userdel ...`, `$ sudo groupdel friends`
* create a system group with name staff and GID 215 - `$ sudo groupadd -r -g 215 staff`

### chown and chgrp 
* as rocky, create afile in somedir and change ownership to bullwinkle - `$ sudo chown bullwinkle afile`
* change both the user and group ownership of a file at the same time - `$ sudo <user>:<group> somefile` (you need to be a member of the group, too)
* change both the user and group ownership of a file recursively - `$ chown -R <user>:<group> ./` or `$ chown -R <user>:<group> subdir`

### umask
* create an empty file - `touch /tmp/afile`
* show current umask - `$ umask`
* change the umask so that new files will have 777 permissions - `$ umask ...` (depends on current value) - can't do that
* change the umask so that new files will have 755 permissions - `$ umask ...` (depends on current value) - can't do that
* change the umask so that new files will have 222 permissions - `$ umask 0444` (depends on current value)

### ACLs
* create a file using your usual user name and look at it's ACL properties - ` $ getfacl somefile`
* create a new user, login as that user, and try to add a line to the file you created in the first step - `$ sudo useradd <user>`, `$ sudo passwd <user>`, `$ sudo su - <user>`, `$ echo another line > /tmp/afile`
* make the file writeable by the new user using ACLs and try again - `$ setfacl -m u:<user>:rw /tmp/afile`, `$ getfacl /tmp/afile`, `$ echo ...`
* check the permissions - have they changed?
* make the file not readable by the new user using ACLs and try again - `$ setfacl -m u:<user>:w /tmp/afile`, `$ echo another line > /tmp/afile`
* remove the ACL entries - `$ setfacl -x u:<user> /tmp/afile`
* create a directory for sharing files and set the default ACL so that another user can read and write files there - `$ setfacl -m d:u:<user>:rw /tmp/sharing`

### Delete stuff
* clean up files - `$ rm /tmp/sharing`
* delete rocky, bullwinkle, and boris and their home directories - `$ userdel -r rocky`
* delete groups friends, bosses, characters - `$ sudo /usr/sbin/groupdel friends` 

### Extras
* add user bullwinkle w/ zsh shell and a comment - `$ sudo useradd -s /bin/zsh -m -k /etc/skel -c "Bullwinkle J Moose" bullwinkle` (-k /etc/skel)
* confirm my group ID in two places - `$ grep -e student /etc/group /etc/passwd`

* start a restricted shell - `$ bash -r`
* Lab 30.2: restricted shells
* 30.15: setup a restricted account
* 31.5: `$ vigr ...` can edit `/etc/group`; it's generally symbolically linked to the **vipw** utility
* Lab 32.1: chmod
* Lab 33.1: PAM configuration
    * configure `/etc/pam.d/sshd` to deny login after three failed attempts - add `auth required pam_tally2.so deny=3 onerr=fail` and `account required pam_tally2.so`
    * check to see how many failed logins there are for the user - `$ sudo pam_tally2 -u rocky`
    * reset the failed login counter - `$ sudo pam_tally2 -u rocky -r`
