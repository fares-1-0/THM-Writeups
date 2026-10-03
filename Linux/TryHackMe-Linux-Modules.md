# TryHackMe - Linux Modules
> personal notes and learning documentation for Linux-Modules room 

## About Room
| Item | Details|
|---|---|
|platform| TryHackMe
|Name| Linux Modules
|Url| 'https://tryhackme.com/room/linuxmodules'
|Category| Linux
|Difficulty| Easy
|Foucs| command-line utilities

---

## Objectives

I learned and practiced how to use:
-   `du`
-   `grep, egrep, fgrep`

---

# 1. `du`

## What is du?
Ans:   
`du` acronym to **disk usage**. It informs me which files/directories consume much space and
does not matter the state of file/dir (hidden or visable)


### Basic Usage  
``` Bash
1. du:> list all directories in current path as (size   path)   # size show in bits  
        Ex: ┌──(kali㉿kali)-[~/Desktop]
            └─$ du                          
            12      ./Reports
            36      .
        In this example all directories on Desktop 

    if we add a directory 'test1' with path './Reports' and add a hidden directory to be sure
        Ex2: ┌──(kali㉿kali)-[~/Desktop]
             └─$ du
             4      ./Reports/test1
             16     ./Reports
             4      ./.secret
             44     .

###  Most Important And Used Flags 
1.1 du -h:> show size in human readable way (KB, GB, ...)
        Ex: ┌──(kali㉿kali)-[~/Desktop]
            └─$ du -h
            4.0K    ./Reports/test1
            16K     ./Reports
            4.0K    ./.secret
            44K     .
1.2 du -a:> list files as well as directories like `du` but lists files also  
        Ex: ┌──(kali㉿kali)-[~/Desktop]
            └─$ du -a
            4       ./.first.txt
            4       ./test.txt
            4       ./Reports/scan_192.168.50.128.txt
            4       ./Reports/scan_45.33.32.156.txt
            0       ./Reports/test1/rere.txt
            4       ./Reports/test1
            16      ./Reports
            4       ./script.sh
            4       ./.secret
            4       ./scanner.sh
            4       ./Bouns_task3.sh
            44      .
1.3 du -d <number>:> choose the deepness of direcotry 
        Ex: ┌──(kali㉿kali)-[~/Desktop]
            └─$ du -d 1                                
            16      ./Reports
            4       ./.secret
            44      .
        Ex2: ┌──(kali㉿kali)-[~/Desktop]
             └─$ du -d 2
             4       ./Reports/test1
             16      ./Reports
             4       ./.secret
             44      .
1.4 du -c:> add total size at the end
        Ex: ┌──(kali㉿kali)-[~/Desktop]
            └─$ du -c  
            4       ./Reports/test1
            16      ./Reports
            4       ./.secret
            44      .
            44      total
1.5 du --time:> add last modified date
        Ex: ┌──(kali㉿kali)-[~/Desktop]
            └─$ du --time
            4       2026-09-29 03:41        ./Reports/test1
            16      2026-09-29 03:41        ./Reports
            4       2026-09-29 03:40        ./.secret
            44      2026-09-29 03:41        .

```
---

# 2. `grep`, `egrep`, `fgrep`

## What are they and the deffrence between them?
Ans: 
`grep` is a regular search command and used to search for a word or a specific pattern inside a file and to understand special characters like (|, *, /, ....) must put '/' before it
`fgrep` is fixed search command and doesn't understandard the special characters
`egrep` is extended grep support the special characters

In past used egrp, fgrep but grep updated and can do thier job `-E` for egrep, `-F` for fgrep

## Basic Usage 
```Bash
2. grep '<pattern/text>' file_path  OR cat file_path | grep '<text\pattern>' file_path:> search for this text in the file  

###  Most Important And Used Flags 
2.1 grep -R '<text>' path:> search for this text recursive in every file in this path until found it or not  
        Ex: ┌──(kali㉿kali)-[~]
            └─$ grep -R 'FTP' ./Desktop
            ./Desktop/test.txt:2121/tcp open  ftp         ProFTPD 1.3.1
            ./Desktop/Reports/scan_192.168.50.128.txt:21    OPEN    FTP
            ./Desktop/Reports/scan_45.33.32.156.txt:21      CLOSED  FTP
            ./Desktop/scanner.sh:   [21]="FTP"      [22]="SSH"

2.2 grep -h '<text>' file_path:> same task but hide file_path from output 
        Ex: ┌──(kali㉿kali)-[~]
            └─$ grep -R -h 'FTP' Desktop
            2121/tcp open  ftp         ProFTPD 1.3.1
            21      OPEN    FTP
            21      CLOSED  FTP
            [21]="FTP"      [22]="SSH"
2.3 grep -i -o -v <text> file_path:> -i : non-senstive case ex: FTP=ftp=FtP=ftP 
                                     -v : ignor the pattern and print all but this pattern no  
                                     -o : print only the patter/text 

2.4 man grep OR grep --h:> to see all flags 

```
---

# 3. tr

## what is tr?
Ans:
`tr` acronym to **translate**. it enables you to manipulate with data-sets and fromating file text as you want 

## Syntax
> `tr` [flags] []

## Basic Usage  
``` Bash
    cat <path>





## Key Tackaways


## References