LFS101 Introduction to Linux
=====

[Chapter 3: Linux Basics and System Startup](lfs101_chapter_notes/lfs101-03-basics.md)  
   * [3.1: The Boot Process](lfs101_chapter_notes/lfs101-03-basics.md#31-the-boot-process)  
   * [3.2: Kernel, init, and Services](lfs101_chapter_notes/lfs101-03-basics.md#32-kernel-init-and-services)  
   * [3.3: Linux Filesystem Basics](lfs101_chapter_notes/lfs101-03-basics.md#33-linux-filesystem-basics)  
   * [3.4: Linux Distribution Installation](lfs101_chapter_notes/lfs101-03-basics.md#34-linux-distribution-installation)  
   
[Chapter 4: Graphical Interface](lfs101_chapter_notes/lfs101-04-graphical_interface.md)  
   * 
[Chapter 7: Command Line Operations](lfs101_chapter_notes/lfs101-07-command_line.md)  
[Chapter 8: Finding Linux Documentation](lfs101_chapter_notes/lfs101-08-documentation.md)  
[Chapter 9: Processes](lfs101_chapter_notes/lfs101-09-processes.md)  
[Chapter 10: File Operations](lfs101_chapter_notes/lfs101-10-file_operations.md)  
[Chapter 11: Text Editors](lfs101_chapter_notes/lfs101-11-text_editors.md)  
[Chapter 12: User Environment](lfs101_chapter_notes/lfs101-12-user_environment.md)  
[Chapter 13: Manipulating Text](lfs101_chapter_notes/lfs101-13-manipulating_text.md)  
[Chapter 14: Network Operations](lfs101_chapter_notes/lfs101-14-network_operations.md)  
[Chapter 15: Bash Shell Scripting I](lfs101_chapter_notes/lfs101-15-bash_scripting_1.md)  
[Chapter 16: Bash Shell Scripting II](lfs101_chapter_notes/lfs101-16-bash_scripting_2.md)  
[Chapter 17: Printing](lfs101_chapter_notes/lfs101-17-printing.md)  
[Chapter 18: Local Security Principles](lfs101_chapter_notes/lfs101-18-security_principles.md)  

Linux filesystem and paths
-----

### Linux paths
`////usr//bin` (7.2)  
`.` (7.2)  
`..` (7.2)  
`~` (7.2)  


### Linux commands
`$ gnome-tweak-tool`  
`$ cat [FILE]`  
`$ head [FILE]`  
`$ tail [FILE]`  
`$ man _name_`  
**pipe** |  
`[student@centos ~]$ su  
Password:  
[root@centos student]#`  
`$ sudo systemctl stop gdm`  (or `$ sudo telinit 3`)  
`$ sudo systemctl start gdm`  (or `$ sudo telinit 5`)  
(use **lightdm** instead of **gdm** on Ubuntu)  
`$ sudo shutdown -h 10:00 "Shutting down for scheduled maintenance."` (7.2)  
`$ halt` (7.2)  
`$ poweroff` (7.2)  
`$ shutdown -r` (7.2)  
`$ cd /` (7.2)  
`$ ls` (7.2)  
`$ ls -a` (7.2)  
`$ tree` (7.2)  
`$ tree -d` (7.2)  
`$ ln file1 file2` (7.2)  
`$ ls -li file1 file2` (7.2)  
`$ ln -s file1 file3` (7.2)  
`$ cd -` (7.2)  
`$ pushd .` (7.2)  
`$ popd .` (7.2)  
`$ dirs` (7.2)  
`$ cat` (7.3)  
`$ tac` (7.3)  
`$ less` (7.3)  
`$ tail` (7.3)  
`$ tail -n 15` (7.3)  
`$ tail -15` (7.3)  
`$ head` (7.3)  
`$ touch <filename>` (7.3)  
`$ touch -t 06302000 <filename>` (7.3)  
`$ mkdir <dirname>` (7.3)  
`$ rmdir <dirname>` (7.3)  
`$ rm -rf` (7.3)  
`$ mv <original> <new>` (7.3)  
`$ rm` (7.3)  
`$ rm -f` (7.3)  
`$ rm -i` (7.3)  
`$ PS1="\u@\h \$ "` (7.3)  
`$ do_something < input-file` (7.4)  
`$ do_something > output-file` (7.4)  
`$ do_something 2> error-file` (7.4)  
`$ do_something > all-output-file 2>&1` (7.4)  
`$ do_something >& all-output-file` (7.4)  
`$ locate zip | grep bin` (7.4)  
`$ cp <original_file> <new_file>` (7.4)  
`$ find` with `-name`, `-iname`, `-type` (`d` for directory, `l` for link, `f` for file), `-exec` (7.4)  
`$ find /usr -name gcc` (7.4)  
`$ find /usr -type d -name gcc` (7.4)  
`$ find /usr -type f -name gcc` (7.4)  
`$ find -name "*.swp" -exec rm {} ';'` or `-ok` instead (7.4)  
`$ find / -ctime 3` or `-atime`, `-mtime` with **n**, **+n**, or **-n**; `-cmin`, `-amin`, `-mmin` (7.4)  
`$ find / -size 0` with **c**, **k**, **M**, **G**, etc. or with **n**, **+n**, or **-n** (7.4)  
`$ find / -name init.d -type d 2>&1 | grep -v 'Permission denied'` (7.4)  
`$ man -f` same as typing **whatis** (8.2)  
`$ man -k` same as typing **apropos** (8.2)  
`$ man 3 printf` (8.2)  
`$ man -a printf` (8.2)  
`$ info cpio` (8.3)  
`$ help` (8.4)  
`$ help <command>` (8.4)  
`$ mkdir --help` (8.4)  
`$ gnome-help` or `$ yelp` (8.5)  
`$ khelpcenter` (8.5)  
`$ yelp man:printf` (8.5)  
`$ kill -SIGKILL <pid>` (9.1)  
`$ kill -9 <pid>` (9.1)  
#### 9.2
`$ w`  
`$ top`  
`$ uptime`  
`$ updatedb &`  
`$ fg <job>`  
`$ bg <job>`  
`$ jobs`  
`$ jobs -l`  
`$ top | head`  
#### 9.3
`$ ps`  
`$ ps -u <username>`  
`$ ps -ef`  
`$ ps -eLf`  
`$ ps aux`  
`$ ps axo <attributes>`  
`$ pstree`  
`$ top`  
#### 9.4
`$ at now + 2 days`  
`$ at now + 1 minute`  
`$ atq`  
`$ atrm 1`  
`$ crontab -e`  
`$ crontab -l`  
`$ sudo ls -l /var/spool/cron/student`  
`$ sudo cat /var/spool/cron/student`  
`$ crontab -r`  
`$ sleep NUMBER[SUFFIX]...`  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  
`$ `  