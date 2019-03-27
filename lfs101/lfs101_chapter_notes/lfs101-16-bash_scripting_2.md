Chapter 16: Bash Shell Scripting II
-----------------------------------

[16.0: Introduction/ Learning Objectives](#160-introduction-learning-objectives)  
[16.1: String Manipulation](#161-string-manipulation)  
[16.2: The Case Statement](#162-the-case-statement)  
[16.3: Looping Constructs](#163-looping-constructs)  
[16.4: Script Debugging](#164-script-debugging)  
[16.5: Some Additional Useful Techniques](#165-some-additional-useful-techniques)  
[16.6: Summary](#166-summary)  
  
### 16.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should be able to:
* Manipulate strings to perform actions such as comparison and sorting.
* Use Boolean expressions when working with multiple data types, including strings or numbers, as well as files.
* Use **case** statements to handle command line options.
* Use looping constructs to execute one or more lines of code repetitively.
* Debug scripts using set **-x** and set **+x**.
* Create temporary files and directories.
* Create and use random numbers.

### 16.1: String Manipulation
----
#### String Manipulation  
* **string variable** examples: **abcde**, **123**, **abcde 123**, **abcde-123**, **&acbde=%123**  
* **string operators**:  

Operator                 | Meaning  
--------                 | -------  
[[ string1 > string2 ]]  | Compares the sorting order of string1 and string2.  
[[ string1 == string2 ]] | Compares the characters in string1 with the characters in string2.  
myLen1=${#string1}       | Saves the length of string1 in the variable myLen1.  

#### Example of String Manipulation
* see "16.1 - string demos.png"  

#### Parts of a String
* string slicing: to get first **n** characters of a string: **${string:0:n}**  
* to get all characters: **${string#*.}**  

#### Testing for Strings
(video)  

#### String Operations
(video)  
#### Knowledge Check

#### Lab 16.1: String Tests and Operations
```
Write a script which reads two strings as arguments and then:
1. Tests to see if the first string is of zero length, and if the other is of non-zero length, telling the user of both results.
2. Determines the length of each string, and reports on which one is longer or if they are of equal length.
3. Compares the strings to see if they are the same, and reports on the result.
```
* My solution:  
```
#!/bin/bash

echo "Enter first string"
read string1
echo "Enter second string"
read string2

len1=${#string1}
len2=${#string2}

if [[ $len1 > 0 ]] ; then
  if [[ $len2 > 0 ]] ; then
    echo $string1: $len1
    echo $string2: $len2
    if [[ $len1 > $len2 ]] ; then
      echo $string1 is longer than $string2
    elif [[ $len2 > $len1 ]] ; then
      echo $string2 is longer than sstring1
    else
      echo $string1 is the same length as $string2
    fi
    if [[ $string1 == $string2 ]] ; then
      echo $string1 is the same as $string2
    else
      echo $string1 is not the same as $string2
    fi
  fi
fi

```
* Lab solution:  
```
Create a file named teststrings.sh, with the content below.

#!/bin/bash

# check two string arguments were given
[[ $# -lt 2 ]] && \
    echo "Usage: Give two strings as arguments" && exit 1

str1=$1
str2=$2

#------------------------------------
## test command

echo "Is string 1 zero length? Value of 1 means FALSE"
[ -z "$str1" ]
echo $?
# note if $str1 is empty, the test [ -z $str1 ] would fail
#                              but [[ -z $str1 ]] succeeds
#         i.e., with [[ ]] it works even without the quotes

echo "Is string 2 nonzero length? Value of 0 means TRUE;"
[ -n $str2 ]
echo $?

## comparing the lengths of two string

len1=${#str1}
len2=${#str2}
echo length of string1 = $len1, length of string2 = $len2

if [ $len1 -gt $len2 ]
then
    echo "String 1 is longer than string 2"
else
    if [ $len2 -gt $len1 ]
    then
	echo "String 2 is longer than string 1"
    else
	echo "String 1 is the same length as string 2"
    fi
fi

## compare the two strings to see if they are the same

if [[ $str1 == $str2 ]]
then
    echo "String 1 is the same as string 2"
else
    if [[ $str1 != $str2 ]]
    then
	echo "String 1 is not the same as string 2"
    fi
fi

student:/tmp> chmod +x teststrings.sh 
student:/tmp> ./teststrings.sh str1 str2


Is string 1 zero length? Value of 1 means FALSE
1
Is string 2 nonzero length? Value of 0 means TRUE;
0
length of string1 = 4, length of string2 = 4
String 1 is the same length as string 2
String 1 is not the same as string 2


student:/tmp>./teststrings.sh str1 str2long


Is string 1 zero length? Value of 1 means FALSE
1
Is string 2 nonzero length? Value of 0 means TRUE;
0
length of string1 = 4, length of string2 = 8
String 2 is longer than string 1
String 1 is not the same as string 2


student:/tmp>
```

### 16.2: The Case Statement
----
#### The case Statement
* **case** statements are often used to handle command-line options  
* advantages:  
   * it is easier to read and write  
   * it is a good alternative to nested, multi-level if-then-else-fi code blocks  
   * it enables you to compare a variable against several values at once  
   * it reduces the complexity of a program  

#### Structure of the case Statement
* structure:  
```
case expression in
   pattern1) execute commands;;
   pattern2) execute commands;;
   pattern3) execute commands;;
   pattern4) execute commands;;
   * )       execute some default commands or nothing ;;
esac
```

#### Example of Use of the case Construct
* see "16.2 - test case.png"  

#### Lab 16.2: Using the case Statement
```
Write a script that takes as an argument a month in numerical form (i.e., between 1 and 12), and translates this to the month name and displays the result on standard out (the terminal).
If no argument is given, or a bad number is given, the script should report the error and exit.
```
* My solution:  
```
#!/bin/bash

echo "Enter a month in numerical form (1-12), and I'll tell you the name of the month1"
read month

case "$month" in
  "1" | "01" )  echo "January" ;;
  "2" | "02" )  echo "February" ;;
  "3" | "03" )  echo "March" ;;
  "4" | "04" )  echo "April" ;;
  "5" | "05" )  echo "May" ;;
  "6" | "06" )  echo "June" ;;
  "7" | "07" )  echo "July" ;;
  "8" | "08" )  echo "August" ;;
  "9" | "09" )  echo "September" ;;
  "10"       )  echo "October" ;;
  "11"       )  echo "November" ;;
  "12"       )  echo "December" ;;
  *          )  echo "I don't recognize that month!" ;;
esac

```
* Lab solution:  
```
Create a file named testcase.sh, with the content below.

#!/bin/bash

# Accept a number between 1 and 12 as
# an argument to this script, then return the
# the name of the month that corresponds to that number.

# Check to see if the user passed a parameter.
if [ $# -eq 0 ]
then
  echo "Error. Give as an argument a number between 1 and 12."
  exit 1
fi

# set month equal to argument passed for use in the script
month=$1

################################################
# The example of a case statement:

case $month in

  1)  echo "January"   ;;
  2)  echo "February"  ;;
  3)  echo "March"     ;;
  4)  echo "April"     ;;
  5)  echo "May"       ;;
  6)  echo "June"      ;;
  7)  echo "July"      ;;
  8)  echo "August"    ;;
  9)  echo "September" ;;
  10) echo "October"   ;;
  11) echo "November"  ;;
  12) echo "December"  ;;
  *)
     echo "Error. No month matches: $month"
     echo "Please pass a number between 1 and 12."
     exit 2
     ;;
esac
exit 0

Make it executable and run it:

student:/tmp> chmod +x testcase.sh 
student:/tmp> ./testcase.sh 5
May

student:/tmp> ./testcase.sh 12
December

student:/tmp> ./testcase.sh 99
Error. No month matches: 99
Please pass a number between 1 and 12
student:/tmp>
```

### 16.3: Looping Constructs
----
#### Looping Constructs
* three main types:  
   * for  
   * while  
   * until  

#### The 'for' Loop
* syntax:  
```
for variable-name in list
do
  execute on iteration for each item in the list until the list is finished
done
```
* incidentally, the sum of numbers from 1 to n is 'n*(n+1)/2'  

#### The 'for' Statement in Action (along with case)
(video)  

#### The 'while' Loop
* repeats statements as long as control command returns true (while x < y)  
* syntax:  
```
while condition is true
do
  Command for execution
  ----
done
```

#### The while Statement
(video)  

#### The 'until' loop
* repeats statements as long as control command is false (until x > y)  
* syntax:  
```
until condition is false
do
  Commands for execution
  ----
done
```

#### The until Loop (video)
(video)  

### 16.4: Script Debugging
----
#### Introduction to Script Debugging

#### More About Script Debugging
* run a bash script in debug mode either by doing `bash –x ./script_file`,  or bracketing parts of the script with `set -x` and `set +x`  
* debug mode helps identify the error because:  
   * it traces and prefixes each command with the `+` character  
   * it displays each command before executing it  
   * it can debug only selected parts of a script (if desired) with:  
```
    set -x    # turns on debugging
    ...
    set +x    # turns off debugging
```
* example:  
   * in bash script: `if [ "$1" != "" ] ; then set -x ; fi`  
   * execution: `$ script.sh debug`  

#### Redirecting Errors to File and Screen
* file streams:  
File stream | Description | File Descriptor  
----------- | ----------- | ---------------  
stdin       | Standard Input, by default the keyboard/terminal for programs run from the command line | 0  
stdout      | Standard output, by default the screen for programs run from the command line           | 1  
stderr      | Standard error, where output error messages are shown or saved                          | 2  

### 16.5: Some Additional Useful Techniques
----
#### Creating Temporary Files and Directories
* for temporary files created while a script is running and removed afterwards, the best practice is to create random and unpredictable filenames   
* **mktemp** can be used to do this  
* examples, where **XXXXXXXX** is replaced with random characters:  

Command                               | Usage  
-------                               | -----  
TEMP=$(mktemp /tmp/tempfile.XXXXXXXX) | To create a temporary file  
TEMPDIR=$(mktemp -d /tmp/tempdir.XXXXXXXX) | To create a temporary directory  

#### Example of Creating a Temporary File and Directory
If someone were to create a symbolic link from a known temporary file used by root to the `/etc/passwd` file, like this:  
`$ ln -s /etc/passwd /tmp/tempfile`,  
there could be a big problem if a script run by root has a line in like this:  
`$ echo $VAR > /tmp/tempfile`.  
The password file will be overwritten by the temporary file contents.  
Randomizing temporary file names helps:  
`$ TEMP=$(mktemp /tmp/tempfile.XXXXXXXX)`  
`$ echo $VAR > $TEMP`  

#### Discarding Output with /dev/null
* `/dev/null` is a device node; a **pseudofile** also called the **bit bucket** or **black hole**  
* data written to it is discarded; write operations never return a failure condition  
* used to make output disappear:  
   * `$ ls -lR /tmp > /dev/null` # output stream ignored, but errors still appear on console  
   * `$ ls -lR /tmp >& /dev/null` # both **stdout** and **stderr** will be dumped into `/dev/null`  
   
#### Random Numbers and Data
* random numbers and random data useful when performing:  
   * security-related tasks  
   * reinitializing storage devices  
   * erasing and/or obscuring existing data  
   * generating meaningless data to be used for tests  
* can be generated with `$RANDOM` environment variable, as in `$ echo $RANDOM`  
* uses FIPS140 algorithm  


#### How the Kernel Generates Random Numbers
* key terms in this section:  
   * **transducer**  
   * **A-D converter**  
   * **entropy pool**  
* Linux offers these device nodes:  
   * `/dev/random` # used where very high quality randomness is required, but slower; won't work when entropy pool is empty and waits for additional environmental noise  
   * `/dev/urandom` # faster and good enough for most crytographic purposes; reuses internal pool  
   
#### Lab 16.5: Using Random Numbers
```
Write a script which:
1. Takes a word as an argument.
2. Appends a random number to it.
3. Displays the answer.
```
* My solution:  
```
#!/bin/bash

echo "Please enter a word"
read word
new_word = "$word$RANDOM"
echo $new_word
```
* Lab solution:  
```
Create a file named testrandom.sh, with the content below.

#!/bin/bash
##
# check to see if the user supplied in the parameter.

[[ $# -eq 0 ]] && echo "Usage: $0 word" && exit 1

echo "$1-$RANDOM"
exit 0

Make it executable and run it:

student:/tmp> chmod +x testrandom.sh 
student:/tmp> ./testrandom.sh strA

strA-29294

student:/tmp>./testrandom.sh strB

strB-23911

student:/tmp>./testrandom.sh strC

strC-27782
student:/tmp>
```

### 16.6: Summary
----
You have completed this chapter. Let’s summarize the key concepts covered:
* You can manipulate strings to perform actions such as comparison, sorting, and finding length.
* You can use Boolean expressions when working with multiple data types, including strings or numbers, as well as files.
* The output of a Boolean expression is either true or false.
* Operators used in Boolean expressions include the `&&` (AND), `||` (OR), and `!` (NOT) operators.
* We looked at the advantages of using the `case` statement in scenarios where the value of a variable can lead to different execution paths.
* Script debugging methods help troubleshoot and resolve errors.
* The standard and error outputs from a script or shell commands can easily be redirected into the same file or separate files to aid in debugging and saving results
* Linux allows you to create temporary files and directories, which store data for a short duration, both saving space and increasing security.
* Linux provides several different ways of generating random numbers, which are widely used.

PATHS:  
16.5  
`/dev/null`  
`/dev/random`  
`/dev/urandom`  

COMMANDS:  
16.5  
`$ ls -lR /tmp > /dev/null`  
`$ ls -lR /tmp >& /dev/null`  
 