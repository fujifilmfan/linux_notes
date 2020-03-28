Chapter 31: Group Management
----------------------------

[31.3: Learning Objectives](#313-learning-objectives)  
[31.4: Groups](#314-groups)  
[31.5: Group Management](#315-group-management)  
[31.6: User Private Groups](#316-user-private-groups)  
[31.7: Group Membership](#317-group-membership)  
[31.9: Lab 31.1: Working with Groups](#319-lab-311-working-with-groups)  
[Paths and Commands](#paths-and-commands)  
  
### 31.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain why it is useful to have Linux users belong to one or more groups.
* Use utilities such as **groupadd**, **groupdel**, **groupmod**, and **usermod** to create, remove and manipulate groups and their membership.
* Describe User Private Groups.
* Explain the concept of group membership.
  
### 31.4: Groups
----
* users belong to one or more groups
* users in the same group share certain files and directories
* groups are defined in `/etc/group`, which serves the same function for groups that `/etc/passwd` does for users
* an entry looks like:
    ```
    groupname:password:GID:user1,user2,...
    ```
    mine:
    ```
    student:x:1000:student
    ```
    * groupname: name of the group
    * password: password placeholder; group passwords can be set if `/etc/gshadow` exists
    * GID: group identifier; 0 - 99 for system groups, 100 - GID_MIN (defined in `/etc/login.defs`) are special, > GID_MIN are for **UPG** (User Privage Groups)
    * user1, user2, ...: comma-separated list of users who are members of the group; user need not be listed here if the group is the user's principal group
  
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

### Paths and Commands
----
  
#### Paths  
  
Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
accounts | `/etc/group` | holds group definitions | LFS201 31.4
accounts | `/etc/gshadow` | holds group passwords | LFS201 31.4
accounts, system | `/etc/profile` | contains system-wide environment and startup programs | LFS201 31.6
accounts, system | `/etc/bashrc` | contains system-wide functions and aliases | LFS201 31.6
  
#### Commands  
  
Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
accounts | `$ sudo groupadd -r -g 215 staff` | creates a system group (**-r**) and assigns it the GID "215" and name "staff" (?) | LFS201 31.5
accounts | `$ sudo groupmod -g 101  blah` | changes the group ID of "blah" (?) | LFS201 31.5
accounts | `$ sudo groupdel newgroup` | deletes the group "newgroup" | LFS201 31.5
accounts | `$ sudo usermod -G student,group1,group2 student` | assign "student" to the comma-separated list of groups | LFS201 31.5
accounts | `$ vigr ...` | make changes to `/etc/group` | LFS201 31.5
accounts | `$ useradd -G <group name>` | adds a user to an existing group when creating the user | LFS201 31.8
accounts | `$ groups [USER1 USER2 ...]` | identify group membership for users | LFS201 31.7
accounts | `$ id -Gn [USER1 USER2 ...]` | identify group membership for users | LFS201 31.7
accounts | `$ groups` or `$ id -Gn` | shows current user group membership | LFS201 31.7
accounts | `$ id` | shows current user UID, GID, and groups | LFS201 31.7
