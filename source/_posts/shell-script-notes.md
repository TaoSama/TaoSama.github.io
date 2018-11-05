---
title: Notes, Shell Script
categories:
  - Doing
  - Shell
  - 
tags:
  - 
  - 
date: 2017-07-31 11:20:00
toc: true

---
~~Last Modified: 2017-08-11 11:18:00~~

### Acknowledge
* What is shell script?
  A shell script is a file containing a series of commands.

### The first shell script
`hello_world.sh`
```shell
#!/bin/bash
# The line above is called a shebang.
# Every shell script should include this as its first line.

# This is our first script.
echo 'Hello World!'
```

* make it executable
```shell
ls -l hello_world
-rw-r--r-- 1  me    me      63  2009-03-07 10:10 hello_world
chmod 755 hello_world
ls -l hello_world
-rwxr-xr-x 1  me    me      63  2009-03-07 10:10 hello_world

./hello_word
Hello World!
```
<!--- more --->

### Build a program
```shell
#!/bin/bash
# Program to output a system information page
TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date +"%x %r %Z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"
echo "<HTML>
        <HEAD>
                <TITLE>$TITLE</TITLE>
        </HEAD>
        <BODY>
                <H1>$TITLE</H1>
                <P>$TIME_STAMP</P>
        </BODY>
</HTML>"
```

#### here documents
```shell
command << token
text
token
```
* By default, **single and double quotes** within here documents **lose their special meaning** to the shell.
* If we change the redirection operator from **“<<” to “<<-“**, the shell will **ignore leading tab characters** in the here document. This allows a here document to be indented, which can improve readability.
```shell
#!/bin/bash
# Program to output a system information page
TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date +"%x %r %Z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"
cat << _EOF_
<HTML>
         <HEAD>
                <TITLE>$TITLE</TITLE>
         </HEAD>
         <BODY>
                <H1>$TITLE</H1>
                <P>$TIME_STAMP</P>
         </BODY>
</HTML>
_EOF_
```

### Top-down design
#### shell function
```shell
function name {
    commands
    return
}
and
name () {
    commands
    return
}
```

#### local variable
```shell
#!/bin/bash
i=0
function hello {
    local i=1
    echo 'local i = '$i
    echo 'Hello World'
}

hello
echo 'global i = '$i
```

#### keep program runnable
```shell
#!/bin/bash
# Program to output a system information page
TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date +"%x %r %Z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"
report_uptime () {
  echo "Function report_uptime executed."
  return
}
report_disk_space () {
  echo "Function report_disk_space executed."
  return
}
report_home_space () {
  echo "Function report_home_space executed."
  return
}
cat << _EOF_
<HTML>
    <HEAD>
        <TITLE>$TITLE</TITLE>
    </HEAD>
    <BODY>
        <H1>$TITLE</H1>
        <P>$TIME_STAMP</P>
        $(report_uptime)
        $(report_disk_space)
        $(report_home_space)
    </BODY>
</HTML>
_EOF_

############################################
./sys_info_page
<HTML>
<HEAD>
<TITLE>System Information Report For linuxbox</TITLE>
</HEAD>
<BODY>
<H1>System Information Report For linuxbox</H1>
<P>Generated 03/20/2009 05:17:26 AM EDT, by me</P>
Function report_uptime executed.
Function report_disk_space executed.
Function report_home_space executed.
</BODY>
</HTML>
```

###  control: if branching

#### if statement
```shell
if commands; then
     commands
[elif commands; then
     commands...]
[else
     commands]
fi
```

#### exit status
```shell
ls -d /usr/bin
/usr/bin
echo $?
0
ls -d /bin/usr
ls: cannot access /bin/usr: No such file or directory
echo $?
2
```

#### test expression
The test command returns an **exit status** of **zero** when the **expression is true** and a status of **one** when the **expression is false**.
```shell
test expression
# the more popular one:
[ expression ]
```

#### test file expression
```shell
test_file () {
    # test-file: Evaluate the status of a file
    FILE=~/.bashrc
    if [ -e "$FILE" ]; then
        if [ -f "$FILE" ]; then
            echo "$FILE is a regular file."
        fi
        if [ -d "$FILE" ]; then
            echo "$FILE is a directory."
        fi
        if [ -r "$FILE" ]; then
            echo "$FILE is readable."
        fi
        if [ -w "$FILE" ]; then
            echo "$FILE is writable."
        fi
        if [ -x "$FILE" ]; then
            echo "$FILE is executable/searchable."
        fi
    else
        echo "$FILE does not exist"
        return 1
    fi
}
```

#### test string expression
|Expression|	Is Ture If...|
|:-:|:-|
|string|	string is not null.|
|-n string|	The length of string is greater than zero.|
|-z string|	The length of string is zero.|
|string1 = string2 or string1 == string2|string1 and string2 are equal. Single or double equal signs may be used, but the use of double equal signs is greatly preferred.|
|string1 != string2|	string1 and string2 are not equal.|
|string1 > string2|	sting1 sorts after string2.|
|string1 < string2|	string1 sorts before string2.|

#### test integer expression
|Expression|	Is Ture If...|
|:-:|:-|
|integer1 -eq integer2|	integer1 is equal to integer2.|
|integer1 -ne integer2|	integer1 is not equal to integer2.|
|integer1 -le integer2|	integer1 is less than or equal to integer2.|
|integer1 -lt integer2|	integer1 is less than integer2.|
|integer1 -ge integer2|	integer1 is greater than or equal to integer2.|
|integer1 -gt integer2|	integer1 is greater than integer2.|

#### compound command, enhanced test expression

`[[ expression ]]`: it is similar to test and it supports all of its expressions.

```shell
# but add a new string expression
# which returns true if string is matched by the extended regular expression regex
string =~ regex
```

```shell
#!/bin/bash
# test-integer2: evaluate the value of an integer.
INT=-5
if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if [ $INT -eq 0 ]; then
        echo "INT is zero."
    else
        if [ $INT -lt 0 ]; then
            echo "INT is negative."
        else
            echo "INT is positive."
        fi
        if [ $((INT % 2)) -eq 0 ]; then
            echo "INT is even."
        else
            echo "INT is odd."
        fi
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

Another added feature of `[[ ]]` is that the == operator supports pattern matching the same way pathname expansion does. For example:
```shell
FILE=foo.bar
if [[ $FILE == foo.* ]]; then
> echo "$FILE matches pattern 'foo.*'"
> fi
foo.bar matches pattern 'foo.*'
```


`(( arithmetic expression ))`: it is used to perform arithmetic truth tests.
```shell
#!/bin/bash
# test-integer2a: evaluate the value of an integer.
INT=-5
if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if ((INT == 0)); then
        echo "INT is zero."
    else
        if ((INT < 0)); then
            echo "INT is negative."
        else
            echo "INT is positive."
        fi
        if (( ((INT % 2)) == 0)); then
            echo "INT is even."
        else
            echo "INT is odd."
        fi
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

#### combine expressions
Logical Operators

|Operation|	test|	[[ ]] and (( ))|
|:-:|:-:|:-:|
|AND	|-a| <code>&&</code> |
|OR|	-o|	<code>&#124;&#124;</code> |
|NOT|	!|	!|

```shell
#!/bin/bash
# test-integer3: determine if an integer is within a
# specified range of values.
MIN_VAL=1
MAX_VAL=100
INT=50
if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if [[ INT -ge MIN_VAL && INT -le MAX_VAL ]]; then
        echo "$INT is within $MIN_VAL to $MAX_VAL."
    else
        echo "$INT is out of range."
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

We also include parentheses around the expression, for grouping
```shell
if [ ! \( $INT -ge $MIN_VAL -a $INT -le $MAX_VAL \) ]; then
    echo "$INT is outside $MIN_VAL to $MAX_VAL."
else
    echo "$INT is in range."
fi
```

#### two control operators, can perform branching
`command1 && command2`
`command1 || command2`

```shell
mkdir temp && cd temp
[ -d temp ] || mkdir temp
[ -d temp ] || exit 1
```

#### detect permission with if
```shell
report_home_space () {
    if [[ $(id -u) -eq 0 ]]; then
        cat <<- _EOF_
        <H2>Home Space Utilization (All Users)</H2>
        <PRE>$(du -sh /home/*)</PRE>
_EOF_
    else
        cat <<- _EOF_
        <H2>Home Space Utilization ($USER)</H2>
        <PRE>$(du -sh $HOME)</PRE>
_EOF_
    fi
    return
}
```

### Keyboard input
```shell
read [-options] [variable...]
```
* If read receives **fewer** than the expected number, the **extra variables are empty**.
* If read receives **more** than the expected number, the **final variable** will contain **all of the extra input**. 
* If no variables are listed after the read command, a shell variable, `REPLY`, will be assigned all the input.

```shell
#!/bin/bash
# read-secret: input a secret pass phrase
if read -t 10 -sp "Enter secret pass phrase > " secret_pass; then
    echo "\nSecret pass phrase = '$secret_pass'"
else
    echo "\nInput timed out" >&2
    exit 1
fi
```

#### IFS (Internal Field Separator)
The shell allows one or more variable assignments to take place immediately before a command.
These assignments alter the environment for the command that follows. 
**The effect of the assignment is temporary**; only changing the environment for the duration of the command. 
In our case, the value of IFS is changed to a colon character. 
```shell
#!/bin/bash
# read-ifs: read fields from a file
FILE=/etc/passwd
read -p "Enter a user name > " user_name
file_info=$(grep "^$user_name:" $FILE)
if [ -n "$file_info" ]; then
    IFS=":" read user pw uid gid name home shell <<< "$file_info"
    echo "User = '$user'"
    echo "UID = '$uid'"
    echo "GID = '$gid'"
    echo "Full Name = '$name'"
    echo "Home Dir. = '$home'"
    echo "Shell = '$shell'"
else
    echo "No such user '$user_name'" >&2
    exit 1
fi
```

The `<<<` operator indicates **a here string**. 
A here string is like a here document, only shorter, consisting of a single string. 
We might wonder why this **rather oblique method** was chosen rather than:
```shell
echo "$file_info" | IFS=":" read user pw uid gid name home shell
```

**You Can’t Pipe read**
The explanation has to do with the way the **shell handles pipelines**. 
In bash (and other shells such as sh), **pipelines create subshells** (subshells is the subprocesses).
Subshells in Unix-like systems create copies of the environment for the processes to use while they execute.
When the command exits, the subshell and its environment are destroyed.
This means that a subshell can never alter the environment of its parent process.
**Then the effect of the assignment is lost**.

#### validating input
```shell

    echo "Invalid input '$REPLY'" >&2
    exit 1
}
read -p "Enter a single item > "
# input is empty (invalid)
[[ -z $REPLY ]] && invalid_input
# input is multiple items (invalid)
(( $(echo $REPLY | wc -w) > 1 )) && invalid_input
# is input a valid filename?
if [[ $REPLY =~ ^[-[:alnum:]\._]+$ ]]; then
    echo "'$REPLY' is a valid filename."
    if [[ -e $REPLY ]]; then
        echo "And file '$REPLY' exists."
    else
        echo "However, file '$REPLY' does not exist."
    fi
    # is input a floating point number?
    if [[ $REPLY =~ ^-?[[:digit:]]*\.[[:digit:]]+$ ]]; then
        echo "'$REPLY' is a floating point number."
    else
        echo "'$REPLY' is not a floating point number."
    fi
    # is input an integer?
    if [[ $REPLY =~ ^-?[[:digit:]]+$ ]]; then
        echo "'$REPLY' is an integer."
    else
        echo "'$REPLY' is not an integer."
    fi
else
    echo "The string '$REPLY' is not a valid filename."
fi
```

#### menu
```shell
#!/bin/bash
# read-menu: a menu driven system information program
clear
echo "
Please Select:

    1. Display System Information
    2. Display Disk Space
    3. Display Home Space Utilization
    0. Quit
"
read -p "Enter selection [0-3] > "

if [[ $REPLY =~ ^[0-3]$ ]]; then
    if [[ $REPLY == 0 ]]; then
        echo "Program terminated."
        exit
    fi
    if [[ $REPLY == 1 ]]; then
        echo "Hostname: $HOSTNAME"
        uptime
        exit
    fi
    if [[ $REPLY == 2 ]]; then
        df -h
        exit
    fi
    if [[ $REPLY == 3 ]]; then
        if [[ $(id -u) -eq 0 ]]; then
            echo "Home Space Utilization (All Users)"
            du -sh /home/*
        else
            echo "Home Space Utilization ($USER)"
            du -sh $HOME
        fi
        exit
    fi
else
    echo "Invalid entry." >&2
    exit 1
fi
```

### Flow control: while/until loop
#### while
```shell
while commands; do commands; done
```

`break` and `continue`
```shell
#!/bin/bash
# while-menu2: a menu driven system information program
DELAY=3 # Number of seconds to display results
while true; do
    clear
    cat <<- _EOF_
        Please Select:
        1. Display System Information
        2. Display Disk Space
        3. Display Home Space Utilization
        0. Quit
    _EOF_
    read -p "Enter selection [0-3] > "
    if [[ $REPLY =~ ^[0-3]$ ]]; then
        if [[ $REPLY == 1 ]]; then
            echo "Hostname: $HOSTNAME"
            uptime
            sleep $DELAY
            continue
        fi
        if [[ $REPLY == 2 ]]; then
            df -h
            sleep $DELAY
            continue
        fi
        if [[ $REPLY == 3 ]]; then
            if [[ $(id -u) -eq 0 ]]; then
                echo "Home Space Utilization (All Users)"
                du -sh /home/*
            else
                echo "Home Space Utilization ($USER)"
                du -sh $HOME
            fi
            sleep $DELAY
            continue
        fi
        if [[ $REPLY == 0 ]]; then
            break
        fi
    else
        echo "Invalid entry."
        sleep $DELAY
    fi
done
echo "Program terminated."
```

#### until
```shell
#!/bin/bash
# until-count: display a series of numbers
count=1
until [ $count -gt 5 ]; do
    echo $count
    count=$((count + 1))
done
echo "Finished."
```

#### process files with while or until loop
* redirection
```shell
#!/bin/bash
# while-read: read lines from a file
while read distro version release; do
    printf "Distro: %s\tVersion: %s\tReleased: %s\n" \
        $distro \
        $version \
        $release
done < distros.txt
```
* pipe
```shell
#!/bin/bash
# while-read2: read lines from a file
sort -k 1,1 -k 2n distros.txt | while read distro version release; do
    printf "Distro: %s\tVersion: %s\tReleased: %s\n" \
        $distro \
        $version \
        $release
done
```


### Stay out of trouble
#### syntactic errors
```shell
if [ $number = 1 ]; then echo "Number is equal to 1."

# add double quotes to prevent unexpected expansion
if [ "$number = 1]; the echo "Number is equal to 1."
```
#### logical errors
* incorrect conditional expressions. 
* “Off by one” errors. 
* Unanticipated situations.

#### testing
```shell
# use 'echo' to show the expanded parameters
echo rm *  # TESTING
```
#### debugging
* isolate the area related to problem
  **commenting out** the code sections the code
* tracing
  * echo more messages
  * a method of tracing of bash
```shell
#!/bin/bash -x
# trouble: script to demonstrate common errors

# to activate tracing for the entire script by adding the -x option to the first line
number=1
if [ $number = 1 ]; then
    echo "Number is equal to 1."
else
    echo "Number is not equal to 1."
fi
##############################################
export PS4='$LINENO + '
trouble
5 + number=1
7 + '[' 1 = 1 ']'
8 + echo 'Number is equal to 1.'
Number is equal to 1.
```

```shell
#!/bin/bash
# trouble: script to demonstrate common errors

# to perform a trace on a selected portion of a script
# we can use the set command with the -x option:
number=1
set -x # Turn on tracing
if [ $number = 1 ]; then
    echo "Number is equal to 1."
else
    echo "Number is not equal to 1."
fi
set +x # Turn off tracing
```


### Flow control: case branching
```shell
case word in
    [pattern [| pattern]...) commands ;;]...
esac
```

The patterns used by case are the same as those used by pathname expansion. Here are some valid patterns:
`a)`:	Matches if word equals "a".
`[[:alpha:]])`:	Matches if word is a single alphabetic character.
`???)`:	Matches if word is exactly three characters long.
`*.txt)`:	Matches if word ends with the characters “.txt”.
`*)`:	Matches any value of word. It is good practice to include this as the last pattern in a case command; that is, to catch any possible invalid values.
```shell
#!/bin/bash
# case-menu: a menu driven system information program
clear
echo "
Please Select:
1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
"
read -p "Enter selection [0-3] > "
case $REPLY in
    0)  echo "Program terminated."
        exit
        ;;
    1)  echo "Hostname: $HOSTNAME"
        uptime
        ;;
    2)  df -h
        ;;
    3)  if [[ $(id -u) -eq 0 ]]; then
            echo "Home Space Utilization (All Users)"
            du -sh /home/*
        else
            echo "Home Space Utilization ($USER)"
            du -sh $HOME
        fi
        ;;
    *)  echo "Invalid entry" >&2
        exit 1
        ;;
esac
```

#### match more the one test
In bash **prior to version 4.0** there was **no way** for case to match more than one test. 
Modern versions of bash, add the `;;&` notation to terminate each action, we can do this:
```shell
#!/bin/bash
# case4-2: test a character
read -n 1 -p "Type a character > "
echo
case $REPLY in
    [[:upper:]])    echo "'$REPLY' is upper case." ;;&
    [[:lower:]])    echo "'$REPLY' is lower case." ;;&
    [[:alpha:]])    echo "'$REPLY' is alphabetic." ;;&
    [[:digit:]])    echo "'$REPLY' is a digit." ;;&
    [[:graph:]])    echo "'$REPLY' is a visible character." ;;&
    [[:punct:]])    echo "'$REPLY' is a punctuation symbol." ;;&
    [[:space:]])    echo "'$REPLY' is a whitespace character." ;;&
    [[:xdigit:]])   echo "'$REPLY' is a hexadecimal digit." ;;&
esac
#####################################################################
case4-2
Type a character > a
'a' is lower case.
'a' is alphabetic.
'a' is a visible character.
'a' is a hexadecimal digit.
```

### Positional parameters
#### access to the contents of the command line
```shell
#!/bin/bash
# posit-param: script to view command line parameters
echo "
Number of arguments: $#
\$0 = $0
\$1 = $1
\$2 = $2
"
#####################################################
posit-param
Number of arguments: 0
$0 = /home/me/bin/posit-param
$1 =
$2 =
#####################################################
Number of arguments: 2
posit-param a b
$0 = /home/me/bin/posit-param
$1 = a
$2 = b
```

#### shift, access to a large number of arguments
Each time shift is executed, the value of `$2` is moved to `$1`, the value of `$3` is moved to `$2` and so on.
The value of `$#` is also **reduced by one**.
In addition to `$0`, which **never changes**.
```shell
#!/bin/bash
# posit-param2: script to display all arguments
count=1
while [[ $# -gt 0 ]]; do
    echo "Argument $count = $1"
    count=$((count + 1))
    shift
done
#####################################################
posit-param2 a b c d
Argument 1 = a
Argument 2 = b
Argument 3 = c
Argument 4 = d
```
#### group positional parameters
`“$@”` is by far the most useful for most situations, because it preserves the integrity of each positional parameter.
```shell
#!/bin/bash
# posit-params3 : script to demonstrate $* and $@
print_params () {
    echo "\$1 = $1"
    echo "\$2 = $2"
    echo "\$3 = $3"
    echo "\$4 = $4"
}
pass_params () {
    echo -e "\n" '$* :';      print_params   $*
    echo -e "\n" '"$*" :';    print_params   "$*"
    echo -e "\n" '$@ :';      print_params   $@
    echo -e "\n" '"$@" :';    print_params   "$@"
}
pass_params "word" "words with spaces"
#####################################################
posit-param3
 $* :
$1 = word
$2 = words
$3 = with
$4 = spaces
 "$*" :
$1 = word words with spaces
$2 =
$3 =
$4 =
 $@ :
$1 = word
$2 = words
$3 = with
$4 = spaces
 "$@" :
$1 = word
$2 = words with spaces
$3 =
$4 =
```

```shell
# with our arguments
both $* and $@ produce a four word result:
     word words with spaces
"$*" produces a one word result:
    "word words with spaces"
"$@" produces a two word result:
    "word" "words with spaces"
```

#### a complicated application
* Output file `-f or --file`
* Interactive mode `-i or --interactive`
* Help `-h or --help`
```shell
#!/bin/bash
# sys_info_page: program to output a system information page
PROGNAME=$(basename $0)
TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date +"%x %r %Z")
TIMESTAMP="Generated $CURRENT_TIME, by $USER"
report_uptime () {
    cat <<- _EOF_
        <H2>System Uptime</H2>
        <PRE>$(uptime)</PRE>
    _EOF_
    return
}
report_disk_space () {
    cat <<- _EOF_
        <H2>Disk Space Utilization</H2>
        <PRE>$(df -h)</PRE>
    _EOF_
    return
}
report_home_space () {
    if [[ $(id -u) -eq 0 ]]; then
        cat <<- _EOF_
            <H2>Home Space Utilization (All Users)</H2>
            <PRE>$(du -sh /home/*)</PRE>
        _EOF_
    else
        cat <<- _EOF_
            <H2>Home Space Utilization ($USER)</H2>
            <PRE>$(du -sh $HOME)</PRE>
        _EOF_
    fi
    return
}
usage () {
    echo "$PROGNAME: usage: $PROGNAME [-f file | -i]"
    return
}
write_html_page () {
    cat <<- _EOF_
        <HTML>
            <HEAD>
                <TITLE>$TITLE</TITLE>
            </HEAD>
            <BODY>
                <H1>$TITLE</H1>
                <P>$TIMESTAMP</P>
                $(report_uptime)
                $(report_disk_space)
                $(report_home_space)
            </BODY>
        </HTML>
    _EOF_
    return
}
# process command line options
interactive=
filename=
while [[ -n $1 ]]; do
    case $1 in
        -f | --file)          shift
                              filename=$1
                              ;;
        -i | --interactive)   interactive=1
                              ;;
        -h | --help)          usage
                              exit
                              ;;
        *)                    usage >&2
                              exit 1
                              ;;
    esac
    shift
done
# interactive mode
if [[ -n $interactive ]]; then
    while true; do
        read -p "Enter name of output file: " filename
        if [[ -e $filename ]]; then
            read -p "'$filename' exists. Overwrite? [y/n/q] > "
            case $REPLY in
                Y|y)    break
                        ;;
                Q|q)    echo "Program terminated."
                        exit
                        ;;
                *)      continue
                        ;;
            esac
        fi
    done
fi
# output html page
if [[ -n $filename ]]; then
    if touch $filename && [[ -f $filename ]]; then
        write_html_page > $filename
    else
        echo "$PROGNAME: Cannot write file '$filename'" >&2
        exit 1
    fi
else
    write_html_page
fi
```

###  control: for loop
#### the original for command’s syntax
```shell
for variable [in words]; do
    commands
done
```


```shell
#!/bin/bash
# longest-word : find longest string in a file
while [[ -n $1 ]]; do
    if [[ -r $1 ]]; then
        max_word=
        max_len=0
        for i in $(strings $1); do
            len=$(echo $i | wc -c)
            if (( len > max_len )); then
                max_len=$len
                max_word=$i
            fi
        done
        echo "$1: '$max_word' ($max_len characters)"
    fi
    shift
done
```

If the optional in words portion of the for command is omitted, for defaults to processing the **positional parameters**.
```shell
# test.sh 
#!/bin/bash
for i; do
    echo $i
done

####################
test 1 2 3
1
2
3
```

#### C style
```shell
for (( expression1; expression2; expression3 )); do
    commands
done
```

```shell
#!/bin/bash
# simple_counter : demo of C style for command
for (( i=0; i<5; i=i+1 )); do
    echo $i
done
################################################
simple_counter
0
1
2
3
4
```

### Strings and numbers
`omitted`
[Strings and number](http://billie66.github.io/TLCL/book/chap35.html)

### Arrays
* shell arrays is `0-based`.
* one way to create an array
```shell
declare -a a
```
* usually in the following way
```shell
name[subscript]=value
name=(value1 value2 ...)
days=(Sun Mon Tue Wed Thu Fri Sat)
days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)
```

* output the whole array
**(a small mistake in the book, corrected)**
```shell
$ animals=("a dog" "a cat" "a fish")
for i in ${animals[*]}; do echo $i; done
a dog
a cat
a fish
for i in ${animals[@]}; do echo $i; done
a dog
a cat
a fish
for i in "${animals[*]}"; do echo $i; done
a dog a cat a fish
for i in "${animals[@]}"; do echo $i; done
a dog
a cat
a fish
```
* determine the number of elements
```shell
a[100]=foo
echo ${#a[@]} # number of array elements
1
echo ${#a[100]} # length of element 100
3
```

* find the index of array used
```shell
foo=([2]=a [4]=b [6]=c)
for i in "${foo[@]}"; do echo $i; done
a
b
c
for i in "${!foo[@]}"; do echo $i; done
2
4
6
```

* sort the array
```shell
#!/bin/bash
# array-sort : Sort an array
a=(f e d c b a)
echo "Original array: ${a[@]}"
a_sorted=($(for i in "${a[@]}"; do echo $i; done | sort))
echo "Sorted array: ${a_sorted[@]}"
```

* delete an array
```shell
$ foo=(a b c d e f)
$ echo ${foo[@]}
a b c d e f
$ unset foo
$ echo ${foo[@]}
$
##################################
$ foo=(a b c d e f)
$ echo ${foo[@]}
a b c d e f
$ unset 'foo[2]'
$ echo ${foo[@]}
a b d e f
```

* any reference to an array variable without a subscript refers to **element zero** of the array
```shell
$ foo=(a b c d e f)
$ echo ${foo[@]}
a b c d e f
$ foo=A
$ echo ${foo[@]}
A b c d e f
```

* associative array
associative arrays can **only** be created with the `declare` command using the new `-A` option
```shell
declare -A colors
colors["red"]="#ff0000"
colors["green"]="#00ff00"
colors["blue"]="#0000ff"
echo ${colors["blue"]}
```
### Odds and ends

* group command or subshell
```shell
# group command
# the braces must be separated from the commands by a space
# the last command must be terminated with either a semicolon or a newline prior to the closing brace.
{ command1; command2; [command3; ...] }

# subshell
(command1; command2; [command3;...])
```

```shell
ls -l > output.txt
echo "Listing of foo.txt" >> output.txt
cat foo.txt >> output.txt

###################################################################
{ ls -l; echo "Listing of foo.txt"; cat foo.txt; } > output.txt
(ls -l; echo "Listing of foo.txt"; cat foo.txt) > output.txt
```

* process substitution
```shell
# for processes that produce standard output:
<(a list of commands)

# for processes that intake standard input:
>(a list of commands)
```

```shell
# to solve the problem brought by subshell, we can employ process substitution like this:
read < <(echo "foo")
echo $REPLY
```

* trap
```shell
trap argument signal [signal...]
```

```shell
#!/bin/bash
# trap-demo2 : simple signal handling demo
exit_on_signal_SIGINT () {
    echo "Script interrupted." 2>&1
    exit 0
}
exit_on_signal_SIGTERM () {
    echo "Script terminated." 2>&1
    exit 0
}
trap exit_on_signal_SIGINT SIGINT
trap exit_on_signal_SIGTERM SIGTERM
for i in {1..5}; do
    echo "Iteration $i of 5"
    sleep 5
done
```

* temp file
`mktemp`
```shell
tempfile=$(mktemp /tmp/foobar.$$.XXXXXXXXXX)
echo $tempfile
/tmp/foobar.6593.UOZuvM6654
```

* asynchronous execution
`wait`

```shell
#!/bin/bash
# async-parent : Asynchronous execution demo (parent)
echo "Parent: starting..."
echo "Parent: launching child script..."
async-child &
pid=$!
echo "Parent: child (PID= $pid) launched."
echo "Parent: continuing..."
sleep 2
echo "Parent: pausing to wait for child to finish..."
wait $pid
echo "Parent: child is finished. Continuing..."
echo "Parent: parent is done. Exiting."
```

```shell
#!/bin/bash
# async-child : Asynchronous execution demo (child)
echo "Child: child is running..."
sleep 5
echo "Child: child is done. Exiting."
```

```shell
async-parent
Parent: starting...
Parent: launching child script...
Parent: child (PID= 6741) launched.
Parent: continuing...
Child: child is running...
Parent: pausing to wait for child to finish...
Child: child is done. Exiting.
Parent: child is finished. Continuing...
Parent: parent is done. Exiting.
```
* named pipe
```shell
# it works as `process1 | process2`
process1 > named_pipe
process2 < named_pipe
```

`mkfifo`
```shell
mkfifo pipe1
# process1
ls -l > pipe1
# process2
cat < pipe1
```

### Summay
Well, we have completed our journey. The only thing left to do now is **practice, practice, practice**. Even though we covered a lot of ground in our trek, we barely **scratched the surface** as far as the command line goes. There are still thousands of command line programs left to be discovered and enjoyed. Start digging around in `/usr/bin` and you’ll see!
