Chapter 7: Command Line Operations
----------------------------------

[7.0: Introduction/ Learning Objectives](#7.0:-Introduction/-Learning-Objectives)  
[7.1: Command Line Mode Options](#7.1:-Command-Line-Mode-Options)  
[7.2: Basic Operations](#7.2:-Basic-Operations)  
[7.3 Working with Files](#7.3-Working-with-Files)  
[7.4: Searching for Files](#7.4:-Searching-for-Files)  
[7.5: Installing Software](#7.5:-Installing-Software)  
[7.6: Summary](#7.6:-Summary)  

### 7.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
  * Use the command line to perform operations in Linux.
  * Search for files.
  * Create and manage files.
  * Install and update software.

### 7.1: Command Line Mode Options

#### Introduction to Command Line
"Graphical user interfaces make easy tasks easier, while command line interfaces make difficult tasks possible."

#### Some Basic Utilities
* **cat**
* **head**
* **tail**
* **man**

#### The Command Line
Most input lines entered at shell prompt have three basic elements:
* command
* options
* arguments (what the command operates on)

#### sudo
* **sudo** allows a user to run programs with privileges of another user, generally root (superuser)
steps for setting up and running sudo if not already set up and enabled:
  1. use **su** and then enter password; prompt changes from `$` to `#`
  2. create a config file, typically created in `/etc/sudoers.d`; file created like this: `# echo "student ALL=(ALL) ALL" > /etc/sudoers.d/student`  
     I already have the file:
    
    
    
     `[root@centos sudoers.d]# cat student` 
     > student ALL=(ALL) NOPASSWD: ALL
  3. change permissions: `# chmod 440 /etc/sudoers.d/student`
* it is possible (though very insecure) to configure sudo to not require a password or change the time window in which the password does not have to be repeated with every sudo command

#### Using a Text Terminal on the Graphical Desktop
**gnome-terminal** is the default **terminal emulator** on **GNOME** desktop environments; other terminal programs include:
* **xterm**
* **rxvt**
* **konsole**
* **terminator**

#### Launching Terminal Windows
in **CentOS 7**:
* click **Applications->Utilities->Terminal**
* right-click on desktop background and select **Open Terminal**
* hit **Alt-F2** and type **gnome-terminal** or **xterm**

#### Switching between the GUI and the Command Line
* during installation, one can often choose between desktop (with **X**) or server (usually without **X**)
* removing **X** from a production server can help maintain a lean system that can be easier to support and more secure

#### Virtual Terminals
**Virtual Terminals (VT)** are **console** sessions that use the entire display and keyboard outside of graphical environment; only one visible at a time even if others active; not the same as command line terminal on desktop  

One VT reserved for graphical environment (Ubuntu: VT 7; CentOS/RHEL and openSUSE: VT 1), six for text logins
To switch between the VTs:
* **CTRL-ALT-corresponding function key** for the VT if running **X**
* **ALT-corresponding function key** if not running **X**

#### Turning Off the Graphical Desktop
on newer **systemd**-based distros, the display manager is run as a service; it can be stopped with the **systemctl** utility or **telinit** on most distros:
* `$ sudo systemctl stop gdm`  (or `$ sudo telinit 3`)
* `$ sudo systemctl start gdm`  (or `$ sudo telinit 5`)
  (use **lightdm** instead of **gdm** on Ubuntu)

### Lab 7.1 Killing the Graphical User Interface
1. kill the current graphical desktop
I used `$ sudo systemctl stop gdm`
2. restart the GUI from the console
I used `$ sudo telinit 5`; GUI reinstated in VT 1; got message that last login was on tty2, so I guess that refers to the terminal

### 7.2: Basic Operations

#### Logging In and Out
* **text terminal** will prompt for username and password
* **Secure Shell (SSH)** mentioned

#### Rebooting and Shutting Down
* **shutdown** is the preferred method; **init** then controls shutdown or reboot
* **halt** and **poweroff** commands issue a `shutdown -h` to halt the system, while **reboot** issues `shutdown -r`; both require superuser (root) access, as in:
  * `$ sudo shutdown -h 10:00 "Shutting down for scheduled maintenance."`

#### Locating Applications
* most executables should live in `/bin`, `/usr/bin`, `/sbin`, `/usr/sbin`, or `/opt`
* use **which** to find programs
  * example: `$ which python`
  > /usr/bin/python
* use **whereis** to search a broader range of directories; includes **source** and **man** files packaged with the program
  * example: `$ whereis python`
  > python: /usr/bin/python2.7 /usr/bin/python /usr/lib/python2.7 /usr/lib64/python2.7 /etc/python /usr/include/python2.7 /usr/share/man/man1/python.1.gz

#### Accessing Directories
* `$ echo $HOME` to see path of default directory
* `$ pwd` to see present working directory
* `$ cd ~` or `$ cd` to change to home directory
* `$ cd ..` to change to parent directory
* `$ cd -` to change to previous directory

#### Understanding Absolute and Relative Paths
* an **absolute pathname** always begins with `/`
* a **relative pathname** never starts with `/`
* `////usr//bin` is seen as `/usr/bin` by the system
* `.` == present directory
* `..` == parent directory
* `~` == home directory

#### Exploring the FileSystem
* `$ cd /` change to root directory
* `$ ls` list contents of present working directory
* `$ ls -a` list all files including **hidden** files (those whose name starts with .)
* `$ tree` display a tree view of filesystem
* `$ tree -d` show only directories and not file names

#### Hard File Links
* `$ ln file1 file2` creates a **hard link**
  * file1 is the original file
  * file2 is created
* `$ ls -li file1 file2` indicates that the **inode** number, a unique quantity for each file object, is the same for both, so really it's **one** file
  * `-i` flag for showing inode
  * the 2 in the results indicates there are two names associated with the file
* deleting one of the files, the **inode object** and other file name will remain
* when editing, **vi** and **gedit** will retain the link, but other editors might break it, resulting in creation of two objects

#### Soft (Symbolic) Links
* `$ ln -s file1 file3`
  * file3 has a different **inode** number
* symbolic links take no extra space on filesystem
* can point to objects on different filesystems or partitions; if source is not available or does not exist, link is called a **dangling** link

#### Navigating the Directory History
* `$ cd -` will go to last directory visited
* `$ pushd .` pushes starting directory to a list
* `$ popd .` move to directories on list in reverse order (most recent first)
* `$ dirs` to see the list

### Lab 7.2: Locating Applications
Find out the location of the ip network utility.  
My solution:
* `$ which ip`
> /usr/sbin/ip
* `$ whereis ip`
> ip: /usr/sbin/ip /usr/share/man/man7/ip.7.gz /usr/share/man/man8/ip.8.gz

### 7.3 Working with Files

#### Viewing Files

**Command** | **Usage**
----------- | ---------
cat         | used for viewing files that aren't long
tac         | used to look at a file backwards, starting with last line
less        | used to view larger files; pauses at each screen full of text; use / to search forward and ? to search backward; **more** is an older program that is similar
tail        | used to print last 10 lines of file by default; use -n 15 or -15 to look at last 15 lines, for instance
head        | opposite of tail

#### touch and mkdir
* **touch** is often used to set or update the access, change, and modify times of files; it resets a file's time stamp to match the current time
  * also used to create an empty file: `$ touch <filename>`
  * can set timestamp of file: `$ touch -t 06302000 <filename>` # June 30, 20:00
* **mkdir** is used to create directories
* **rmdir** is used to remove empty directories, use `$ rm -rf` for directories with data

#### Removing a File

**Command** | **Usage**
----------- | ---------
mv          | rename a file
rm          | remove a file
rm -f       | forcefully remove a file
rm -i       | interactively remove a file (good practice when using patterns)

#### Renaming or Removing a Directory

**Command** | **Usage**
----------- | ---------
mv          | rename a directory
rmdir       | remove an empty directory
rm -rf      | forcefully remove a directory recursively

#### Modifying the Command Line Prompt
* the **PS1** variable is the character string that is displayed as the prompt on the command line
* example: `student@quad32 $` could be set with `\u@\h \$`
`$ echo $PS1`
> \$
`$ PS1="\u@\h \$ "`
`coop@quad64 $ echo $PS1`
> \u@\h \$
`coop@quad64 $` 

### Lab 7.3: Creating, Moving, and Removing Files
*  Create an empty file named exercise.txt and move this file to the /tmp directory, using a relative pathname from your home directory. Then, delete this file using an absolute pathname.
* My solution:
  * `$ touch exercise.txt`
  * `$ mv exercise.txt ../../../../tmp/`
  * `$ rm /tmp/exercise.txt`


### 7.4: Searching for Files

#### Standard File Streams
Three standard **file streams** (or **descriptors**) are always open for use:  

**Name**        | **Symbolic Name** | **Value** | **Example**
--------        | ----------------- | --------- | -----------
standard input  | stdin             | 0         | keyboard
standard output | stdout            | 1         | terminal
standard error  | stderr            | 2         | log file

* **stdin** is your keyboard or supplied from output of a previous commmand via a **pipe**
* **stdout** is printed on the terminal or redirected into a file
* **stderr** is usually redirected to an error logging file
* Other open files will start at file descriptor 3 and increase from there

#### I/O Redirection
* assume we have a program called **do_something** that reads from **stdin** and writes to **stdout** and **stderr**
  * change its input source:
`$ do_something < input-file`
  * send its output to a file:
`$ do_something > output-file`
  * send its error messages to a file using the file descriptor:
`$ do_something 2> error-file`
  * put error messages to the same place as file descriptor 1:
`$ do_something > all-output-file 2>&1`
  * the previous in **bash**:
`$ do_something >& all-output-file`

#### Pipes
* a **pipeline**:
`$ command1 | command2 | command3`
  * _command2 and command3 do not have to wait for previous commands to complete_
  * this allows short and simple programs to be combined to produce complex results

#### locate
* **locate** searches a database constructed by **updatedb** (usually run once per day)
* example: `$ locate zip | grep bin`

#### Wildcards and Matching File Names

**Wildcard** | **Result**
------------ | ----------
?            | matches any single character
\*           | matches any string of characters
[set]        | matches any character in the set
[!set]       | matches any character not in the set

This is just regex  

#### Using find
* without args, `$ find` lists all files in current directory and all of its subdirectories7.4 - find
* common options:
  * `-name` for file name
  * `-iname` ignore case of file name
  * `-type` (`d` for directory, `l` for link, `f` for file)
* example use:
  * `$ find /usr -name gcc`
  * `$ find /usr -type d -name gcc`
  * `$ find /usr -type f -name gcc`

#### Using Advanced find Options
* `-exec` to run commands on matched files
* example: find and remove all files ending with .swp:
  * `$ find -name "*.swp" -exec rm {} ';'`7.4 - a
  * {} place holder for file names resulting from search
  * must end with ';' or '\;'
* `-ok` acts like exec but prompts for permission

#### Finding Files Based on Time and Size
* time, such as `$ find / -ctime 3`
  * `-ctime` for inode metadata (e.g., file ownership, permissions, etc.) last changed
  * `-atime` for last accessed or read
  * `-mtime` for last modified or written
  * the number **n** is for days, **+n** for larger than n, **-n** for less than n
  * for minutes, use `-cmin`, `-amin`, `-mmin`
* size, such as `$ find / -size 0`
  * default number is in 512-byte blocks
  * **c** for bytes, **k** for kilobytes, **M** for megabytes, **G** for gigabytes, etc.
  * can also use exact numbers as with time, **n**, **+n**, or **-n**

#### Try-It-Yourself: Using find
Tasks to be performed:
* earch for a file with name gcc in the /usr folder using find command.
* earch for a directory with filename gcc, in the /usr folder using find command.
* earch for files in the current directory which were modified today.
* Search for files with size 0 bytes.

### Lab 7.4: Finding Directories and Creating Symbolic Links
Find the init.d directory, starting from /, and then create a symbolic link from within your home directory to this directory.  
(Note that this SysVinit directory is no longer used much in systemd-based systems, but is kept for backwards compatibility reasons.)  
My solution:  
`$ find / -name init.d -type d 2>&1 | grep -v 'Permission denied'` (I could also use **sudo** to not see the Permission denied errors)  
`$ cd ~`  
`$ ln -s /etc/rc.d/init.d .`  

### 7.5: Installing Software
See LFS201 Chapters 5-7 for more details  

#### Package Managers: Two Levels
Both package management systems provide two tool levels:  
* a low-level tool (such as dpkg or rpm) takes care of the details of unpacking individual packages, running scripts, and getting the software installed correctly
* a high-level tool (such as apt-get, yum, or zypper) works with groups of packages, downloads packages from the vendor, and figures out dependencies.  

### Lab 7.5: Installing and Removing Software Packages
Using the upper level package management system appropriate for your Linux distribution, do the following:
* Install the dump package on your system.
* Remove the dump package from your system.
NOTE: If dump is already installed (you will be told so when you try to install), then do things in opposite order, i.e., remove and then install.
* My solution:
* `sudo yum install dump`
* `sudo yum remove dump`

### 7.6: Summary
Key concepts covered:
* Virtual terminals (VT) in Linux are consoles, or command line terminals that use the connected monitor and keyboard.
* Different Linux distributions start and stop the graphical desktop in different ways.
* A terminal emulator program on the graphical desktop works by emulating a terminal within a window on the desktop.
* The Linux system allows you to either log in via text terminal or remotely via the console.
* When typing your password, nothing is printed to the terminal, not even a generic symbol to indicate that you typed.
* The preferred method to shut down or reboot the system is to use the **shutdown** command.
* There are two types of **pathnames**: absolute and relative.
  * An absolute pathname begins with the root directory and follows the tree, branch by branch, until it reaches the desired directory or file.
  * A relative pathname starts from the present working directory.
  * Using **hard** and **soft** (**symbolic**) links is extremely useful in Linux.
* **cd** remembers where you were last, and lets you get back there with `cd -`.
* **locate** performs a database search to find all file names that match a given pattern.
* **find** locates files recursively from a given directory or set of directories.
* **find** is able to run commands on the files that it lists, when used with the `-exec` option.
* **touch** is used to set the access, change, and edit times of files, as well as to create empty files.
* The **Advanced Packaging Tool** (**apt**) package management system is used to manage installed software on Debian-based systems.
* You can use the **Yellowdog Updater Modified** (**yum**) open source command-line package-management utility for **RPM**-compatible Linux operating systems.
* The **zypper** package management system is based on RPM and used for openSUSE.