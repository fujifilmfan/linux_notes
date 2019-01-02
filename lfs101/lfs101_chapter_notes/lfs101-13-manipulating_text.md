Chapter 13: Manipulating Text
-----

[13.0: Introduction/ Learning Objectives](#130-introduction-learning-objectives)  
[13.1: cat and echo](#131-cat-and-echo)  
[13.2: Working with Large and Compressed Files](#132-working-with-large-and-compressed-files)  
[13.3: sed and awk](#133-sed-and-awk)  
[13.4: File Manipulation Utilities](#134-file-manipulation-utilities)  
[13.5: grep and strings](#135-grep-and-strings)  
[13.6: Miscellaneous Text Utilities](#136-miscellaneous-text-utilities)  
[13.7: Summary](#137-summary)  
  
### 13.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:  
* Display and append to file contents using **cat** and **echo**. 
* Edit and print file contents using **sed** and **awk**.
* Search for patterns using **grep**.
* Use multiple other utilities for file and text manipulation.


### 13.1: cat and echo

#### Command Line Tools

#### cat
* **cat** is short for *concatenate*  
* used for viewing and printing files  
* view file:  
   `$ cat <filename>`  
* other commands:  
  
Command                       | Usage  
-------                       | -----  
`$ cat file1 file2`           | Concatenate multiple files and display the output; i.e., the entire content of the first file is followed by that of the second file.  
`$ cat file1 file2 > newfile` | Combine multiple files and save the output into a new file.  
`$ cat file >> existingfile`  | Append a file to the end of an existing file.  
`$ cat > file`                | Any subsequent lines typed will go into the file, until **CTRL-D** is typed.  
`$ cat >> file`               | Any subsequent lines are appended to the file, until **CTRL-D** is typed.  

* **tac** prints lines in reverse order:  
   `$ tac file`  
   `$ tac file1 file2 > newfile`  
   
#### Using cat Interactively
* another way to create a file:  
  `$ cat > filename << EOF` (enter EOF at beginning of a line to exit)  
  
#### Using cat
(video)  

#### Try-It-Yourself: Using cat
done  

#### echo
* **echo** displays text:  
   `$ echo string`  
* can put text in:  
   * standard output  
   * new file using >  
   * existing file using >>  
* The `–e` option, along with the following switches, is used to enable special character sequences, such as the newline character or horizontal tab.  
   * `\n`  represents newline  
   * `\t`  represents horizontal tab  
* echo commands:  

Command                         | Usage  
-------                         | -----  
`$ echo string > newfile`       | The specified string is placed in a new file.  
`$ echo string >> existingfile` | The specified string is appended to the end of an already existing file.  
`$ echo $variable`              | The contents of the specified environment variable are displayed.  

#### Try-It-Yourself: Using echo
done  

### 13.2: Working with Large and Compressed Files

#### Working with Large Files
* **less** is used to view large files without reading the whole thing into memory  
   `$ less <filename>`  
   `$ cat <filename> | less`  
* **man** is sent through **less** by default  
* **more** is older and less capable than **less**  
   
#### head
* **head** reads the first lines of each named file and displays on STDOUT  
   `$ head –n 5 grub.cfg` to display first five lines  

#### tail
* **tail** reads the last lines of each named file and displays on STDOUT  
   `$ tail -n 15 atmtrans.txt` to display last fifteen lines  
   `$ tail -f atmtrans.txt` for continuous display of last lines  
   
#### Try-It-Yourself: Using head and tail
done

#### Viewing Compressed Files
* utilities used for working with **gzip** compressed files are prefixed by a **z**  
* example commands:  

Command                                            | Description  
-------                                            | -----------  
`$ zcat compressed-file.txt.gz`                    | To view a compressed file  
`$ zless <filename>.gz` or `$ zmore <filename>.gz` | To page through a compressed file  
`$ zgrep -i less test-file.txt.gz`                 | To search inside a compressed file  
`$ zdiff filename1.txt.gz filename2.txt.gz`        | To compare two compressed files  

#### Try-It-Yourself: Viewing Compressed Files
done  

### 13.3: sed and awk

#### Introduction to sed and awk

#### sed
* **sed** is short for **stream editor**
* data from the input stream is filtered or modified in the working stream and then written to the output stream  

#### sed Command Syntax
* **-e** allows multiple editing commands  
* sed commands:  

Command                          | Usage  
-------                          | -----  
`$ sed -e command <filename>`    | Specify editing commands at the command line, operate on file and put the output on standard out (e.g., the terminal)  
`$ sed -f scriptfile <filename>` | Specify a scriptfile containing sed commands, operate on file and put output on standard out.  

#### sed Basic Operations
* in the following table, **pattern** is the current string and **replace_string** is the new one  
* sed commands:  

Command                                          | Usage  
-------                                          | -----  
`$ sed s/pattern/replace_string/ file`           | Substitute first string occurrence in a line  
`$ sed s:pattern:replace_string: file`           | Substitute first string occurrence in a line  
`$ sed s/pattern/replace_string/g file`          | Substitute all string occurrences in a line  
`$ sed 1,3s/pattern/replace_string/g file`       | Substitute all string occurrences in a range of lines  
`$ sed -i s/pattern/replace_string/g file`       | Save changes for string substitution in the same file  
`$ sed s/pattern/replace_string/g file1 > file2` | Safer than using **-i**

Example: convert 01/01/... to JAN/FEB/...:  

```
$ sed -e 's/01/JAN/' -e 's/02/FEB/' -e 's/03/MAR/' -e 's/04/APR/' -e 's/05/MAY/' \  
    -e 's/06/JUN/' -e 's/07/JUL/' -e 's/08/AUG/' -e 's/09/SEP/' -e 's/10/OCT/' \  
    -e 's/11/NOV/' -e 's/12/DEC/'  
```  

#### Using sed
(video)  

#### Try-It-Yourself : Using sed
done  

#### awk
* **awk** extracts and prints specific contents of a file  
* features:  
   * It is a powerful utility and interpreted programming language.  
   * It is used to manipulate data files, retrieving, and processing text.  
   * It works well with **fields** (containing a single piece of data, essentially a column) and **records** (a collection of fields, essentially a line in a file).  
* awk commands:  

Command                              | Usage  
-------                              | -----  
`$ awk ‘command’ var=value file`     | Specify a command directly at the command line  
`$ awk -f scriptfile var=value file` | Specify a file that contains the script to be executed along with **f**  

#### awk Basic Operations
* input file is read one line at a time, and, for each line, awk matches the given pattern in the given order and performs the requested action  
* the **-F** option allows you to specify a particular field separator character  
* basic operations with awk:  

Command                                   | Usage  
-------                                   | -----  
`$ awk '{ print $0 }' /etc/passwd`        | Print entire file  
`$ awk -F: '{ print $1 }' /etc/passwd`    | Print first field (column) of every line, separated by a space  
`$ awk -F: '{ print $1 $7 }' /etc/passwd` | Print first and seventh field of every line  


#### Try-It-Yourself : Using awk
done  

### Lab 1: Using sed
```
Search for all instances of the user command interpreter (shell) equal to **/sbin/nologin** in **/etc/passwd** and 
replace them with **/bin/bash**.  
```
* My solution (with help from the solutions for the use of quotes):  
   `$ sed s/'\/sbin\/nologin'/'\/bin\/bash'/g /etc/passwd > passwd_new`  
* Lab solution:  
```
To get output on standard out (terminal screen):
`$ student:/tmp> sed s/'\/sbin\/nologin'/'\/bin\/bash'/g /etc/passwd`

or to direct to a file:
`$ student:/tmp> sed s/'\/sbin\/nologin'/'\/bin\/bash'/g /etc/passwd > passwd_new`

Note this is kind of painful and obscure because we are trying to use the forward slash ( / ) as both a string and a delimiter between fields. One can do instead:
`$ student:/tmp> sed s:'/sbin/nologin':'/bin/bash':g /etc/passwd`
   where we have used the colon ( : ) as the delimiter instead. (You are free to choose your delimiting character!) In fact when doing this we do not even need the single quotes:
`$ student:/tmp> sed s:/sbin/nologin:/bin/bash:g /etc/passwd` works just fine.  
```

### 13.4: File Manipulation Utilities

#### File Manipulation Utilities
* in this section:
   * **sort**  
   * **uniq**  
   * **paste**  
   * **join**  
   * **split**  

#### sort
* **sort** is used to rearrange lines of a text file  
* When used with the **-u** option, **sort** checks for unique values after sorting the records (lines). It is equivalent to running **uniq** on the output of **sort**.  
* example usage:  

Syntax                     | Usage  
------                     | -----  
`$ sort <filename>`        | Sort the lines in the specified file, according to the characters at the beginning of each line.  
`$ cat file1 file2 | sort` | Combine the two files, then sort the lines and display the output on the terminal  
`$ sort -r <filename>`     | Sort the lines in reverse order  
`$ sort -k 3 <filename>`   | Sort the lines by the 3rd field on each line instead of the beginning  

#### uniq
* **uniq** is used to remove duplicate lines in a text file  
* **uniq** requires that the duplicate entries must be consecutive (run **sort** first or use **sort** with **-u** option)
* remove duplicate entries from multiple files:  
   `$ sort file1 file2 | uniq > file3`  
   `$ sort -u fileq file2 > file3`  
* count duplicate entries:  
    `$ uniq -c filename`  

#### Try-It-Yourself: Using sort and uniq
done  

#### paste
* **paste** is used to combine fields from different files or combine lines from multiple files  
* options:  
   * **-d** delimiters, which specify a list of delimiters to be used instead of tabs for separating consecutive values on a single line. Each delimiter is used in turn; when the list has been exhausted, paste begins again at the first delimiter.  
   * **-s**, which causes **paste** to append the data in series rather than in parallel; that is, in a horizontal rather than vertical fashion.  

#### Using paste
* paste contents from two files:  
   * `$ paste file1 file2`  
* use a different delimiter:
   * `$ paste -d, file1 file2`  
* common delimiters in are 'space', 'tab', '|', 'comma', etc.  
  
#### join
* **join** is used to combine files without repeating columns, basically an enhanced **paste**  

#### Using join
* to combine two files on a common field:  
   `$ join file1 file2`  
? what happens when the two columns are not *exactly* the same?  

#### split
* **split** is used to break up large files  
* default is 1000-line segments  
* original file remains intact; new files are created with an added prefix  
* example usage:  
   `$ split infile`  
* use a different prefix:  
   `$ split infile <Prefix>`  

#### Using split
* example:  
   `$ wc -l american-english`  
   > 99171 american-english  
   
   `$ split american-english dictionary`  

#### Regular Expressions and Search Patterns
(skip)  

#### Using Regular Expressions and Search Patterns
* example matches for **the quick brown fox jumped over the lazy dog**:  

Command  | Usage  
-------  | -----  
a..      | matches azy  
b.|j.    | matches both br and ju  
..$      | matches og  
l.\*     | matches lazy dog  
l.\*y    | matches lazy  
the.\*   | matches the whole sentence  

### Lab 2: Parsing Files with awk (and sort and uniq)
```
Generate a column containing a unique list of all the shells used for users in /etc/passwd.
You may need to consult the manual page for /etc/passwd as in:
student:/tmp> man 5 passwd
Which field in /etc/passwd holds the account’s default shell (user command interpreter) ?
How do you make a list of unique entries (with no repeats.)
```
* My solution:  
   `$ awk -F: '{ print $7 }' /etc/passwd | sort | uniq`  
   > /bin/bash  
   > /bin/sync  
   > /sbin/halt  
   > /sbin/nologin  
   > /sbin/shutdown  
   
   `$ awk -F: '{ print $7 }' /etc/passwd | sort -u`
   
* Lab solution:  
```
The field in /etc/passwd that holds the shell is number 7. To display the field holding the shell in /etc/passwd using awk and produce a unique list:

$ awk -F: '{print $7}' /etc/passwd | sort -u
or
$ awk -F: '{print $7}' /etc/passwd | sort | uniq

For example:
$ awk -F: '{print $7}' /etc/passwd | sort -u
/bin/bash
/bin/sync
/sbin/halt
/sbin/nologin
/sbin/shutdown
```

### 13.5: grep and strings

#### grep
* **grep** is used to search text  
* grep commands:  

Command                            | Usage
-------                            | -----
`$ grep [pattern] <filename>`      | Search for a pattern in a file and print all matching lines  
`$ grep -v [pattern] <filename>`   | Print all lines that do not match the pattern  
`$ grep [0-9] <filename>`          | Print the lines that contain the numbers 0 through 9  
`$ grep -C 3 [pattern] <filename>` | Print context of lines (specified number of lines above and below the pattern) for matching the pattern. Here, the number of lines is specified as 3.  

#### strings
* **strings** is used to extract printable character strings; useful for looking for human-readable content in binaries, for instance  
* for example, to search for *my_string* in a spreadsheet:  
   `$ strings book1.xls | grep my_string`  

### Lab 3: Using grep
```
In the following we give some examples of things you can do with the grep command; your task is to experiment with these examples and extend them.
1. Search for your username in file /etc/passwd .
2. Find all entries in /etc/services that include the string ftp:
3. Restrict to those that use the tcp protocol.
4. Now restrict to those that do not use the tcp protocol, while printing out the line number
5. Get all strings that start with ts or end with st.
```
* My solution:
   1. `$ grep student /etc/passwd`  
      > student:x:1000:1000:LF Student:/home/student:/bin/bash
   2. `$ grep ftp /etc/services`  
      example output:  
      > ftp             21/sctp                 # FTP  
      > ni-ftp          47/tcp                  # NI FTP  
      > ni-ftp          47/udp                  # NI FTP  
      > bftp            152/tcp                 # Background File Transfer Program  
      > bftp            152/udp                 # Background File Transfer Program  
   3. `$ grep -i ftp /etc/services | grep -i /tcp`  
      example output:  
      > ftp-agent       574/tcp                 # FTP Software Agent System  
      > pftp            662/tcp                 # PFTP  
      > ftps-data       989/tcp                 # ftp protocol, data, over TLS/SSL  
   4. `$ grep -i ftp /etc/services | grep -iv /tcp`  
      example output:  
      > ftp-agent       574/udp                 # FTP Software Agent System  
      > pftp            662/udp                 # PFTP  
      > ftps-data       989/udp                 # ftp protocol, data, over TLS/SSL  
   5. `$ grep -e ^ts /etc/services`  
      `$ grep -e st$ /etc/services`  
* Lab solution:
```
1. student:/tmp> grep student /etc/passwd
2. student:/tmp> grep ftp /etc/services
3. student:/tmp> grep ftp /etc/services | grep tcp
4. student:/tmp> grep -n ftp /etc/services | grep -v tcp
5. student:/tmp> grep -e ^ts -e st$ /etc/services 
```

### 13.6: Miscellaneous Text Utilities

#### tr
* **tr** is used to **translate** specified characters into others or delete them  
* syntax (optional items in brackets):  
   `$ tr [option] set1 [set2]`  
* commands:  

Command                                                      | Usage  
-------                                                      | -----  
`$ tr abcdefghijklmnopqrstuvwxyz ABCDEFGHIJKLMNOPQRSTUVWXYZ` | Convert lower case to upper case  
`$ tr '{}' '()' < inputfile > outputfile`                    | Translate braces into parenthesis  
`$ echo "This is for testing" | tr [:space:] '\t'`           | Translate white-space to tabs  
`$ echo "This   is   for    testing" | tr -s [:space:]`      | Squeeze repetition of characters using -s
`$ echo "the geek stuff" | tr -d 't'`                        | Delete specified characters using -d option  
`$ echo "my username is 432234" | tr -cd [:digit:]`          | Complement the sets using -c option  
`$ tr -cd [:print:] < file.txt`                              | Remove all non-printable character from a file  
`$ tr -s '\n' ' ' < file.txt`                                | Join all the lines in a file into a single line  

#### Try-It-Yourself : Using tr
done  

#### tee
* **tee** takes output from any command and sends it to STDOUT and also writes it to a file
* example:  
   `$ ls -l | tee newfile`  
   
#### wc
* **wc** (word count) counts lines, words, and characters  
* example:  
   `$ wc -l filename`  
* options:  

Option | Description  
------ | -----------  
–l     | Displays the number of lines.  
-c     | Displays the number of bytes.  
-w     | Displays the number of words.  

#### Try-It-Yourself: Using wc
done  

#### cut
* **cut** is used for manipulating column-based files and is designed to extract specific columns  
* to display third column delimited by a blank space:  
   `$ ls -l | cut -d" " -f3`

### Lab 4: Using tee
```
The tee utility is very useful for saving a copy of your output while you are watching it being generated.
Execute a command such as doing a directory listing of the /etc directory:
student:/tmp> ls -l /etc
while both saving the output in a file and displaying it at your terminal.
```
* My solution:  
   `$ ls -l /etc | tee ls_output`  
* Lab solution:  
```
student:/tmp> ls -l /etc | tee /tmp/ls-output
student:/tmp> less /tmp/ls-output

total 2948
drwxr-xr-x.  3 root root     4096 Nov  3 07:27 abrt
-rw-r--r--.  1 root root       16 Jan 15  2015 adjtime
-rw-r--r--   1 root root     1518 Jun  7  2013 aliases
-rw-r--r--.  1 root root    12288 Nov  3 07:49 aliases.db
drwxr-xr-x.  2 root root     4096 Nov  3 07:26 alsa
drwxr-xr-x.  2 root root     4096 Jan 20 07:28 alternatives
-rw-------   1 root root      541 Feb 23  2016 anacrontab
-rw-r--r--   1 root root       55 Jun  6  2016 asound.conf
-rw-r--r--   1 root root        1 May 23  2016 at.deny
drwxr-xr-x.  2 root root     4096 Nov  3 07:26 at-spi2
drwxr-x---.  3 root root     4096 Nov  3 07:26 audisp
drwxr-x---.  3 root root     4096 Nov  3 07:26 audit
drwxr-xr-x.  4 root root     4096 Nov  3 07:32 avahi
drwxr-xr-x.  2 root root     4096 Jan 18 06:59 bash_completion.d
-rw-r--r--   1 root root     2853 May  4  2016 bashrc
drwxr-xr-x.  2 root root     4096 Nov  7 10:20 binfmt.d
drwxr-xr-x   2 root root     4096 Nov  3 07:26 bluetooth
drwxr-xr-x.  2 root root     4096 Apr  9  2015 bonobo-activation
drwxr-xr-x   2 root root    12288 Nov  3 07:26 brltty
-rw-r--r--   1 root root    21929 May  6  2016 brltty.conf
-rw-r--r--   1 root root      676 Jun 23  2016 cgconfig.conf
:
```

### Lab 5: Using wc
```
Using wc (word count), find out how many lines, words, and characters there are in all the files in /var/log that have the .log extension.
```
* My solution:  
   `$ ls -l /var/log | grep -e \\.log$`  
   ``[student@centos log]$ wc `ls /var/log | grep -e \\.log$` ``  
   ```
   > wc: boot.log: Permission denied  
   >      0      1 292292 lastlog  
   > wc: maillog: Permission denied  
   > wc: tallylog: Permission denied  
   >     32    145   1362 vmware-network.1.log  
   >     27    120   1062 vmware-network.log  
   >    736   7357  60666 vmware-vmsvc.log  
   >     25     75   1000 wpa_supplicant.log  
   >    609   6570  44757 Xorg.0.log  
   >    305   2945  21406 Xorg.9.log  
   > wc: yum.log: Permission denied  
   >   1734  17213 422545 total  
   ```
* Lab solution:  
```
student:/tmp> wc /var/log/*.log

    325    2204   18114 /var/log/boot.log
      0       0       0 /var/log/pm-powersave.log
   3098   17871  125630 /var/log/systemtap.log
   5234  251967 4889056 /var/log/vbox-install.log
wc: /var/log/wpa_supplicant.log: Permission denied
   1313   15374   99968 /var/log/Xorg.0.log
    150    1185    8520 /var/log/Xorg.1.log
    318    2851   20868 /var/log/Xorg.9.log
wc: /var/log/yum.log: Permission denied
  10438  291452 5162156 total
```

### 13.7: Summary
You have completed this chapter. Let’s summarize the key concepts covered:
* The **command line** often allows the users to perform tasks more efficiently than the GUI.
* **cat**, short for **concatenate**, is used to read, print, and combine files.
* **echo** displays a line of text either on standard output or to place in a file.
* **sed** is a popular **stream editor** often used to filter and perform substitutions on files and text data streams.
* **awk** is an interpreted programming language, typically used as a data extraction and reporting tool.
* **sort** is used to sort text files and output streams in either ascending or descending order.
* **uniq** eliminates duplicate entries in a text file.
* **paste** combines fields from different files. It can also extract and combine lines from multiple sources.
* **join** combines lines from two files based on a common field. It works only if files share a common field.
* **split** breaks up a large file into equal-sized segments.
* **Regular expressions** are text strings used for **pattern matching**. The pattern can be used to search for a specific location, such as the start or end of a line or a word.
* **grep** searches text files and data streams for patterns and can be used with regular expressions.
* **tr** translates characters, copies standard input to standard output, and handles special characters.
* **tee** saves a copy of standard output to a file while still displaying at the terminal.
* **wc** (**word count**) displays the number of lines, words, and characters in a file or group of files.
* **cut** extracts columns from a file.
* **less** views files a page at a time and allows scrolling in both directions.
* **head** displays the first few lines of a file or data stream on standard output. By default, it displays 10 lines.
* **tail** displays the last few lines of a file or data stream on standard output. By default, it displays 10 lines.
* **strings** extracts printable character strings from binary files.
* The **z** command family is used to read and work with compressed files.


PATHS:  

COMMANDS:  
13.1  
`$ cat <filename>`  
`$ cat file1 file2`  
`$ cat file1 file2 > newfile`  
`$ cat file >> existingfile`  
`$ cat > file`  
`$ cat >> file`  
`$ tac file`  
`$ tac file1 file2 > newfile`  
`$ cat > filename << EOF` (enter EOF at beginning of a line to exit)  
`$ echo string`  
`$ echo -e "string \n new line"`  
`$ echo -e "string \t tab"`  
`$ echo string > newfile`  
`$ echo string >> existingfile`  
`$ echo $variable`  

13.2 
`$ less <filename>`  
`$ cat <filename> | less`  
`$ head –n 5 grub.cfg`  
`$ tail -n 15 atmtrans.txt`  
`$ tail -f atmtrans.txt`  
`$ zcat compressed-file.txt.gz`  
`$ zless <filename>.gz`  
`$ zmore <filename>.gz`  
`$ zgrep -i less test-file.txt.gz`  
`$ zdiff filename1.txt.gz filename2.txt.gz`  

13.3  
`$ sed -e command <filename>`  
`$ sed -f scriptfile <filename>`  
`$ sed s/pattern/replace_string/ file`  
`$ sed s:pattern:replace_string: file`  
`$ sed s/pattern/replace_string/g file`  
`$ sed 1,3s/pattern/replace_string/g file`  
`$ sed -i s/pattern/replace_string/g file`  
`$ sed s/pattern/replace_string/g file1 > file2`  
`$ sed -e 's/01/JAN/' -e 's/02/FEB/' -e 's/03/MAR/' -e 's/04/APR/' -e 's/05/MAY/' \  
    -e 's/06/JUN/' -e 's/07/JUL/' -e 's/08/AUG/' -e 's/09/SEP/' -e 's/10/OCT/' \  
    -e 's/11/NOV/' -e 's/12/DEC/'`  
`$ awk ‘command’ var=value file`  
`$ awk -f scriptfile var=value file`  
`$ awk '{ print $0 }' /etc/passwd`  
`$ awk -F: '{ print $1 }' /etc/passwd`  
`$ awk -F: '{ print $1 $7 }' /etc/passwd`  
`$ sed s/'\/sbin\/nologin'/'\/bin\/bash'/g /etc/passwd > passwd_new`  
`$ student:/tmp> sed s:'/sbin/nologin':'/bin/bash':g /etc/passwd`  
`$ student:/tmp> sed s:/sbin/nologin:/bin/bash:g /etc/passwd`  

13.4  
`$ sort <filename>`  
`$ cat file1 file2 | sort`  
`$ sort -r <filename>`  
`$ sort -k 3 <filename>`  
`$ sort file1 file2 | uniq > file3`  
`$ sort -u fileq file2 > file3`  
`$ uniq -c filename`  
`$ paste file1 file2`  
`$ paste -d, file1 file2`  
`$ join file1 file2`  
`$ split infile`  
`$ split infile <Prefix>`  
`$ wc -l american-english`  
`$ split american-english dictionary`  
`$ awk -F: '{ print $7 }' /etc/passwd | sort | uniq`  
`$ awk -F: '{ print $7 }' /etc/passwd | sort -u`  

13.5  
`$ grep [pattern] <filename>`  
`$ grep -v [pattern] <filename>`  
`$ grep [0-9] <filename>`  
`$ grep -C 3 [pattern] <filename>`  
`$ strings book1.xls | grep my_string`  
`$ grep student /etc/passwd`  
`$ grep ftp /etc/services`  
`$ grep -i ftp /etc/services | grep -i /tcp`  
`$ grep -i ftp /etc/services | grep -iv /tcp`  
`$ grep -e ^ts /etc/services`  
`$ grep -e st$ /etc/services`  
`$ grep ftp /etc/services | grep tcp`  
`$ grep -n ftp /etc/services | grep -v tcp`  
`$ grep -e ^ts -e st$ /etc/services`  

13.6  
`$ tr [option] set1 [set2]`  
`$ tr abcdefghijklmnopqrstuvwxyz ABCDEFGHIJKLMNOPQRSTUVWXYZ`  
`$ tr '{}' '()' < inputfile > outputfile`  
`$ echo "This is for testing" | tr [:space:] '\t'`  
`$ echo "This   is   for    testing" | tr -s [:space:]`  
`$ echo "the geek stuff" | tr -d 't'`  
`$ echo "my username is 432234" | tr -cd [:digit:]`  
`$ tr -cd [:print:] < file.txt`  
`$ tr -s '\n' ' ' < file.txt`  
`$ ls -l | tee newfile`  
`$ wc -l filename`  
`$ ls -l | cut -d" " -f3`
`$ ls -l /etc | tee ls_output`  
`$ ls -l /etc | tee /tmp/ls-output`  
`$ ls -l /var/log | grep -e \\.log$`  
``[student@centos log]$ wc `ls /var/log | grep -e \\.log$` ``  
`$ wc /var/log/*.log`  
