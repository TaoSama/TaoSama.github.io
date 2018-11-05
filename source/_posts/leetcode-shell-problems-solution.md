---
title: Solutions, Leetcode Shell Problems
categories:
  - Doing
  - Shell
  - 
tags:
  - 
  - 
date: 2017-08-07 18:06:10
toc: true

---
~~Last Modified: 2017-08-25 10:53:10~~

* 193\. Valid Phone Numbers
* 195\. Tenth Line
* 192\. Word Frequency
* 194\. Tranpose File

<!--- more --->

### 193. Valid Phone Numbers
You may assume that a valid phone number must appear in one of the following two formats: `(xxx) xxx-xxxx or xxx-xxx-xxxx. (x means a digit)`
```shell
# Read from the file file.txt and output all valid phone numbers to stdout.
grep -E '^(([0-9]{3}-[0-9]{3}-[0-9]{4})|(\([0-9]{3}\) [0-9]{3}-[0-9]{4}))$' file.txt
```

### 195. Tenth Line
How would you print just the 10th line of a file?
```shell
# Read from the file file.txt and output the tenth line to stdout.
line_num=0
while read line && ((line_num < 10)); do
    line_num=$line_num+1
    if ((line_num == 10)); then
        echo $line
        break
    fi
done < file.txt
```

### 192. Word Frequency

```shell
# Read from the file words.txt and output the word frequency list to stdout.
declare -A freq
while read word; do
    ((++freq[$word]))
done < <(tr -s " " "\n" < words.txt)
# echo ${words[*]}
for word in ${!freq[*]}; do
    echo $word ${freq[$word]}
done | sort -k 2nbr
```

**it seems that there some problems with shell array...**
```shell
➜  ~ a+=1
➜  ~ a+=1
➜  ~ echo ${a[*]}
11
➜  ~ for i in ${a[*]}; do
for> echo $i
for> done
11
➜  ~ for i in ${a[@]}; do
for> echo $i
for> done
11
➜  ~ for i in "${a[@]}"; do
for> echo $i
for> done
11
➜  ~ for i in "${a[*]}"; do
for> echo $i
for> done
11
➜  ~ echo ${a[1]}
1
➜  ~ echo ${a[2]}
1
➜  ~ echo 'num of elemets='${#a[*]}
num of elemets=2

# it looks rather werid... so avoid using it...
```

### 194. Tranpose File
Given a text file `file.txt`, transpose its content.
You may assume that each row has the same number of columns and each field is separated by the `' '` character.
For example, if `file.txt` has the following content:
```bash
name age
alice 21
ryan 30

# Output the following:
name alice ryan
age 21 30
```

```bash
# Read from the file file.txt and print its transposed content to stdout.
awk '
{
    for(i = 1; i <= NF; ++i) {
        if(1 == NR) {
            s[i] = $i;
        }
        else {
            s[i] = s[i] " " $i 
        }
    }
}
END {
    for(i = 1; s[i] != ""; ++i) {
        print s[i];
    }
}' file.txt
```