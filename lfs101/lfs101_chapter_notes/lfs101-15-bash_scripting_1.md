Chapter 15: Bash Shell Scripting I
----------------------------------

[15.0: Introduction/ Learning Objectives](#15.0:-Introduction/-Learning-Objectives)  
[15.1: Features and Capabilities](#15.1:-Features-and-Capabilities)  
[15.2: Syntax](#15.2:-Syntax)  
[15.3: Constructs](#15.3:-Constructs)  
[15.4: Summary](#15.4:-Summary)  
  
### 15.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
* Explain the features and capabilities of **bash shell scripting**.
* Know the basic syntax of scripting statements.
* Be familiar with various methods and constructs used.
* Test for properties and existence of files and other objects.
* Use conditional statements, such as **if-then-else** blocks.
* Perform arithmetic operations using scripting language.

### 15.1: Features and Capabilities

#### Introduction to Scripting

#### Introduction to Shell Scripts
* running `$ find . -name "*.c" -ls` is the same as running a script containing:  
```
#!/bin/bash  
find . -name "*.c" -ls  
```
* #!/bin/bash is the full path of the command interpreter  

#### Command Shell Choices
* find available shells in `$ /etc/shells`  
 
#### bash Scripts
* sample script:  
```
$ cat > hello.sh  
  #!/bin/bash  
  echo "Hello Linux Foundation Student"  
```
* execute using:  
   * `$ ./hello.sh  (file needs to be executable)  
   * `$ bash hello.sh` (file does not need to be executable)
  
#### Interactive Example Using bash Scripts
* sample script:  
```
   #!/bin/bash  
   # Interactive reading of a variable  
   echo "ENTER YOUR NAME"  
   read name  
   # Display variable input  
   echo The name given was :$name  
```
* **$** is used to reference value of a shell variable  
* **#** (comments) can be placed anywhere in the line  

#### Return Values
* all shell scripts generate a **return value** upon finishing execution, the value can be set with the `exit` statement  
* used to monitor exit state of another process; take steps depending on success or failure  

#### Viewing Return Values
* success == 0  
* failure == non-zero value  
* value stored in environment variable **$?**  
* example:  
   * `$ cat hello.sh`  
     > #!/bin/bash  
     > echo "Hello Linux Foundation Student"  
   * `$ echo $?`  
     > 0  
   * `$ cat hiya.sh`  
     > cat: hiya.sh: No such file or directory  
   * `$ echo $?`  
     > 1  

### Lab 1: Exit Status Codes
```
Write a script which:
 * Does ls for a non-existent file, and then displays the resulting exit status.
 * Creates a file and does ls for it, and then once again displays the resulting exit status.
```
* My solution:  
```
$ cat return_value.sh 
#!/bin/bash

# run ls on a non-existent file and display exit status
echo "Enter a file name"
read file
ls $file
echo "Exit code:" $? 

# create the non-existent file and run ls again
touch ./$file
ls $file
echo "Exit code:" $?

```
`$ ./return_value.sh`  
   > Enter a file name  
   > hiya.sh  
   > ls: cannot access hiya.sh: No such file or directory  
   > Exit code: 2  
   > hiya.sh  
   > Exit code: 0  

* Lab solution:  
```
Create a file named testls.sh, with the content below.

#!/bin/bash
#
# check for non-existent file, exit status will be 2
#
ls SoMeFiLe.ext  
echo "status: $?"

# create file, and do again, exit status will be 0
touch SoMeFiLe.ext
ls SoMeFiLe.ext  
echo "status: $?"

# remove the file to clean up
rm SoMeFiLe.ext

Make it executable and run it:

student:/tmp> chmod +x testls.sh 
student:/tmp> ./testls.sh

ls: cannot access SoMeFiLe.ext: No such file or directory
status: 2
SoMeFiLe.ext
status: 0
```

### 15.2: Syntax

#### Basic Syntax and Special Characters
* special character usages within bash scripts:  
  
Character | Description
--------- | -----------
\#        | Used to add a comment, except when used as \#, or as #! when starting a script  
\         | Concatenation operator; used at the end of a line to indicate continuation on to the next line  
;         | Used to interpret what follows as a new command to be executed next  
$         | Indicates what follows is an environment variable  
>         | Redirect output  
>>        | Append output  
<         | Redirect input  
|         | Used to pipe the result into the next command  

* other special character combinations include: **(..), {..}, \[..\], &&, ||, ', ", $((...))**  

#### Splitting Long Commands Over Multiple Lines
* concatenation example:  
`$ scp abc@server1.linux.com:/var/ftp/pub/userdata/custdata/read \`  
` abc@server3.linux.co.in:/opt/oradba/master/abc/`  

#### Putting Multiple Commands on a Single Line
* "chaining commands"  
* each ensuing command is executed whether or not the preceding ones succeed  
* examples:  
   * `$ make ; make install ; make clean`  
   * `$ make && make install && make clean` # using **&&** (and) operator, if any command fails, the next will not be executed  
   * `$ cat file1 || cat file2 || cat file3` # using **||** (or) operator, proceed until a command succeeds, then stop  
   
#### Output Redirection
* **>** used to write output to a file (or somewhere else)  
* **>>** used to append output to a file  

#### Input Redirection
* **<** used to read input from a file  
* examples (three equivalent commands):  
   * `$ wc < /etc/passwd` # input redirection  
     > 49  105 2678 /etc/passwd 
   * `$ wc /etc/passwd` # direct action on file  
   * `$ cat /etc/passwd | wc` # piping into a second process  

#### Built-In Shell Commands
* shell scripts execute the following types of commands:  
   * compiled applications (binary executable files, often in /usr/bin) like **rm, ls, df, vi, gzip**  
   * built-in bash commands (can only be used to display the output within a terminal shell or shell script)  
      * might be named the same as executables on the system, like **echo**, which can lead to problems
      * built-ins include **cd, pwd, echo, read, logout, printf, let, ulimit**  
      * see complete list in bash man page  
   * shell scripts or scripts from other interpreted languages, such as perl and Python  

#### Script Parameters
* within a script, the parameter or an argument is represented with a **$** and a number or special character  

Parameter    | Meaning
---------    | -------
$0           | Script name
$1           | First parameter
$2, $3, etc. | Second, third parameter, etc.
$*           | All parameters
$#           | Number of arguments

#### Using Script Parameters
* example script:  
```
#!/bin/bash
echo "The name of this program is: $0"
echo "The first argument passed from the command line is: $1"
echo "The second argument passed from the command line is: $2"
echo "The third argument passed from the command line is: $3"
echo "All of the arguments passed from the command line are: $*"
echo
echo "All done with $0"
```

#### Script Parameters
(video)  

#### Command Substitution
* can be done two ways:  
   * enclosing inner command with backticks (**`**)  
   * enclosing inner command with **$()**  
* innermost command will be executed in a newly launched shell environment, and the stdout will be inserted where the command substitution was done  
* **$()** allows command nesting; favored over the older backtick method  
* example:  
   * `$ ls /lib/modules/$(uname -r)/`  
   
#### Environment Variables
* list environment variables:  
   * `$ env`  
   * `$ printenv`  
   * `$ set`  

#### Exporting Environment Variables
* to make variables within a script available to child processes (sub-shells):  
   * `$ export VAR=value`  
   * `$ VAR=value ; export VAR`  
* while child processes are allowed to modify the value of exported variables, the parent will not see any change  
* exported variables are not shared, they are only copied and inherited  
* `$ export` with no arguments will give a list of all currently exported environment variables  

#### Functions
* also called **subroutines**  
* syntax:  
```
function_name () {
    command...
}
```
* example:  
```
display () {
    echo "This is a sample function"
}
```

### Lab 2: Working with Files and Directories in a Script
```
Write a script which:
1. Prompts the user for a directory name and then creates it with mkdir.
2. Changes to the new directory and prints out where it is using pwd.
3. Using touch, creates several empty files and runs ls on them to verify they are empty.
4. Puts some content in them using echo and redirection.
5. Displays their content using cat.
6. Says goodbye to the user and cleans up after itself.
```
* My solution:  
```
#!/bin/bash

echo "Enter a directory name: "
read dir
mkdir $dir
cd $dir; pwd
touch file1; touch file2; ls file?
echo "Hello" > file1; echo "World" > file2
cat *
echo "Goodbye!"
cd ..; rm -r $dir

```
* Lab solution:  
```
Create a file named testfile.sh, with the content below.

#!/bin/bash

# Prompts the user for a directory name and then creates it  with mkdir.

echo "Give a directory name to create:" 
read NEW_DIR

# Save original directory so we can return to it (could also just use pushd, popd)

ORIG_DIR=$(pwd)

# check to make sure it doesn't already exist!

[[ -d $NEW_DIR ]] && echo $NEW_DIR already exists, aborting && exit 
mkdir $NEW_DIR

# Changes to the new directory and prints out where it is using pwd.  

cd $NEW_DIR
pwd

# Using touch, creates several empty files and runs ls on them to verify they are empty.

for n in 1 2 3 4
do 
    touch file$n 
done

ls file?

# (Could have just done touch file1 file2 file3 file4, just want to show do loop!)

# Puts some content in them using echo and redirection.

for names in file?  
do 
    echo This file is named $names > $names
done

# Displays their content using cat

cat file?

# Says goodbye to the user and cleans up after itself 

cd $ORIG_DIR 
rm -rf $NEW_DIR 
echo "Goodbye My Friend!"

Make it executable and run it:

$ chmod +x testfile.sh
./testfile.sh

Give a directory name to create:

/tmp/SOME_DIR

/tmp/SOME_DIR
file1  file2  file3  file4
This file is named file1
This file is named file2
This file is named file3
This file is named file4
Goodbye My Friend
```

### Lab 3: Passing Arguments
```
Write a script that takes exactly one argument, and prints it back out to standard output. Make sure the script generates a usage message if it is run without giving an argument.
```
* My solution:  
```
#!/bin/bash

if [ $# -eq 0 ]
  then
    echo "One argument is required."
  else
    echo $1
fi

```
* Lab solution:  
```
Create a file named testarg.sh, with the content below.

#!/bin/bash
#
# check for an argument, print a usage message if not supplied.
#
if [ $# -eq 0 ] ; then
      echo "Usage: $0 argument"
      exit 1
fi
echo $1
exit 0

Make it executable and run it:

student:/tmp> chmod +x testarg.sh 
student:/tmp> ./testarg.sh Hello

Hello

student:/tmp>./testarg.sh 

Usage: ./testarg.sh argument
student:/tmp>
```

### Lab 4: Environment Variables
```
Write a script which:
1. Asks the user for a number, which should be "1" or "2". Any other input should lead to an error report.
2. Sets an environmental variable to be "Yes" if it is "1", and "No" if it is "2".
3. Exports the environmental variable and displays it.
```
* My solution:  
```
#!/bin/bash

echo "Enter '1' for 'Yes' or '2' for 'No'."
read val
if [ $val -eq 1 ]
  then
    export CONDITION="Yes"
elif [ $val -eq 2 ]
  then
    export CONDITION="No"
else
  echo "Please make sure your argument is the number 1 or 2"
fi
echo "Current value of CONDITION: "
echo $CONDITION

```
* Lab solution:  
```
Create a file named testenv.sh, with the content below.

#!/bin/bash

echo "Enter 1 or 2, to set the environmental variable EVAR to Yes or No"
read ans

# Set up a return code
RC=0

if [ $ans -eq 1 ]  
then 
    export EVAR="Yes"
else
    if [ $ans -eq 2 ]
    then
	export EVAR="No"
    else
# can only reach here with a bad answer
	export EVAR="Unknown"
	RC=1
    fi    
fi
echo "The value of EVAR is: $EVAR"
exit $RC

Make it executable and run it:

student:/tmp> chmod +x testenv.sh 
student:/tmp> ./testenv.sh

Enter 1 or 2, to set the environmental variable EVAR to Yes or No
1
The value of EVAR is: Yes

student:/tmp> ./testenv.sh 

Enter 1 or 2, to set the environmental variable EVAR to Yes or No
2
The value of EVAR is: No

student:/tmp> ./testenv.sh 

Enter 1 or 2, to set the environmental variable EVAR to Yes or No
3
The value of EVAR is: Unknown 
```

### Lab 5: Working with Functions
```
Write a script which:
1. Asks the user for a number (1 , 2 or 3).
2. Calls a function with that number in its name. The function should display a message with its name included.
```
* My solution:  
```
#!/bin/bash

function1 () {
  echo "This is function1"
}

function2 () {
  echo "This is function2"
}

function3 () {
  echo "This is function3"
}

echo "Enter a number for the function you want to use (1, 2, or 3): "
read num
if [ $num -eq 1 ]
  then function1
elif [ $num -eq 2 ]
  then function2
elif [ $num -eq 3 ]
  then function3
else
  echo "function"$num" does not exist"
fi

```
* Lab solution:  
```
Create a file named testfun.sh, with the content below.

#!/bin/bash

# Functions (must be defined before use)
func1() {
echo " This message is from function 1"
}
func2() {
echo " This message is from function 2" 
}
func3() { 
echo " This message is from function 3" 
}

# Beginning of the main script

# prompt the user to get their choice
echo "Enter a number from 1 to 3"
read n

# Call the chosen function
func$n

Make it executable and run it:

student:/tmp> chmod +x testfun.sh 
student:/tmp> ./testfun.sh
Enter a number from 1 to 3
2
This message is from function 2

$ ./testfun.sh
Enter a number from 1 to 3
7
./testfun.sh: line 21: func7: command not found
```

### 15.3: Constructs

#### The if Statement
#### The if Statement (video)
#### Nested if Statements
#### The elif Statement
#### Using the if Statement
#### Testing for Files
#### Boolean Expressions
#### Tests in Boolean Expressions
#### Example of Testing of Strings
#### Numerical Tests
#### Example of Testing for Numbers
#### Testing for Numbers (video)
#### Arithmetic Expressions
#### Knowledge Check
#### Knowledge Check
#### Knowledge Check
#### Knowledge Check
#### Knowledge Check
### Lab 5: Arithmetic and Functions

### 15.4: Summary

PATHS:  
`/etc/shells`  


COMMANDS:  
`$ find . -name "*.c" -ls`  
`$ echo $?`  
`$ make ; make install ; make clean`  
`$ make && make install && make clean`  
`$ cat file1 || cat file2 || cat file3`  
`$ wc < /etc/passwd`  
`$ wc /etc/passwd`  
`$ cat /etc/passwd | wc`  
`$ ls /lib/modules/$(uname -r)/`  
`$ env`  
`$ printenv`  
`$ set`  
`$ export VAR=value`  
`$ VAR=value ; export VAR`  
`$ export`  
