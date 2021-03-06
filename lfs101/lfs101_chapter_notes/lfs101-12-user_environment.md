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
