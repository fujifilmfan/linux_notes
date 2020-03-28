Chapter 32: File Permissions and Ownership
------------------------------------------
  
[32.3: Learning Objectives](#323-learning-objectives)  
[32.4: Owner, Group and World](#324-owner,-group-and-world)  
[32.5: File Access Rights](#325-file-access-rights)  
[32.6: File Permissions and Security and Authentication](#326-file-permissions-and-security-and-authentication)  
[32.7: Changing Permissions: chmod](#327-changing-permissions-chmod)  
[32.8: Octal Digits](#328-octal-digits)  
[32.9: chown and chgrp](#329-chown-and-chgrp)  
[32.10: umask](#3210-umask)  
[32.11: umask Demo](#3211-umask-demo)  
[32.12: Filesystem ACLs](#3212-filesystem-acls)  
[32.13: Getting and Setting ACLs](#3213-getting-and-setting-acls)  
[Lab 32.1: Using chmod](#lab-321-using-chmod)  
[Lab 32.2: umask](#lab-322-umask)  
[Lab 32.3: Using Access Control Lists](#lab-323-using-access-control-lists)  
[Paths and Commands](#paths-and-commands)  
  
### 32.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the concepts of **owner**, **group**, and **world**.
* Set file access rights (read, write, and execute) for each category.
* **Authenticate** requests for file access, respecting proper permissions. 
* Use **chmod** to change file permissions, **chown** to change user ownership, and **chgrp** to change group ownership.
* Understand the role of **umask** in establishing desired permissions on newly created files.
* Use ACLs to extend the simpler user, group, world and read, write, execute model.
  
### 32.4: Owner, Group and World
----
* in `-rw-rw-r-- 1 coop aproject 1601 Mar 9 15:04  a_file`:
    * first character is the type of file
    * next three represent the **owner**, or **user** ("coop")
    * next three represent the **group** ("aproject")
    * next three represent the **world**, or **other**
  
### 32.5: File Access Rights
----
* in `-rwxr-xr-x. 1 root root 910200 Jan 30 2014 /usr/bin/vi`:
    * **r** means read access
    * **w** means write access
    * **x** means execute access
    * **-** means no access of that type
* **setuid**, **setgid**, and other specialized permissions also exist for each category
  
### 32.6: File Permissions and Security and Authentication
----
* authentication is granted depending on one of these three sets of permissions, in the following order:
    1. if requester is file owner, file owner permissions are used
    2. if requester is in the group that owns the files, group permissions are examined
    3. if that doesn't succeed, world permissions are examined
  
### 32.7: Changing Permissions: chmod
----
* **chmod** changes file permissions
* `$ chomod uo+x,g-w a_file` adds execution access for user and other and removes write access for group: `-rw-rw-r--` to `-rwxr--r-x`; this is the symbolic form of permissions
  
### 32.8: Octal Digits
----
* the octal number representation is the sum for each digit of:
    * 4 for read access
    * 2 for write access
    * 1 for execute access
* with **chmod**, give a value for each of the three digits, as in:
    * `$ chmod 755 a_file` to give all access to the user and read/execute to groups and other
  
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
  
### Paths and Commands
----
  
#### Paths  
  
Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
  
#### Commands  
  
Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
accounts, files, permissions | `$ chmod ...` | changes file permissions | LFS201 32.7
accounts, files, permissions | `$ chomod uo+x,g-w a_file` | adds execution access for user and other and removes write access for group; symbolic form | LFS201 32.7
accounts, files, permissions | `$ chmod 755 a_file` | gives all access to the user and read/execute to groups and other; octal form | LFS201 32.8
accounts, files, permissions | `$ chown ...` | changes file ownership; only the superuser can do this | LFS201 32.9
accounts, files, permissions | `$ chown wally somefile` | makes wally the owner of the file | LFS201 32.9
accounts, files, permissions | `$ chgrp ...` | changes group ownership; you can only change group ownership to groups you are a member of | LFS201 32.9
accounts, files, permissions | `$ chgrp cleavers somefile` | makes cleavers the group owner of the file | LFS201 32.9
accounts, files, permissions | `$ chown wally:cleavers somefile` | changes both owner and group at the same time | LFS201 32.9
accounts, files, permissions | `$ chown -R wally:cleavers ./` | changes owner and group recursively | LFS201 32.9
accounts, files, permissions | `$ chown -R wally:wally subdir` | changes owner and group recursively | LFS201 32.9
accounts, files, permissions | `$ umask` | shows which permissions should be denied (0002 on my machine) | LFS201 32.10
accounts, files, permissions | `$ umask 0022` | changes the umask value | LFS201 32.10
accounts, files, permissions | `$ getfacl <file|directory>` | see ACLs | LFS201 32.13
accounts, files, permissions | `$ getfacl test` | see ACLs for test file | LFS201 32.13
accounts, files, permissions | `$ setfacl <options> <permissions> <file|directory>` | sets ACLs | LFS201 32.13
accounts, files, permissions | `$ setfacl -m u:isabelle:rx /home/stephane/file1` | modifies ACL of a file or directory | LFS201 32.13
accounts, files, permissions | `$ setfacl -x u:isabelle /home/stephane/file` | removes ACL entries | LFS201 32.13
accounts, files, permissions | `$ setfacl -m d:u:isabelle:rx somedir` | sets default on a directory | LFS201 32.13
  