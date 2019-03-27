Chapter 17: Printing
--------------------

[17.0: Introduction/ Learning Objectives](#170-introduction-learning-objectives)  
[17.1: Configuration](#171-configuration)  
[17.2: Printing Operations](#172-printing-operations)  
[17.3: Manipulating Postscript and PDF Files](#173-manipulating-postscript-and-pdf-files)  
[17.4: Summary](#174-summary)  
  
### 17.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should know how to:
* Configure a printer on a Linux machine.
* Print documents.
* Manipulate postscript and pdf files using command line utilities.

### 17.1: Configuration
----
#### Introduction to Printing
The Linux standard for printing software is the **Common UNIX Printing System (CUPS)**.  

#### CUPS Overview
**CUPS** works as a **print server** for local and network printers  

#### How Does CUPS Work?
**CUPS** carries out the printing process with the help of its various components:  
* Configuration Files
* Scheduler
* Job Files
* Log Files
* Filter
* Printer Drivers
* Backend

#### Scheduler
**CUPS** is designed around a **print scheduler** that manages print jobs, handles administrative commands, allows users to query the printer status, and manages the flow of data through all **CUPS** components.  

#### Configuration Files
The scheduler reads from configuration files, stored under `/etc/cups`:  
* `cupsd.conf` - system-wide settings, esp. network security  
* `printers.conf` - printer-specific settings  
* `$ ls -l /etc/cups/` - view all config files  

#### Job Files
Print requests are stored as files under `/var/spool/cups`.  
* data files prefixed with **d**; removed after job is handled; data files commonly known as the **print queue**  
* control files prefixed with **c**  
? are control files considered part of the **print queue**, too?  

#### Log Files
* log files are stored in `/var/log/cups`; include access, error, and page records  
* view log files: `$ sudo ls -l /var/log/cups`  

#### Filters, Printer Drivers, and Backends
* **filters** convert job file formats to printable formats  
* printer **drivers** are stored under `/etc/cups/ppd/`  
* the **backend** helps locate connected devices  

#### Installing CUPS

#### Installing CUPS Demonstration
* manual installation:  
   * CentOS: `$ sudo yum install cups`  
   * OpenSUSE: `$ sudo zypper install cups`  
   * Ubuntu: `$ sudo apt-get install cups`  
   
#### Managing CUPS
all management features are wrapped around the **cups** init script, which can be easily started, stopped, and restarted  

#### Managing the CUPS daemon in Ubuntu
(video)  

#### Managing the CUPS daemon in openSUSE
(video)  

#### Managing the CUPS in CentOS
(video)  

#### Configuring a Printer from the GUI
* `$ lsusb` can help troubleshoot connection issues

#### Configuring a Printer in Ubuntu
(video)  

#### Configuring a Printer in openSUSE
(video)  

#### Configuring a Local Printer in CentOS
(video)  

#### Adding Printers from the CUPS Web Interface
* the **CUPS** web interface is available at http://localhost:631

### 17.2: Printing Operations
----
#### Printing from the Graphical Interface

#### Printing from the Command-Line Interface
* you can use either **lp** (System V) or **lpr** (BSD) to print  
* **lp** is a command line front-end to the **lpr** utility  

#### Using lp
* **lp** command and other printing utilities:  

Command                           | Usage  
-------                           | -----  
`lp <filename>`                   | To print the file to default printer  
`lp -d printer <filename>`        | To print to a specific printer (useful if multiple printers are available)  
`program | lp ; echo string | lp` | To print the output of a program  
`lp -n number <filename>`         | To print multiple copies  
`lpoptions -d printer`            | To set the default printer  
`lpoptions help`                  | Obtain list of supported options  
`lpq -a`                          | To show the queue status  
`lpadmin`                         | To configure printer queues  

#### Printing Using lp
(video)  

#### Try-It-Yourself: Printing with the lp Command
done  

#### Managing Print Jobs

Command                        | Usage  
-------                        | -----  
`lpstat -p -d`                 | To get a list of available printers, along with their status  
`lpstat -a`                    | To check the status of all connected printers, including job numbers  
`cancel job-id OR lprm job-id` | To cancel a print job  
`lpmove job-id newprinter`     | To move a print job to new printer  

#### Try-It-Yourself: Managing Print Jobs
done  

### 17.3: Manipulating Postscript and PDF Files
----
#### Working with PostScript
* **PostScript** is a standard **page description language**. It effectively manages scaling of fonts and vector graphics to provide quality printouts.  
* Postscript has been for the most part superseded by the **PDF** format (**P**ortable **D**ocument **F**ormat) which produces far smaller files...  

#### Working with enscript
* **enscript** is a tool that is used to convert a text file to PostScript and other formats  
* it supports RTF and HTTP  

Command                                    | Usage  
-------                                    | -----  
`$ enscript -p psfile.ps textfile.txt`     | Convert a text file to PostScript (saved to psfile.ps)  
`$ enscript -n -p psfile.ps; textfile.txt` | Convert a text file to n columns where n=1-9 (saved in psfile.ps)  
`$ enscript textfile.txt`                  | Print a text file directly to the default printer  
`-r`                                       | rotates to landscape mode  

#### Converting between PostScript and PDF
* various utilities are available:  

Command                         | Usage  
-------                         | -----  
`$ pdf2ps file.pdf`             | Converts file.pdf to file.ps  
`$ ps2pdf file.ps`              | Converts file.ps to file.pdf  
`$ pstopdf input.ps output.pdf` | Converts input.ps to output.pdf  
`$ pdftops input.pdf output.ps` | Converts input.pdf to output.ps  
`$ convert input.ps output.pdf` | Converts input.ps to output.pdf  
`$ convert input.pdf output.ps` | Converts input.pdf to output.ps  

#### Viewing PDF Content
Linux PDF readers:  
* Evince  
* Okular  
* GhostView  
* Xpdf  

#### Manipulating PDFs with pdftk
**pdftk** can be used to merge, split, or rotate PDF files  
* pdftk is the Swiss army knife of PDF tools  

#### Installing pdftk on Different Family Systems
* can be installed on CentOS 6 with `$ sudo yum install pdftk`  

#### Using pdftk
* example usage:  

Command                                          | Usage  
-------                                          | -----  
`$ pdftk 1.pdf 2.pdf cat output 12.pdf`          | Merge the two documents 1.pdf and 2.pdf. The output will be saved to 12.pdf.  
`$ pdftk A=1.pdf cat A1-2 output new.pdf`        | Write only pages 1 and 2 of 1.pdf. The output will be saved to new.pdf.  
`$ pdftk A=1.pdf cat A1-endright output new.pdf` | Rotate all pages of 1.pdf 90 degrees clockwise and save result in new.pdf.  

#### Encrypting PDF Files
* use `$ pdftk public.pdf output private.pdf user_pw PROMPT`  

#### Using Ghostscript
* **gs** can do most of the operations that **pdftk** can as well as many others  
* Combine three PDF files into one: `$ gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite  -sOutputFile=all.pdf file1.pdf file2.pdf file3.pdf`  
* Split pages 10 to 20 out of a PDF file: `$ gs -sDEVICE=pdfwrite -dNOPAUSE -dBATCH -dDOPDFMARKS=false -dFirstPage=10 -dLastPage=20\
-sOutputFile=split.pdf file.pdf`  

#### Using pdftk
(video)  

#### Using Additional Tools
* use **pdfinfo** to extract information  
* use **flpsed** to add data to a PostScript document  
* use **pdfmod** for a GUI for modifying PDF documents  
* example: `$ pdfinfo /usr/share/doc/readme.pdf`  

#### Lab 17.3a (Lab 1): Creating PostScript and PDF from Text Files
```
1. Check to see if the enscript package has been installed on your system, and if not, install it.
2. Using enscript, convert the text file /var/dmesg to PostScript format and name the result /tmp/dmesg.ps. (As an alternative, you can use any large text file on your system.) Make sure you can read the PostScript file (with evince for example) and compare to the original file.
   (Note: on some systems, such as RHEL7/CentOS7, evince may have problems with the PostScript file, but the PDF file you produce from it will be fine for viewing.)
3. Convert the PostScript document to PDF format, using ps2pdf. Make sure you can read the resulting PDF file. Does it look identical to the PostScript version?
4. Is there a way you can go straight to the PDF file without producing a PostScript file on the disk along the way?
5. Using pdfinfo, determine what is the PDF version used to encode the file, the number of pages, the page size, and other metadata about the file. (If you do not have pdfinfo you probably need to install the poppler-utils package.
```
* My solution:  
```
$ sudo yum install enscript
$ enscript -p /tmp/dmesg.ps /var/dmesg
enscript: couldn't open input file "/var/dmesg": No such file or directory
no output generated
$ enscript -p /tmp/dmesg.ps /var/log/dmesg
[ 32 pages * 1 copy ] left in /tmp/dmesg.ps
160 lines were wrapped
$ ps2pdf /var/log/dmesg.ps
Error: /undefinedfilename in (/var/log/dmesg.ps)
Operand stack:

Execution stack:
   %interp_exit   .runexec2   --nostringval--   --nostringval--   --nostringval--   2   %stopped_push   --nostringval--   --nostringval--   --nostringval--   false   1   %stopped_push
Dictionary stack:
   --dict:940/1684(ro)(G)--   --dict:0/20(G)--   --dict:77/200(L)--
Current allocation mode is local
Last OS error: No such file or directory
GPL Ghostscript 9.07: Unrecoverable error, exit code 1
```
* Lab solution:  
```

    Try

    which enscript         

    /usr/bin/enscript

    If you do not get a positive result, install with whichever command is appropriate for your Linux distribution:

    apt-get install enscript
    yum install enscript
    zypper install enscript 

    enscript -p /tmp/dmesg.ps /var/log/dmesg
    evince /tmp/dmesg.ps 

    ps2pdf /tmp/dmesg.ps
    ls -lh /var/log/dmesg /tmp/dmesg.ps /tmp/dmesg.pdf 

    -rw-rw-r-- 1 coop coop 28K Apr 22 13:00 /tmp/dmesg.pdf
    -rw-rw-r-- 1 coop coop 80K Apr 22 12:59 /tmp/dmesg.ps
    -rw-r--r-- 1 root root 53K Apr 22 11:48 /var/log/dmesg
            

    evince /tmp/dmesg.ps /tmp/dmesg.pdf 

    Note the difference in sizes. PostScript files tend to be large, while PDF is a compressed format.
    You may want to scan the man pages for enscript and ps2pdf to figure out how to use standard input or standard output instead of files.

    student:/tmp> enscript -p - /var/log/dmesg  | ps2pdf -  dmesg_direct.pdf         

    [ 15 pages * 1 copy ] left in -
    85 lines were wrapped

    student:/tmp> ls -l dmesg*pdf         

    -rw-rw-r-- 1 coop coop 28177 Apr 22 13:20 dmesg_direct.pdf
    -rw-rw-r-- 1 coop coop 28177 Apr 22 13:00 dmesg.pdf 

    student:/tmp> pdfinfo dmesg.pdf         

    Title:          Enscript Output
    Author:         Theodore Cleaver
    Creator:        GNU Enscript 1.6.6
    Producer:       GPL Ghostscript 9.07
    CreationDate:   Wed Apr 22 13:00:26 2015
    ModDate:        Wed Apr 22 13:00:26 2015
    Tagged:         no
    Form:           none
    Pages:          15
    Encrypted:      no
    Page size:      612 x 792 pts (letter)
    Page rot:       0
    File size:      28177 bytes
```

#### Lab 17.3b (Lab 2): Combining PDFs
```
You can convert two text files (you can create them or use ones that already exist since this is non-destructive) into PDFs, or you can use two pre-exisiting ones. Combine them into one PDF, and view the result.
If pdftk is not installed, you can try to install. However, if you are on a system for which it is not available (such as RHEL7/CentOS7), you will have to use gs.
```
* My solution:  
```
```

* Lab solution:  
```
First we can create two PDFs to play with, using enscript and then ps2pdf:

cd /var/log
enscript -p dmesg.ps /var/log/dmesg
enscript -p boot.ps /var/log/boot.log
ps2dpf dmesg.ps
ps2dpf boot.ps 

Of course you may use two pre-existing PDF files and substitute their names below.

    Method 1: Using pdftk:

    pdftk dmesg.pdf boot.pdf cat output method1.pdf

    Method 2: Using gs:

    gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=method2.pdf dmesg.pdf boot.pdf

Now view them:

ls -l method1.pdf method2.pdf
evince method1.pdf method2.pdf

How do the files compare?
```

### 17.4: Summary
----
You have completed this chapter. Let’s summarize the key concepts covered:
* **CUPS** provides two command-line interfaces: the **System V** and **BSD** interfaces.
* The CUPS interface is available at http://localhost:631
* **lp** and **lpr**  are used to submit a document to **CUPS** directly from the command line.
* **lpoptions** can be used to set printer options and defaults.
* PostScript effectively manages scaling of fonts and vector graphics to provide quality prints.
* **enscript** is used to convert a text file to PostScript and other formats.
* **Portable Document Format (PDF)** is the standard format used to exchange documents while ensuring a certain level of consistency in the way the documents are viewed.
* **pdftk** joins and splits PDFs; pulls single pages from a file; encrypts and decrypts PDF files; adds, updates, and exports a PDF’s metadata; exports bookmarks to a text file; adds or removes attachments to a PDF; fixes a damaged PDF; and fills out PDF forms.
* **pdfinfo** can extract information about PDF documents.
* **flpsed** can add data to a PostScript document.
* **pdfmod** is a simple application with a graphical interface that you can use to modify PDF documents.

PATHS  
17.1  
`/etc/cups`:  
`/etc/cups/cupsd.conf`  
`/etc/cups/printers.conf`  
`/var/spool/cups`  
`/var/log/cups`  
`/etc/cups/ppd/`  
http://localhost:631  

COMMANDS  
17.1  
`$ ls -l /etc/cups/`  
`$ sudo ls -l /var/log/cups`  
`$ sudo yum install cups`  
`$ sudo zypper install cups`  
`$ sudo apt-get install cups`  
`$ lsusb`  
17.2  
`$ lp <filename>`  
`$ lp -d printer <filename>`  
`$ program | lp ; echo string | lp`  
`$ lp -n number <filename>`  
`$ lpoptions -d printer`  
`$ lpoptions help`  
`$ lpq -a`  
`$ lpadmin`  
`$ lpstat -p -d`  
`$ lpstat -a`  
`$ cancel job-id OR lprm job-id`  
`$ lpmove job-id newprinter`  
17.3  
`$ enscript -p psfile.ps textfile.txt`  
`$ enscript -n -p psfile.ps; textfile.txt`  
`$ enscript textfile.txt`  
`$ pdf2ps file.pdf`  
`$ ps2pdf file.ps`  
`$ pstopdf input.ps output.pdf`  
`$ pdftops input.pdf output.ps`  
`$ convert input.ps output.pdf`  
`$ convert input.pdf output.ps`  
`$ pdftk 1.pdf 2.pdf cat output 12.pdf`  
`$ pdftk A=1.pdf cat A1-2 output new.pdf`  
`$ pdftk A=1.pdf cat A1-endright output new.pdf`  
`$ pdftk public.pdf output private.pdf user_pw PROMPT`  
`$ gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite  -sOutputFile=all.pdf file1.pdf file2.pdf file3.pdf`  
`$ gs -sDEVICE=pdfwrite -dNOPAUSE -dBATCH -dDOPDFMARKS=false -dFirstPage=10 -dLastPage=20-sOutputFile=split.pdf file.pdf`  
`$ pdfinfo /usr/share/doc/readme.pdf`  
