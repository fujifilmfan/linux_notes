Chapter 8: Finding Linux Documentation
--------------------------------------

[8.0: Introduction/ Learning Objectives](#8.0:-Introduction/-Learning-Objectives)  
[8.1: Documentation Sources](#8.1:-Documentation-Sources)  
[8.2: The man pages](#8.2:-The-man-pages)  
[8.3: GNU Info](#8.3:-GNU-Info)  
[8.4: The --help Option and Help Command](#8.4:-The---help-Option-and-Help-Command)  
[8.5: Other Documentation Sources](#8.5:-Other-Documentation-Sources)  
[8.6: Summary](#8.6:-Summary)  

### 8.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
* Use different sources of documentation.
* Use the **man pages**.
* Access the GNU **info** system.
* Use the **help** command and `--help` option.
* Use other documentation sources.

### 8.1: Documentation Sources

#### Introduction to Linux Documenation Sources
Important Linux documentation sources include:
* The **man pages** (short for manual pages)
* GNU **Info**
* The **help** command and `--help` option
* Other Documentation Sources, e.g. https://www.gentoo.org/doc/en/ or https://help.ubuntu.com/community/CommunityHelpWiki

### 8.2: The man pages

#### The man pages
Some Linux distributions require every installed program to have a corresponding **man** page, which explains the depth of coverage.  
**man** pages are often converted to:
* web pages (See http://man7.org/linux/man-pages/)
* published books
* graphical help
* other formats

#### man
* with no options, you see only the dedicated page about the topic
* with `-f`, view all pages containing a string in their name
* with `-k`, view all pages that discuss a specfied subject (even if subject not in name)
* `$ man -f` same as typing **whatis**
* `$ man -k` same as typing **apropos**

#### Manual Chapters
* **man pages** are divided into nine numbered chapters
* `$ man 3 printf` # shows chapter 3 of the printf man page
* `$ man -a printf` # shows all manual pages with the name printf
> --Man-- next: printf(1p) [ view (return) | skip (Ctrl-D) | quit (Ctrl-C) ]  
> --Man-- next: printf(3) [ view (return) | skip (Ctrl-D) | quit (Ctrl-C) ]  
> --Man-- next: printf(3p) [ view (return) | skip (Ctrl-D) | quit (Ctrl-C) ]  

### Lab 8.2 (Lab 1): Working with man
1. Finding man pages
From the command line, bring up the man page for man itself. Scroll down to the EXAMPLES section.
   * My solution:
   * `$ man man`
2. Finding man Pages by Topic
What man pages are available that document file compression?
   * My solution:
   * `$ man -k compression`
> Compress::Raw::Bzip2 (3pm) - Low-Level Interface to bzip2 compression library  
> Compress::Raw::Zlib (3pm) - Low-Level Interface to zlib compression library  
> Compress::Zlib (3pm) - Interface to zlib compression library  
> SSL_COMP_add_compression_method (3ssl) - handle SSL/TLS integrated compression methods  
> SSL_COMP_free_compression_methods (3ssl) - handle SSL/TLS integrated compression methods  
>  zlib (3)             - compression/decompression library  

3. man Pages by Section
From the command line, bring up the man page for the printf library function. In which manual page section are library functions found?
   * My solution:
   * `$ man printf` # Section 1 - this is the command line utility
   * Correct solution:
   * `$ man 3 printf` # Section 3

Here are the sections from the man man page:
1. Executable programs or shell commands
2. System calls (functions provided by the kernel)
3. Library calls (functions within program libraries)
4. Special files (usually found in /dev)
5. File formats and conventions eg /etc/passwd
6. Games
7. Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
8. System administration commands (usually only for root)
9. Kernel routines [Non standard]

### 8.3: GNU Info

#### GNU Info System
* alternative to **man**
* topics connected by links
* accessible via command line, graphical help utility, printed, or viewed online

#### Command Line Info Browser
* typing info with no arguments in a terminal window displays an index of available topics
* you can view help for a particular topic by typing `info <topic name>`

#### info Page Structure
* topic which you view in the info page is called a **node**
* nodes might contain menus and linked subtopics, or **items**, are like hyperlinks and identified by an asterisk (**\***) at the beginning of the item name
* named items outside a menu are identified with double-colons(**::**) at the end of the item name
* items can refer to other nodes within the file or to other files
Helpful keystrokes:
Key   | Function
----- | --------
n     | go to next node
p     | go to previous node
u     | move one node up in the index
q     | quit
h     | help
Enter | select a menu item

### Lab 8.3 (Lab 2): Working with info
From the command line, bring up the info page for cpio. Bring up the tutorial.
* My solution:
* `$ info cpio`, then select "Tutorial" from the menu

### 8.4: The --help Option and Help Command

#### Introduction to the --help Option
* short description accessed with the **--help** or **-h** flags

#### The help Command
* type `$ help` to see a synopsis of built-in commands
* type `$ help <command>`
* for these built-in commands, help performs the same basic function as the **-h** and **--help** arguments perform for stand-alone programs

### Lab 8.4 (Lab 3): Working with Command Line help
List the available options for the mkdir command, in more than one way.  
* My solution:
* `$ help mkdir`
> no help topics match 'mkdir'
* `$ man -k mkdir`
> gvfs-mkdir (1)       - Create directories
> mkdir (1)            - make directories
> mkdir (1p)           - make directories
> mkdir (2)            - create a directory
> mkdir (3p)           - make a directory
> mkdirat (2)          - create a directory relative to a directory file descriptor
* `$ man -f mkdir`
> mkdir (1)            - make directories
> mkdir (1p)           - make directories
> mkdir (3p)           - make a directory
> mkdir (2)            - create a directory
* `$ man mkdir`
* `$ info mkdir`
* Another answer:
* `$ mkdir --help`

### 8.5: Other Documentation Sources

#### Other Documentation Sources
* desktop help system
  * GNOME: within graphical terminal, type `$ gnome-help` or `$ yelp`
  * KDE: `$ khelpcenter`
* package documentation
  * found in `/usr/share/doc`
* online resources
  * LinuxCommand.org: http://linuxcommand.org/tlcl.php
  * Ubuntu: https://help.ubuntu.com/
  * CentOS: https://www.centos.org/docs/ points user to https://access.redhat.com/documentation/en-us/
  * OpenSUSE: http://en.opensuse.org/Portal:Documentation
  * GENTOO: http://www.gentoo.org/doc/en

### Lab 8.5 (Lab 4): Working with Graphical Help Systems
Find the graphical help system on your desktop, and try to locate within it the **man** pages for **printf**. This may be difficult, so do not waste too much time before looking at the suggestions below. 
* My solution:
  * I couldn't find any man pages in the gnome help
  * I used the tip in the lab and ran `$ yelp man:printf`
  * in location bar, hit **CTRL-l** and then type **man:printf**, and it works fine (bug?)
  * do the same for info pages as well

### 8.6: Summary
Key concepts covered:
* The main sources of Linux documentation are the **man pages**, **GNU Info**, the **help** options and command, and a rich variety of online documentation sources. 
* The **man** utility searches, formats, and displays **man pages**.
* The **man pages** provide in-depth documentation about programs and other topics about the system, including configuration files, system calls, library routines, and the kernel.
* The **GNU Info System** was created by the **GNU** project as its standard documentation. It is robust and is accessible via command line, web, and graphical tools using info.
* Short descriptions for commands are usually displayed with the `-h` or `--help` argument.
* You can type **help** at the command line to display a synopsis of built-in commands.
* There are many other help resources both on your system and on the Internet.