---
title: Notes, Shell
categories:
  - Doing
  - Shell
  - 
tags:
  - 
  - 
date: 2017-07-01 10:13:10
toc: true

---
~~Last Modified: 2017-07-31 11:18:00~~

### Acknowledge
* What is shell?
 * The shell is a program that takes keyboard commands and passes them to the operating system to carry out.
 * Almost all Linux distributions supply a shell program from the GNU Project called **bash**.
 * **Bash** is an enhanced replacement for **sh**, the original Unix shell program written by Steve Bourne.
* What is terminal emulator?
 * We use a terminal emulator to interact with the shell when using a GUI.
 * KDE uses **konsole** and GNOME uses **gnome-terminal**, though it’s likely called simply **“terminal”** on our menu.
* Shell prompt
```shell
[me@linuxbox ~]$
```
* Some Simple Commands
 * date - Display the current time and date.
 * cal - Display a calendar of the current month.
 * df - Display the current amount of free space on your disk drives.
 * free - Display the amount of free memory.
 * exit - End the terminal session.

<!--- more --->

## Navigate the file system
* pwd - Print name of current working directory
* cd - Change directory
* ls - List directory contents
* file – Determine file type
* less – View file contents

#### important facts about filenames
* Filenames that **begin with** a **period character(.)** are hidden. 
 `ls -a` can display them, `ll -a` can display the detailed lists.
* **Filenames and commands** in Linux, like Unix, are **case sensitive**. The filenames “File1” and “file1” refer to different files.
* If you want to **represent spaces** between words in a filename, use **underscore characters(\_)**. Though Linux supports long filenames which may contain embedded spaces and punctuation characters: period(.), dash(-), underscore(\_).

### Manipulate files and directories
* cp – Copy files and directories
* mv – Move/rename files and directories
* mkdir – Create directories
* rm – Remove files and directories
* ln – Create hard and symbolic links

**Here is a useful tip.** Whenever you use wildcards with `rm` (besides carefully checking your typing!), test the wildcard first with `ls`.
|Wildcard|Meaning|
|:------:|:------|
|*|	Matches any characters|
|?|	Matches any single character|
|[characters]|	Matches any character that is a member of the set characters|
|[!characters]|	Matches any character that is not a member of the set characters|
|[[:class:]]|	Matches any character that is a member of the specified class|

|Character|Class Meaning|
|:-:|:--|
|[:alnum:]|	Matches any alphanumeric character|
|[:alpha:]|	Matches any alphabetic character|
|[:digit:]|	Matches any numeral|
|[:lower:]|	Matches any lowercase letter|
|[:upper:]|	Matches any uppercase letter|

#### ln — Create links
##### create hard links
```shell
ln file link
```
* a hard link may not reference a file that is not on the same disk partition as the link itself.
* a hard link may not reference a directory.
* a hard link is **indistinguishable** from the file itself when listed with `ls`. 
* when a hard link is deleted, the link is removed but the contents of the file itself continue to exist (that is, its space is not deallocated) until all links to the file are deleted.

##### create symbolic links
```shell
ln -s item link
```
* a file pointed to by a symbolic link is **also written**, if you write some something to the symbolic link.
* however when you delete a symbolic link, **only the link is deleted**, not the file itself.

### Use commands
* type – Indicate how a command name is interpreted
* which – Display which executable program will be executed
* man – Display a command’s manual page
* apropos – Display a list of appropriate commands
* info – Display a command’s info entry
* whatis – Display a very brief description of a command
* whereis - Display the path of the executable program of a command
* alias – Create an alias for a command

```shell
# append a new line of alias
echo 'alias foo="cd /usr; ls; cd -"' >> .bashrc
# force bash to re-read the modified .bashrc file
source .bashrc 
```

### I/O redirection
* cat - Concatenate files
* sort - Sort lines of text
* uniq - Report or omit repeated lines
* grep - Print lines matching a pattern
* wc - Print newline, word, and byte counts for each file
* head - Output the first part of a file
* tail - Output the last part of a file
* tee - Read from standard input and write to standard output and files

* `<` operator redirects the standard input, `>` operator redirects the standard output.
`>>` means to append instead of overwriting.
file streams as standard **input, output and error**, the shell references them internally as **file descriptors zero, one and two**, respectively
```shell
# redirect to standard error
ls -l /bin/usr 2> ls-error.txt

# redirect standard output and error to same file
# the redirection of standard error must always occur after
# redirecting standard output or it doesn’t work
ls -l /bin/usr > ls-output.txt 2>&1  # old version
ls -l /bin/usr &> ls-output.txt      # new version
```

* to suppress error messages from a command, a special file called `/dev/null`, and it is a system device called a **bit bucket** which accepts input and does nothing with it.
```shell
ls -l /bin/usr 2> /dev/null
```
* the **pipe** operator `|` (vertical bar), the standard output of one command can be piped into the standard input of another.
  usually assisted with filters.
```shell
# sort and unique files then show
ls /bin /usr/bin | sort | uniq | less
# report the duplicate files with -d
ls /bin /usr/bin | sort | uniq -d | less

# add wc to pipelines to count things
ls /bin /usr/bin | sort | uniq | wc -l
```
* **grep** is a powerful program used to find text patterns within files.
```shell
ls /bin /usr/bin | sort | uniq | grep zip
```
`-i` to ignore case, `-v` to print the lines that do not match the pattern, `-n` to show the line numbers.

* **head** prints the **first ten lines** of a file and the **tail** command prints **the last ten lines** by default.
```shell
# -n to adjust the number of lines to show
head -n 5 ls-output.txt
# -f to allow tail to view files in real-time
tail -f /var/log/messages
```

* **tee** reads standard input and copies it **to both standard output** (allowing the data to continue down the pipeline) and **to one or more files**.
```shell
ls /usr/bin | tee ls.txt | grep zip
```

### Expansion
* pathname expansion
```shell
echo D*
Desktop  Documents
```
* arithmetic expansion
```shell
# / is just integer division, ** is exponentiation
echo $((2+2-2*2/3%10+2**2))
7
```
* brace expansion
```shell
echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back

echo Number_{1..5}
Number_1  Number_2  Number_3  Number_4  Number_5

echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
```
* parameter expansion
```shell
echo $USER
me

# to see a list of available variables
printenv | less
```
* command substitution
```shell
echo $(ls)
Desktop Documents ls-output.txt Music Pictures Public Templates

ls -l $(which cp)
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp

file $(ls /usr/bin/* | grep zip)
/usr/bin/bunzip2:     symbolic link to `bzip2'
```
```shell
# use back-quotes instead of the dollar sign and parentheses
# in older version of bash
ls -l `which cp`
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp
```

#### to control expansion
* double quotes
If you place text inside double quotes, all the special characters used by the shell lose their special meaning and are treated as ordinary characters. 
The **exceptions** are `$`, `\ (backslash)`, and `` (back-quote)`.
* single quotes
All expansions lose their special meaning.
* escape character
You can precede a character with a `\ (backslash)` to selectively prevent an expansion.
```shell
echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
text /home/me/ls-output.txt a b foo 4 me
```
```shell
echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER"
text ~/*.txt   {a,b} foo 4 me
```
```shell
echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER'
text ~/*.txt  {a,b} $(echo foo) $((2+2)) $USER
```

### Keyboard operating techniques

* clear - Clear the screen
* history - Display the contents of the history list

#### cursor movement shortcuts
|Key|Action|
|:-:|:-|
|Ctrl-a|	Move cursor to the beginning of the line.|
|Ctrl-e|	Move cursor to the end of the line.|
|Ctrl-f|	Move cursor forward one character; same as the right arrow key.|
|Ctrl-b|	Move cursor backward one character; same as the left arrow key.|
|Alt-f|	Move cursor forward one word.|
|Alt-b|	Move cursor backward one word.|

#### text editing shortcuts
|Key|Action|
|:----:|:-|
|Ctrl-d|	Delete the character at the cursor location.|
|Ctrl-t|	Transpose(exchange)the character at the cursor location with the one preceding it.|
|Alt-t|	Transpose the word at the cursor location with the one preceding it.|
|Alt-l|	Convert the characters from the cursor location to the end of the word to lowercase.|
|Alt-u|	Convert the characters from the cursor location to the end of the word to uppercase.|

#### cut and paste shortcuts
|Key|Action|
|:----:|:-|
|Ctrl-k|	Kill text from the cursor location to the end of line.|
|Ctrl-u|	Kill text from the cursor location to the beginning of the line.|
|Alt-d|	Kill text from the cursor location to the end of the current word.|
|Alt-Backspace|	Kill text from the cursor location to the beginning of the word. If the cursor is at the beginning of a word, kill the previous word.|
|Ctrl-y|	Yank text from the kill-ring and insert it at the cursor location.|

#### history expansion and shortcuts
```shell
history | grep /usr/bin
# 88  ls -l /usr/bin > ls-output.txt
# to expand the command of history of 88th line
!88
# to expand the last command of history
!!
```

|Key|Action|
|:----:|:-|
|Ctrl-p|	Move to the previous history entry. Same action as the up arrow.|
|Ctrl-n|	Move to the next history entry. Same action as the down arrow.|
|Ctrl-r|	Reverse incremental search. Searches incrementally from the current command line up the history list.|
|Ctrl-o|	Execute the current item in the history list and advance to the next one. This is handy if you are trying to re-execute a sequence of commands in the history list.|

### Permission
* id – Display user identity
* chmod – Change a file’s mode
* umask – Set the default file permissions
* su – Run a shell as another user
* sudo – Execute a command as another user
* chown – Change a file’s owner
* chgrp – Change a file’s group ownership
* passwd – Change a user’s password

#### access rights, read, write, execution
```shell
ls -l foo.txt
-rw-rw-r-- 1 me   me   0 2008-03-06 14:52 foo.txt
# the first one is file type, and the left nine is file mode.
```
##### file types
|Attribute|	File Type|
|:-:|:-|
|-|	a regular file|
|d|	A directory|
|l|	A symbolic link. Notice that with symbolic links, the remainning file attributes are always “rwxrwxrwx” and are dummy values. The real file attributes are those of the file the symbolic link points to.|
|c|	A character special file. This file type refers to a device that handles data as a stream of bytes, such as a terminal or modem.|
|b|	A block special file. This file type refers to a device that handles data in blocks, such as a hard drive or CD-ROM drive.|

##### file mode
![](http://7xru22.com1.z0.glb.clouddn.com/101.png)
|Attribute|	Files|	Directories|
|:-:|:-|
|r|	Allows a file to be opened and read.|	Allows a directory's contents to be listed if the execute attribute is also set.|
|w|	Allows a file to be written to or truncated, however this attribute does not allow files to be renamed or deleted. The ability to delete or rename files is determined by directory attributes.|	Allows files within a directory to be created, deleted, and renamed if the execute attribute is also set.|
|x|	Allows a file to be treated as a program and executed. Program files written in scripting languages must also be set as readable to be executed.|	Allows a directory to be entered, e.g., cd directory.|

#### chmod

```shell
chmod 600 foo.txt
ls -l foo.txt
-rw------- 1 me    me    0  2008-03-06 14:52 foo.txt
```
a few common ones: 7 (rwx), 6 (rw-), 5 (r-x), 4 (r--), and 0 (---).

##### chmod Symbolic Notation
|Notation|Meaning|
|:-:|:-|
|u|	Short for "user", but means the file or directory owner.|
|g|	Group owner.|
|o|	Short for "others", but means world.|
|a|	Short for "all", the combination of "u", "g", and "o".|

##### chmod Symbolic Notation Examples
|Example|Meaning|
|:-:|:-|
|u+x|	Add execute permission for the owner.|
|u-x	|Remove execute permission from the owner.|
|+x|	Add execute permission for the owner, group, and world. Equivalent to a+x.|
|o-rw|	Remove the read and write permission from anyone besides the owner and group owner.|
|u+x,go=rw|	Add execute permission for the owner and set the permissions for the group and others to read and execute. Multiple specifications may be separated by commas.|

#### umask
The umask command controls the default permissions given to a file when it is created. It uses octal notation to express a mask of bits **to be removed** from a file’s mode attributes.
```shell
umask 0022
```

#### others
* The **su** command is used to start a shell as another user. 
```shell
su [-[l]] [user]
```

* The **sudo** command is used to execute commands as a different user (usually the superuser) in a very controlled way.

* The **chown** command is used to change the owner and group owner of a file or directory. Superuser privileges are required to use this command. 
```shell
chown [owner][:[group]] file...
```

* In older versions of Unix, the chown command only changed file ownership, not group ownership. For that purpose, a separate command, **chgrp** was used. It works much the same way as chown, except for being more limited.

* The **passwd** command is used to set passwords for **yourself** (and for other users if you have access to superuser privileges). 
```shell
passwd [user]
```

* more infos, ... `adduser, useradd, groupadd`

### Process
* ps – Report a snapshot of current processes
* top – Display tasks
* jobs – List active jobs
* bg – Place a job in the background
* fg – Place a job in the foreground
* kill – Send a signal to a process
* killall – Kill processes by name
* shutdown – Shutdown or reboot the system

#### Process States
|State|	Meaning|
|:-:|:-|
|R|	Running. This means that the process is running or ready to run.
|S|	Sleeping. A process is not running; rather, it is waiting for an event, such as a keystroke or network packet.|
|D|	Uninterruptible Sleep. Process is waiting for I/O such as a disk drive.|
|T|	Stopped. Process has been instructed to stop. More on this later.|
|Z|	A defunct or “zombie” process. This is a child process that has terminated, but has not been cleaned up by its parent.|
|<|	A high priority process. It's possible to grant more importance to a process, giving it more time on the CPU. This property of a process is called niceness. A process with high priority is said to be less nice because it's taking more of the CPU's time, which leaves less for everybody else.|
|N|	A low priority process. A process with low priority (a “nice” process) will only get processor time after other processes with higher priority have been serviced.|

#### Signals
In the case of **Ctrl-c**, a signal called **INT** (Interrupt) is sent; with **Ctrl-z**, a signal called **TSTP** (Terminal Stop.) 

```shell
kill [-Number] PID
kill [-SIG<Name>] PID
```
|Number|	Name|	Meaning|
|:-:|:-:|:-|
|1|	HUP|	Hangup. |
|2|	INT|	Interrupt. Performs the same function as the Ctrl-c key sent from the terminal. It will usually terminate a program.|
|9|	KILL|	Kill. |
|15|	TERM|	Terminate. This is the default signal sent by the kill command. If a program is still “alive” enough to receive signals, it will terminate.|
|18|	CONT|	Continue. This will restore a process after a STOP signal.|
|19|	STOP|	Stop. This signal causes a process to pause without terminating. Like the KILL signal, it is not sent to the target process, and thus it cannot be ignored.|

### Shell environment
* printenv – Print part or all of the environment
* set – Set shell options
* export – Export environment to subsequently executed programs
* alias – Create an alias for a command

```shell
printenv USER # print environment variable USER
echo $USER    # a same way as above
# export added PATH
PATH=$PATH:$HOME/bin
export PATH
```

### Basic usage of vi
`to do`

### Customize shell prompt
`omitted`

### Package control
|-|Debian Style (.deb)|Red Hat Style (.rpm)|
|:-:|:-:|:-:|
|Distributions (Partial Listing)|Debian, Ubuntu, Xandros, Linspire|Fedora, CentOS, Red Hat Enterprise Linux, OpenSUSE, Mandriva, PCLinuxOS|
|Low-Level Tools|dpkg|apt-get, aptitude|
|High-Level Tools|rpm|yum|
|Package Search Commands|apt-get update; apt-cache search search_string|yum search search_string|
|Package Installation Commands|apt-get update; apt-get install package_name|yum install package_name|
|Low-Level Package Installation Commands|dpkg --install package_file|rpm -i package_file|
|Package Removal Commands|apt-get remove package_name|yum erase package_name|
|Package Update Commands|apt-get update; apt-get upgrade|yum update|
|Low-Level Package Upgrade Commands|dpkg --install package_file|rpm -U package_file|
|Package Listing Commands|dpkg --list|rpm -qa|
|Package Status Commands|dpkg --status package_name|rpm -q package_name|
|Package Information Commands|apt-cache show package_name|yum info package_name|
|Package File Identification Commands|dpkg --search file_name|rpm -qf file_name|

### Storage devices
* mount – Mount a file system
* umount – Unmount a file system
* fsck – Check and repair a file system
* fdisk – Partition table manipulator
* mkfs – Create a file system
* fdformat – Format a floppy disk
* dd – Write block oriented data directly to a device
* genisoimage (mkisofs) – Create an ISO 9660 image file
* wodim (cdrecord) – Write data to optical storage media
* md5sum – Calculate an MD5 checksum

`omitted`

### Networking
* ping - Send an ICMP ECHO_REQUEST to network hosts
* traceroute - Print the route packets trace to a network host
* netstat - Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships
* ftp - Internet file transfer program
* wget - Non-interactive network downloader
* ssh - OpenSSH SSH client (remote login program)

### Find files
* locate – Find files by name
* find – Search for files in a directory hierarchy

We will also look at a command that is often used with file search commands to process the resulting list of files:

* xargs – Build and execute command lines from standard input

In addition, we will introduce a couple of commands to assist us in or exploration:

* touch – Change file times
* stat – Display file or file system status

### Archive and backup
* gzip – Compress or expand files
* bzip2 – A block sorting file compressor
* tar – Tape archiving utility
* zip – Package and compress files
* rsync – Remote file and directory synchronization

### Regex
grep: global regular expression print
`-n`: Prefix each matching line with the number of the line within the file. May also be specified --line-number.
`-r`: Recursively search subdirectories listed.

#### metacharacters
Regular expression metacharacters consist of the following:
```
^ $ . [ ] { } - ? * + ( ) | \
```

##### any character
The dot or period(.) character is used to match any character. 

##### anchors
The caret `(^)` and dollar `($)` sign characters are treated as anchors. This means that they cause the match to occur only if the regular expression is found **at the beginning of the line** or **at the end of the line**.
```shell
# crossword puzzles
grep -i '^..j.r$' /usr/share/dict/words
Gujar
Kajar
Major
major
```

##### bracket expressions
Bracket expressions is used to match a single character from a specified set of characters.
```shell
grep -h '[bg]zip' dirlist*.txt
bzip2
bzip2recover
gzip
```

Metacharacters lose their special meaning when placed within brackets. However, **two cases** have different meanings.
The first is the caret `(^)`, which is used to indicate **negation**; the second is the dash `(-)`, which is used to indicate a **character range**.
```shell
grep -h '[^bg]zip' dirlist*.txt
bunzip2
gunzip

# it will match all filenames starting with letters and numbers:
grep -h '^[A-Za-z0-9]' dirlist*.txt

# to include a dash, making it the first character in the expression
# it will match every filename containing a dash, or a upper case “A” or an uppercase “Z”.
grep -h '[-AZ]' dirlist*.txt
```
#### POSIX Character Classes
|Character Class|	Description|
|:-:|:-|
|[:alnum:]|	The alphanumeric characters. In ASCII, equivalent to: [A-Za-z0-9]|
|[:word:]|	The same as [:alnum:], with the addition of the underscore (\_) character.|
|[:alpha:]|	The alphabetic characters. In ASCII, equivalent to: [A-Za-z]|
|[:digit:]|	The numerals zero through nine.|
|[:lower:]|	The lowercase letters.|
|[:space:]|	The whitespace characters including space, tab, carriage return, newline, vertical tab, and form feed. In ASCII, equivalent to: [ \t\r\n\v\f]|
|[:upper:]|	The upper case characters.|
|[:xdigit:]|	Characters used to express hexadecimal numbers. In ASCII, equivalent to: [0-9A-Fa-f]|

#### basic regular expressions (BRE) and extended regular expressions (ERE)
What’s the difference between BRE and ERE?
It’s a matter of metacharacters. 
With BRE, the following metacharacters are recognized:
```
^ $ . [ ] *
```
All other characters are considered literals.

With ERE, the following metacharacters (and their associated functions) are added:
```
( ) { } ? + |
```

#### alternation
```shell
# it will match the filenames in our lists that start with either “bz”, “gz”, or “zip
grep -Eh '^(bz|gz|zip)' dirlist*.txt

# it changes to match any filename that begins with “bz” or contains “gz” or contains “zip”
grep -Eh '^bz|gz|zip' dirlist*.txt
```

#### quantifier
The question mark `(?)` means **making the preceding element optional (matching 0 or 1 element)**.
```shell
# to valid if it matched either of these two forms:
# (nnn) nnn-nnnn
# nnn nnn-nnnn

echo "(555) 123-4567" | grep -E '^\(?[0-9][0-9][0-9]
\)? [0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]$'
(555) 123-4567

echo "555 123-4567" | grep -E '^\(?[0-9][0-9][0-9]\)
? [0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]$'
555 123-4567
```

The asterisk `(*)` means **making the preceding element optional (matching 0 or any number of times elements)**.
```shell
# to see if a string was a sentence
# it starts with an uppercase letter
# then contains any number of upper and lowercase letters and spaces
# and ends with a period. 
echo "This works." | grep -E '[[:upper:]][[:upper:][:lower:] ]*\.'
This works.

echo "this does not" | grep -E '[[:upper:]][[:upper:][:lower:] ]*\.'
```

The plus mark `(+)` means **making the preceding element optional (matching 1 or any number of times elements)**.
```shell
# it will match lines consisting of groups of one or more alphabetic characters separated by single spaces
echo "This that" | grep -E '^([[:alpha:]]+ ?)+$'
This that

echo "a b c" | grep -E '^([[:alpha:]]+ ?)+$'
a b c
```

The `{` and `}` metacharacters are used to **express minimum and maximum numbers of required matches**. They may be specified in four possible ways:
|Specifier|	Meaning|
|:-:|:-|
|{n}|	Match the preceding element if it occurs exactly n times.
|{n,m}|	Match the preceding element if it occurs at least n times, but no more than m times.|
|{n,}|	Match the preceding element if it occurs n or more times.|
|{,m}|	Match the preceding element if it occurs no more than m times.|
```shell
echo "(555) 123-4567" | grep -E '^\(?[0-9]{3}\)? [0-9]{3}-[0-9]{4}$'
(555) 123-4567
```

#### find files
```shell
# it will reveal pathnames that contain embedded spaces and other potentially offensive characters
find . -regex '.*[^-\_./0-9a-zA-Z].*'
```

#### less and vim
They support basic regular expressions.
Pressing the `/` key followed by **a regular expression** will perform a search.

### Manipulate text
* cat – Concatenate files and print on the standard output
* sort – Sort lines of text files
* uniq – Report or omit repeated lines
* cut – Remove sections from each line of files
* paste – Merge lines of files
* join – Join lines of two files on a common field
* comm – Compare two sorted files line by line
* diff – Compare files line by line
* patch – Apply a diff file to an original
* tr – Translate or delete characters
* sed – Stream editor for filtering and transforming text
* aspell – Interactive spell checker

#### cat
`-A`: display non- printing characters in the text.
`-n`: show line numbers.
`-s`: suppress the output of multiple blank lines.

#### sort
`-n`: perform sorting based on the numeric evaluation of a string rather than alphabetical value.
`-r`: sort in reverse order. results are in descending rather than ascending order.
`-b`: ignore leading blanks.
`-k field1[,field2]`: sort based on a key field located from field1 to field2 rather than the entire line.

```shell
# by specifying -k 3.7 we instruct sort to 
# use a sort key that begins at the seventh character within the third field
sort -k 3.7nbr -k 3.1nbr -k 3.4nbr distros.txt
Fedora         10    11/25/2008
Ubuntu         8.10  10/30/2008
SUSE           11.0  06/19/2008
...
```

#### unique
`-u`: remove duplicates from the sorted output.
`-d`: only output duplicated lines, rather than unique lines.

#### cut
`-f filed_list`, `-c char_list`, `-d delimiter_char`
```shell
cut -f 3 distros.txt
12/07/2006
11/25/2008

cut -f 3 distros.txt | cut -c 7-10
2006
2008

# default delimiter is tab
# what if we wanted a file fully manipulated with cut by characters, rather than fields
expand distros.txt | cut -c 23-

# set delimiter to ':'
cut -d ':' -f 1 /etc/passwd | head
```

#### paste
The paste command does the opposite of cut. Rather than extracting a column of text from a file, it adds one or more columns of text to a file.

#### join
A join is an operation usually associated with relational databases where data from multiple tables with a shared key field is combined to form a desired result.

#### comm and diff
* comm
The comm program compares two text files and displays the lines that are unique to each one and the lines they have in common.
* diff
`-c`: **contex format**
```shell
diff -c file1.txt file2.txt
*** file1.txt    2008-12-23 06:40:13.000000000 -0500
--- file2.txt   2008-12-23 06:40:34.000000000 -0500
***************
*** 1,4 ****
- a
  b
  c
  d
--- 1,4 ----
  b
  c
  d
  + e
```
`-u`: **unified format**
```shell
diff -u file1.txt file2.txt
--- file1.txt 2008-12-23 06:40:13.000000000 -0500
+++ file2.txt 2008-12-23 06:40:34.000000000 -0500
@@ -1,4 +1,4 @@
-a
 b
 c
 d
+e
```

#### patch
To prepare a diff file for use with patch, the GNU documentation (see Further Reading below) suggests using diff as follows:
```shell
diff -Naur old_file new_file > diff_file
```
Once the diff file has been created, we can apply it to patch the old file into the new file:
```shell
patch < diff_file
```

#### tr
The tr program is used to transliterate characters.
```shell
# transliterate characters
echo "lowercase letters" | tr a-z A-Z
LOWERCASE LETTERS
echo "lowercase letters" | tr [:lower:] A
AAAAAAAAA AAAAAAA

# delete '\r' in dos files
tr -d '\r' < dos_file > unix_file

# “squeeze” (delete) repeated instances of a character
echo "aaabbbccc" | tr -s ab
abccc
```

#### sed
`-n`: no auto-print;

sed Address Notation
|Address|	Description|
|:-:|:-|
|n|	A line number where n is a positive integer.|
|$|	The last line.|
|/regexp/|	Lines matching a POSIX basic regular expression. Note that the regular expression is delimited by slash characters. Optionally, the regular expression may be delimited by an alternate character, by specifying the expression with \cregexpc, where c is the alternate character.|
|addr1,addr2|	A range of lines from addr1 to addr2, inclusive. Addresses may be any of the single address forms above.|
|first~step|	Match the line represented by the number first, then each subsequent line at step intervals. For example 1~2 refers to each odd numbered line, 5~5 refers to the fifth line and every fifth line thereafter.|
|addr1,+n|	Match addr1 and the following n lines.|
|addr!|	Match all lines except addr, which may be any of the forms above.|

```shell
sed -n '1,5p' distros.txt
SUSE           10.2     12/07/2006
Fedora         10       11/25/2008
SUSE           11.0     06/19/2008
Ubuntu         8.04     04/24/2008
Fedora         8        11/08/2007

sed -n '/SUSE/p' distros.txt
SUSE         10.2     12/07/2006
SUSE         11.0     06/19/2008

sed -n '/SUSE/!p' distros.txt
Fedora         10       11/25/2008
Ubuntu         8.04     04/24/2008
```

sed Basic Editing Commands
|Command|	Description|
|:-:|:-|
|=|	Output current line number.|
|a|	Append text after the current line.|
|d|	Delete the current line.|
|i|	Insert text in front of the current line.|
|p|	Print the current line. By default, sed prints every line and only edits lines that match a specified address within the file. The default behavior can be overridden by specifying the -n option.|
|q|	Exit sed without processing any more lines. If the -n option is not specified, output the current line.|
|Q|	Exit sed without processing any more lines.|
|s/regexp/replacement/|	Substitute the contents of replacement wherever regexp is found. replacement may include the special character &, which is equivalent to the text matched by regexp. In addition, replacement may include the sequences \1 through \9, which are the contents of the corresponding subexpressions in regexp. For more about this, see the discussion of back references below. After the trailing slash following replacement, an optional flag may be specified to modify the s command’s behavior.|
|y/set1/set2|	Perform transliteration by converting characters from set1 to the corresponding characters in set2. Note that unlike tr, sed requires that both sets be of the same length.|

```shell
# 's/([0-9]{2})/([0-9]{2})/([0-9]{4})$/\3-\1-\2/'
sed 's/\([0-9]\{2\}\)\/\([0-9]\{2\}\)\/\([0-9]\{4\}\)$/\3-\1-\2/' distros.txt
SUSE           10.2     2006-12-07

echo "aaabbbccc" | sed 's/b/B/'
aaaBbbccc

echo "aaabbbccc" | sed 's/b/B/g'
aaaBBBccc
```

#### aspell
an interactive spelling checker. 
```shell
aspell check textfile
```

### Format text output
`omitted`

### Printer
`ommited`

### Compile source code
Why compile software? There are two reasons:

1. Availability. Despite the number of precompiled programs in distribution repositories, some distributions may not include all the desired applications. In this case, the only way to get the desired program is to compile it from source.
2. Timeliness. While some distributions specialize in cutting edge versions of programs, many do not. This means that in order to have the very latest version of a program, compiling is necessary.

```shell
[~]$ mkdir src
[~]$ cd src
ftp ftp.gnu.org
anonymous
ftp> cd gnu/diction
ftp> ls
150 Here comes the directory listing.
-rw-r--r-- 1 1003 65534 68940 Aug 28 1998 diction-0.7.tar.gz
-rw-r--r-- 1 1003 65534 90957 Mar 04 2002 diction-1.02.tar.gz
-rw-r--r-- 1 1003 65534 141062 Sep 17 2007 diction-1.11.tar.gz
ftp> get diction-1.11.tar.gz
226 File send OK.
ftp> bye
[src]$ ls
diction-1.11.tar.gz

# unpack
[src]$ tar -xzf diction-1.11.tar.gz
[src]$ cd diction-1.11

# configure, compile and install
[diction-1.11]$ ./configure
[diction-1.11]$ make
[diction-1.11]$ make install
```

### Reference
[快乐的 Linux 命令行](http://billie66.github.io/TLCL/book/index.html)