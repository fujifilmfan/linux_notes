
LFS101 18
Chapter 18: Local Security Principles
-------------------------------------

LFS101 12
Chapter 12: User Environment
----------------------------
  
[12.0: Introduction/ Learning Objectives](#120-introduction-learning-objectives)  
[12.1: Accounts, Users, and Groups](#121-accounts-users-and-groups)  
[12.2: Environment Variables](#122-environment-variables)  
[12.3: Recalling Previous Commands](#123-recalling-previous-commands)  
[12.4: File Permissions](#124-file-permissions)  
[12.5: Summary](#125-summary)  
[Paths and Commands](#paths-and-commands)  
  
### 12.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should be able to:  
* Use and configure user accounts and user groups.  
* Use and set environment variables.  
* Use the previous shell command history.  
* Use keyboard shortcuts.  
* Use and define aliases.  
* Use and set file permissions and ownership.  
  
### 12.1: Accounts, Users, and Groups
----
#### Identifying the Current User
* logged-on users: `$ who` (for more detail, use `$ who -a`)
* current user: `$ whoami`
  
#### Basics of Users and Groups
* users are assigned a user ID (**uid**); normal users have a **uid** of 1000 or greater
* **groups** organize users
   * membership controlled in `/etc/group`
   * every user belongs to a **primary group** (default)
* users can have one or more group IDs (**gid**)
   * default one is same as the **uid**
   * numbers are associated with names through `/etc/passwd` and `/etc/group`
  
#### Adding and Removing Users
* only root user can add and remove users and groups
* `$ sudo useradd bjmoose`
   * sets home directory to `/home/bjmoose`
   * populates it with basic files from `/etc/skel`
   * adds a line to `/etc/passwd`
   * sets default shell to `/bin/bash`
* `$ userdel bjmoose`
   * leaves home directory intact
   * use `$ userdel -r bjmoose` to remove home dir, too
* `$ id` info about current user
* `$ id guardian999` info about another user
  
#### Adding and Removing Groups
* `$ sudo /usr/sbin/groupadd anewgroup`  
* `$ sudo /usr/sbin/groupdel anewgroup`  
* `$ groups` group membership of current user  
* `$ groups guardian999` groups of another user  
* add user to existing group:  
   * find existing membership:  
     `$ groups rjsquirrel`  
     > bjmoose : rjsquirrel  
   * add new group:  
     `$ sudo /usr/sbin/usermod -a -G anewgroup rjsquirrel`  
     `-a` avoids removing existing groups  
     `-g` change group properties by group ID  
     `-n` change group properties by name  
     `$ groups rjsquirrel`  
     > rjsquirrel: rjsquirrel anewgroup  
* remove user from group:
   * `$ sudo /usr/sbin/usermod -G rjsquirrel rjsquirrel`  
     `-G` takes complete list of groups (any not written will be removed)
   * `$ groups rjsquirrel`
     > rjsquirrel : rjsquirrel  
  
#### The root Account
* aka **administrator** or **superuser**
* **sudo**:
   * temporary
   * only for a specific set of commands
  
#### su and sudo
* use **su** to become root in a new shell
   * dangerous
   * not recommended
* using **sudo** is less dangerous and preferred
   * must be enabled on a per-user basis on most distributions
  
#### Elevating to root Account
* type **su** and then enter root password
* for one command with root privilege: `$ sudo <commmand>`
* **sudo** config files in `/etc/sudoers` and `/etc/sudoers.d/`
  
#### User Startup Files
* global settings for all users defined in `/etc`
* other initialization files in user's home directory
* startup files can be used for:
   * Customizing the prompt
   * Defining command-line shortcuts and aliases
   * Setting the default text editor
   * Setting the path for where to find executable programs.
  
#### Order of the Startup Files
* `/etc/profile` read and evaluated, then these startup files in order:
* `~/.bash_profile`
* `~/.bash_login`
* `/.profile`
* the first startup file that is found is evaluated and the rest ignored
* when opening a new shell or window, only `~/.bashrc` is read and evaluated
   * for **Ubuntu**, **openSUSE**, and **CentOS**, the user must make appropriate changes in `~/.bash_profile` to have it include  `~/.bashrc`.
   * since `~/.bash_profile` is only executed at login, many users only fiddle with `~/.bashrc`
  
#### Creating Aliases
* `$ alias` to see currently defined aliases
  
#### Try-It-Yourself: Identify the Currently Logged-In User and User
Okay
  
#### Lab 12.1 (Lab 1): Deploying aliases
```
Typing long commands and filenames over and over gain gets rather tedious, and leads to a lot of trivial errors, such as typos.  
Deploying aliases allows us to define shortcuts to alleviate the pain of all of this typing.  
Suppose you are a member of a project team that works in a common, shared directory for your project. This directory is located in `/home/staff/RandD/projects/projectX/src`.  
When you are working on Project X, you often need to create and modify your files in this directory. It does not take too long before typing in:  
   `cd /home/staff/RandD/projects/projectX/src`  
gets tedious.  
Define and use an alias named **projx** to do the above **cd** command for you.  
```
  
My solution: `alias projx='cd /home/staff/RandD/projects/projectX/src'`  
Lab solution: double quotes could also be used (or no quotes since there is no white space in the alias)  
  
### 12.2: Environment Variables
----
#### Environment Variables
Three ways to see currently set environment variables (each has different output):
* `$ set`
* `$ env`
* `$ export` 
  
#### Setting Environment Variables
* variables created within script are only available to current shell, not **child processes** (sub-shells)
* use **export** to allow child processes to see the values
  
Task                                  | Command
----                                  | -------
Show the value of a specific variable | `$ echo $SHELL`
Export a new variable value           | `$ export VARIABLE=value` (or `VARIABLE=value; export VARIABLE`)
Add a variable permanently 	          | 1. Edit `~/.bashrc` and add the line `export VARIABLE=value`
*cont.*                               | 2. Type `$ source ~/.bashrc` or just `$ . ~/.bashrc` (dot ~/.bashrc); or just start a new shell by typing `$ bash`
  
#### The HOME Variable
* **HOME** represents the home directory  
* **~** is often used to abbreviate `$HOME`  
  
Command        | Explanation
-------        | -----------
`$ echo $HOME` | Show the value of the HOME environment variable, then change directory (cd) to /bin
`/home/me`     | *cont.*
`$ cd /bin`    | *cont.*
`$ pwd`        | Where are we? Use print (or present) working directory (pwd) to find out. As expected, /bin
`/bin`         | *cont.*
`$ cd`         | Change directory without an argument...
`$ pwd`        | ...takes us back to HOME
`/home/me`     | as you can now see
  
#### The PATH Variable
* PATH is an ordered list of directories
* null directory or `./` indicates current directory at any given time
* null directory before first colon: `:path1:path2`
* null directory between non-null ones: `path1::path2`
* prefix a `bin` to your path:
   * `$ export PATH=$HOME/bin:$PATH`  
   * `$ echo $PATH`  
     > /home/me/bin:/usr/local/bin:/usr/bin:/bin/usr
  
#### The PS1 Variable and the Command Line Prompt
* **prompt statement** (PS) used to customize **prompt** string  
* `PS1` is the primary prompt variable; special characters:  
* `$ export PS1='\u@\h:\w$ '` gives `me@example.com:~$`
  
Character | Meaning
--------- | -------
`\u`      | user name
`\h`      | host name
`\w`      | current working directory
`\!`      | history number of this command
`\d`      | date
  
#### The SHELL Variable
* SHELL points to user's default command shell
  
#### Lab 12.2a (Lab 2): Adding /tmp to Your Path
```
Create a small file, **/tmp/ls**, which contains just the line:  
**echo HELLO, this is the phony ls program.**  
Then, make it executable by doing:  
`$ chmod +x /tmp/ls`  
1. Append /tmp to your path, so it is searched only after your usual path is considered. Type ls and see which program is run: /bin/ls or /tmp/ls?  
2. Pre-pend /tmp to your path, so it is searched before your usual path is considered. Once again, type ls and see which program is run: /bin/ls or /tmp/ls?  
  
What are the security considerations in altering the path this way?  
```
My solution:  
1. `$ export PATH=$PATH:/tmp` - the usual ls runs  
2. `$ export PATH=/tmp:$PATH` - the fake ls runs  
  
Lab solution:  
```
First create the phony ls program, using an editor, or just simply doing:
student:/tmp>echo "echo HELLO, this is the phony ls program." > /tmp/ls
student:/tmp>chmod +x /tmp/ls

For the next two steps it is a good idea to work in another terminal window, or just start a new shell, so the changes do not persist on later issued commands. You can start a new shell by just typing bash.
    student:/tmp>bash
    student:/tmp>PATH=$PATH:/tmp
    student:/tmp>ls /usr

    bin etc games include lib lib64 libexec local sbin share src tmp

    student:/tmp>exit
    student:/tmp>bash
    student:/tmp>PATH=/tmp:$PATH
    student:/tmp>ls /usr

    HELLO, this is the phony ls program.

    student:/tmp>exit

Note the second form is a very dangerous thing to do, and is a trivial way to insert a Trojan Horse program; if someone can put a malicious program in /tmp, they can trick you into running it accidentally. 
```
  
#### Lab 12.2b (Lab 3): Changing the Command Line Prompt
```
It is nice to have your current working directory as part of your prompt so that a quick glance will give you some information without typing pwd every time.
If you often work on multiple computers, especially if you network from one into another with ssh, it is very convenient to have the computer name be part of your prompt.

1. Put your current working directory in your command line prompt.
2. Put your computer (machine) name in your prompt.
3. Put both your current directory and computer name in your prompt.

How can you make this persistent, so that whenever you start a bash command shell, this is your prompt? 
```
My solution:
1. export PS1='\w $ '
2. export PS1='\h $ '
3. export PS1='\u@\h \w $ '
  
This can be made persistent by putting the commands in `~/.bash_profile` or `~/.bashrc`  
Lab solution:  
```
1. $ echo $PWD
    > /tmp
   $ PS1='\w>'
    > /tmp>
    
2. $ PS1='\h>'
   > student>

3. $ PS1='\h:\w>'
   > student:/tmp>
```
  
### 12.3: Recalling Previous Commands
----
#### Recalling Previous Commands
* **Up** and **Down** cursor keys or `$ history` can be used to see previous commands
* history stored in `~/.bash_history`
  
#### Using History Environment Variables
  
Variable     | Meaning
--------     | -------
HISTFILE     | The location of the history file. 
HISTFILESIZE | The maximum number of lines in the history file (default 500). 
HISTSIZE     | The maximum number of commands in the history file. 
HISTCONTROL  | How commands are stored. 
HISTIGNORE   | Which command lines can be unsaved.
  
#### Finding and Using Previous Commands
  
Key                                | Usage
---                                | -----
**Up**/**Down** arrow keys         | Browse through the list of commands previously executed
`!!` (Pronounced as **bang-bang**) | Execute the previous command
`CTRL-R`                           | Search previously used commands
  
Using `CTRL-R`:  
* `$ ^R`                               # This all happens on 1 line  
  > (reverse-i-search)'s': sleep 1000  # Searched for 's'; matched "sleep"  
* `$ sleep 1000`                       # Pressed Enter to execute the searched command.  
* `$ `  
  
#### Executing Previous Commands
Syntax    | Task
------    | ----
`!`       | Start a history substitution
`!$`      | Refer to the last argument in a line
`!n`      | Refer to the nth command line
`!string` | Refer to the most recent command starting with string
  
* all history substitutions begin with `!`  
* in the line `$ ls -l /bin /etc /var`, `!$` refers to `/var`  
* example:  
   * `$ history`
      * 1  echo $SHELL
      * 2  echo $HOME
      * 3  echo $PS1
      * 4  ls -a
      * 5  ls -l /etc/ passwd
      * 6  sleep 1000
      * 7  history
   * `$ !1`  # Execute command #1 above
      * echo $SHELL
      * /bin/bash
   * `$ !sl`  # Execute the command beginning with "sl"
      * sleep 1000
   * `$ `
  
#### Keyboard Shortcuts
Keyboard Shortcut | Task
----------------- | ----
CTRL-L            | Clears the screen
CTRL-D            | Exits the current shell
CTRL-Z            | Puts the current process into suspended background
CTRL-C            | Kills the current process
CTRL-H            | Works the same as backspace
CTRL-A            | Goes to the beginning of the line
CTRL-W            | Deletes the word before the cursor
CTRL-U            | Deletes from beginning of line to cursor position
CTRL-E            | Goes to the end of the line
Tab               | Auto-completes files, directories, and binaries
  
#### Lab 12.3 (Lab 4): Command History
```
You have been busy working with your Linux workstation long enough to have typed in about 100 commands in one particular bash command shell.  
At some point earlier, you used a new command, but the exact name has slipped your mind.  
Or perhaps it was a pretty complicated command, with a bunch of options and arguments, and you do not want to go through the error prone process of figuring out what to type again.  
1. How do you ascertain what the command was?  
2. Once you find the command in your history, how do you easily issue the command again without having to type it all in at the prompt?  
```
My solution:  
1. `CTRL-R` or `$ history | grep <string>`  
2. Hit **Enter** for CTRL-R or `!n` for the history solution  
Lab solution:  
One could also use `!<command>` for a command only used once  
```
The history command is the way to display the commands you have typed in:

student:/tmp> history

1 cd /
2 ls
3 cd
4 pwd
5 echo $SHELL
6 ls /var/
7 ls /usr/bin
8 ls /usr/local/bin
9 man fstab
10 ls
. . .

In order to re-run a previous command, you have a few choices. Letâ€™s say that you wanted to re-run the man command you ran way back when you first logged in. You could type:

student:/tmp> !9

to re-run the command listed as #9 . If this was the only man command that you typed in, you could also type:

student:/tmp> !man

now that you remember the command name that you typed. Finally, if you had typed a few man commands, you could use CTRL-R to search backward in your history to find the specific man command that you want to re-run, and then just hit Return to execute it. 
```
  
### 12.4: File Permissions
----
#### File Ownership
* every file is associated with a user who is the **owner**
* every file also associated with a **group**
  
Command | Usage
`chown` | Used to change user ownership of a file or directory
`chgrp` | Used to change group ownership
`chmod` | Used to change the permissions on the file, which can be done separately for **owner**, **group** and the rest of the world (often named as **other**.)
  
#### File Permission Modes and chmod
Example 1:  
* `$ ls -l somefile`  
  > -rw-rw-r-- 1 coop coop 1601 Mar 9 15:04 somefile  
* `$ chmod uo+x,g-w somefile`  
* `$ ls -l somefile`  
  > -rwxr--r-x 1 coop coop 1601 Mar 9 15:04 somefile  
  
Example 2:  
* `$ chmod 755 somefile`  
* `$ ls -l somefile`  
  > -rwxr-xr-x 1 coop coop 1601 Mar 9 15:04 somefile  
  
#### Example of chown
See "12.4 - chown rhel7.png"
  
#### Example of chgrp
See "12.4 - chgroup rhel7.png"
  
#### Try-It-Yourself: Using chmod to Change File Permissions
```
Tasks to be performed:
1. View the files present in the current directory in the long listing format.
2. By default the permissions for a file will be rw-rw-r--, change the permissions of the sample2.sh file to
    rwxr--r-x, using the rwx notation.
3. View the files present in the current directory in the long listing format.
4. By default the permissions for a file will be rw-rw-r--, change the permissions of the script1.sh file to
    rwxr-x--x, using 421 method.
5. View the files present in the current directory in the long listing format.
```
Done  
  
### 12.5: Summary
----
You have completed this chapter. Let's summarize the key concepts covered:
* Linux is a multi-user system.
* To find the currently logged on users, you can use the **who** command.
* To find the current user ID, you can use the **whoami** command.
* The `root` account has full access to the system. It is never sensible to grant full root access to a user.
* You can assign root privileges to regular user accounts on a temporary basis using the **sudo** command.
* The shell program (**bash**) uses multiple startup files to create the user environment. Each file affects the interactive environment in a different way. `/etc/profile` provides the global settings.
* Advantages of startup files include that they customize the user's prompt, set the user's terminal type, set the command-line shortcuts and aliases, and set the default text editor, etc.
* An **environment variable** is a character string that contains data used by one or more applications. The built-in shell variables can be customized to suit your requirements.
* The **history** command recalls a list of previous commands, which can be edited and recycled.
* In Linux, various keyboard shortcuts can be used at the command prompt instead of long actual commands.
* You can customize commands by creating aliases. Adding an alias to `/.bashrc` will make it available for other shells.
* File permissions can be changed by typing `chmod permissions filename`.
* File ownership is changed by typing `chown owner filename`.
* File group ownership is changed by typing `chgrp group filename`.
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
accounts, system | `/etc/group` | controls group membership | LFS101 12.1  
accounts, system | `/etc/passwd` | associates IDs with usernames | LFS101 12.1  
accounts, system | `/etc/skel` | basic files used to populate a new user account | LFS101 12.1  
accounts | `/etc/sudoers` | contains config files for **sudo** | LFS101 12.1  
accounts | `/etc/sudoers.d/` | contains config files for **sudo** | LFS101 12.1  
accounts, system | `/etc` | contains global settings for all users | LFS101 12.1  
accounts, environment | 1 `/etc/profile` | read and evaluated at login | LFS101 12.1  
accounts, environment | 2 `~/.bash_profile` | then this startup file is read | LFS101 12.1  
accounts, environment | 3 `~/.bash_login` | then this startup file is read | LFS101 12.1  
accounts, environment | 4 `/.profile` | then this startup file is read | LFS101 12.1  
accounts, environment | `~/.bashrc` | read when opening a new shell or window | LFS101 12.1  
accounts, environment | `~/.bash_history` | stores Bash history | LFS101 12.3
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
bash, environment | `$ who` | shows logged-on users | LFS101 12.1  
bash, environment | `$ who -a` | shows more detail about logged-on users | LFS101 12.1  
bash, environment | `$ whoami` | shows the current user | LFS101 12.1  
accounts | `$ sudo useradd bjmoose` | add user "bjmoose" | LFS101 12.1  
accounts | `$ userdel bjmoose` | deletes user "bjmoose", leaves home directory intact | LFS101 12.1  
accounts | `$ userdel -r bjmoose` | deletes user "bjmoose" and removes home directory | LFS101 12.1  
accounts | `$ id` | shows info about current user | LFS101 12.1  
accounts | `$ id guardian999` | shows info about another user | LFS101 12.1  
accounts | `$ sudo /usr/sbin/groupadd anewgroup` | create a new group | LFS101 12.1  
accounts | `$ sudo /usr/sbin/groupdel anewgroup` | delete a group | LFS101 12.1  
accounts | `$ groups` | group membership of current user | LFS101 12.1  
accounts | `$ groups guardian999` | groups of another user |  | LFS101 12.1  
accounts | `$ sudo /usr/sbin/usermod -a -G anewgroup rjsquirrel` | add user to "rjsquirrel" to "anewgroup"; check current memberships | LFS101 12.1  
accounts | `$ sudo /usr/sbin/usermod -G rjsquirrel rjsquirrel` | remove user from group by not adding full list of current memberships | LFS101 12.1  
accounts | `$ su ...` | become root in a new shell | LFS101 12.1  
accounts | `$ sudo <commmand>` | generic syntax | LFS101 12.1  
bash, environment | `$ alias` | shows currently defined aliases | LFS101 12.1  
bash, environment | `$ alias somealias='somecommand'` | create a new alias | LFS101 12.1  
bash, environment | `$ set` | shows environment variables | LFS101 12.2  
bash, environment | `$ env` | shows environment variables | LFS101 12.2  
bash, environment | `$ export` | shows environment variables | LFS101 12.2  
bash, environment | `$ echo $SHELL` | shows the value of the SHELL variable | LFS101 12.2  
bash, environment | `$ export VARIABLE=value` | exports a new variable value | LFS101 12.2  
bash, environment | `$ VARIABLE=value; export VARIABLE` | exports a new variable value | LFS101 12.2  
accounts, environment | `$ source ~/.bashrc` | refresh environment after editing `.bashrc` | LFS101 12.2  
accounts, environment | `$ . ~/.bashrc` | efresh environment after editing `.bashrc` | LFS101 12.2  
bash, environment | `$ bash` | starts a new shell | LFS101 12.2  
bash, environment | `$ echo $HOME` | shows the value of the HOME variable | LFS101 12.2  
bash | `$ cd /bin` | change to `/bin` directory | LFS101 12.2  
bash | `$ pwd` | show current working directory | LFS101 12.2  
bash | `$ cd` | change to home directory | LFS101 12.2  
bash, environment | `$ export PATH=$HOME/bin:$PATH` | prefix `$HOME/bin` to the PATH | LFS101 12.2  
bash, environment | `$ echo $PATH` | shows the value of the PATH variable | LFS101 12.2  
bash, environment | `$ export PS1='\u@\h:\w$ '` | sets prompt to `me@example.com:~$` | LFS101 12.2  
accounts, files, permissions | `$ chmod +x /tmp/ls` | makes a file executable | LFS101 12.2  
bash | `$ history` | shows Bash history | LFS101 12.3  
bash | `$ !!` | executes the previous command | LFS101 12.3  
bash | `$ ^R` | searches previously used commands | LFS101 12.3  
bash | `$ !` | starts a history substitution | LFS101 12.3  
bash | `$ !$` | refers to the last argument in a line, like `/var` in `$ ls -l /bin /etc /var` | LFS101 12.3  
bash | `$ !n` | refers to the nth command line | LFS101 12.3  
bash | `$ !string` | refers to the most recent command starting with string | LFS101 12.3  
bash | `$ ^L` | clears the screen | LFS101 12.3  
bash | `$ ^D` | exits the current shell | LFS101 12.3  
bash | `$ ^Z` | puts the current process into suspended background | LFS101 12.3  
bash | `$ ^C` | kills the current process | LFS101 12.3  
bash | `$ ^H` | works the same as backspace | LFS101 12.3  
bash | `$ ^A` | goes to the beginning of the line | LFS101 12.3  
bash | `$ ^W` | deletes the word before the cursor | LFS101 12.3  
bash | `$ ^U` | deletes from beginning of line to cursor position | LFS101 12.3  
bash | `$ ^E` | goes to the end of the line | LFS101 12.3  
bash | `$ ⇥` (Tab) | auto-completes files, directories, and binaries | LFS101 12.3  
accounts, files, permissions | `$ chmod <permissions> <filename>` | changes file permissions | LFS101 12.4  
accounts, files, permissions | `$ chmod uo+x,g-w somefile` | changes file permissions | LFS101 12.4  
accounts, files, permissions | `$ chmod 755 somefile` | changes file permissions | LFS101 12.4  
accounts, files, permissions | `$ chown <owner> <filename>` | changes file user ownership | LFS101 12.4  
accounts, files, permissions | `$ chgrp <group> <filename>` | change file group ownership | LFS101 12.4  


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

### Ch.30
* Lab 30.1
    1. Examine /etc/passwd and /etc/shadow, comparing the fields in each file, especially for the normal user account. What is the same and what is different?
    ```
    1. $ sudo grep student /etc/passwd /etc/shadow
        /etc/passwd:student:x:1000:100:LF Student:/home/student:/bin/bash
        /etc/shadow:student:$6$jtoFVPICHhba$iGFFUU8ctrtOGoistJ4/30DrNLi1FS66qnn0VbS6Mvm
        luKI08SgbzT5.IcOHo5j/SOdCagZmF2RgzTvzLb11H0:16028:0:99999:7:::
    (You can use any normal user name in the place of student.) About the only thing that matches is the user name field.
    ```
    2. Create a user1 account using useradd.
    ```
    2. $ sudo useradd user1
    ```
    3. Login as user1 using ssh. You can just do this with:
        $ ssh user1@localhost
    It should fail because you need a password for user1; it was never established.
    ```
    3. $ ssh user1@localhost user1@localhost's password:
    Note you may have to first start up the sshd service as in: $ sudo service sshd restart
    or
    $ sudo systemctl restart sshd.service
    ```
    4. Set the password for user1 to user1pw and then try to login again as user1.
    ```
    4. $ sudo passwd user1
    Changing password for user user1.
    New password:
    ```
    5. Look at the new records which were created in the /etc/passwd, /etc/group and the /etc/shadow files.
    ```
    5. $ sudo grep user1 /etc/passwd /etc/shadow
    /etc/passwd:user1:x:1001:100::/home/user1:/bin/bash
    /etc/shadow:user1:$6$OBE1mPMw$CIc7urbQ9ZSnyiniVOeJxKqLFu8fz4whfEexVem2
    TFpucuwRN1CCHZ19XGhj4qVujslRIS.P4aCXd/y1U4utv.:16372:0:99999:7:::
    ```
    6. Look at the /etc/default/useradd file and see what the current defaults are set to. Also look at the /etc/login.defs file.
    ```
    6. On either RHEL 7 or openSUSE systems for example: $ cat /etc/default/useradd
    # useradd defaults file
    GROUP=100
    HOME=/home
    INACTIVE=-1
    EXPIRE=
    SHELL=/bin/bash
    SKEL=/etc/skel
    CREATE_MAIL_SPOOL=yes
    $ cat /etc/login.defs
    ....
    We don’t reproduce the second file as it is rather longer, but examine it on your system.
    ```
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

* Lab 30.2
    * **Extra:** restricted shells


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
  


### Ch.32
* Lab 32.1
    * **Extra:** chmod
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
### Ch.33
* Lab 33.1
    * **Extra:** PAM configuration


