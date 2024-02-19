---
date: 2024-02-16 21:48
tags:
  - iac
  - terminal
internal-links:
---
1. Intro to Linux
2. Basic CLI commands
3. Understanding files in Linux
4. filter and redirection
5. users and group
6. sudo
7. software management
8. services & processes
9. good to know commands
10. server management

![[Pasted image 20240216215547.png]]

Linux Principles
- everything is a file (including the hardware)
- small single purpose programs
- ability to chain programs together for complex operations
- avoid captive user interface

Why Linux
- opensource
- community support
- support wide variety of hardware
- customization
- most servers runs on linux
- automation

![[Pasted image 20240216220051.png]]

Popular Desktop Linux OS
- Ubuntu Linux
- Linux Mint
- Arch Linux
- Fedora
- Debian
- OpenSuse

Popular Server Linux OS
- Red Hat Enterprise Linux (not free or opensource)
- Ubuntu Server (opensource)
- Centos (opensource)
- SUSE Enterprise Linux

RPM based - RHEL, Centos, Oracle Linux (better for running servers)
Debian based - Ubuntu, Kali Linux (better for devOps and automation)

Directory structure
root user commands in /sbin, /usr/sbin (custom sbin)
websites or mysql data in /var
![[Pasted image 20240216222146.png]]

The command syntax is in the following order:
command options arguments

vim editor
[[Vim to optimize coding workflow]]
default is command mode
`i` to go into insert mode
`:` to enter into extended mode
`o` is inserted in the next line
`:q!` forcefully quit
`:su nu` shows line numbers
`G` last line
`gg` first line
`yy` to copy or yank then move to line you want to paste and `p` to paste below `P` to paste above
`4yy` to copy 4 lines
`dd` to delete but also a cut operation
`u` to undo
`/` to search and `n` to go to next search

`ls -l`
when it starts with `-` it's a file, could be text, binary, etc
when it starts with `d` it's a directory
`c` is a character file
`b` is a block (block disk) file
`l` is link
`s` is socket
`p` is pipe

`ln -s` is a command to create a soft link
give it a source and a destination
`unlink destination` removes the link

`ls -lt` is sorted based on time
`ls -ltr` sorted in reverse

`history` to get vagrant user
`sudo -i` then `history` to get history of root user

filters and redirections is very useful and important

`grep` to find the line of a text within a file
`grep quiz exam.txt` finds the line that contians `quiz` within `exam.txt`
`grep -i` for case insensitive

`grep -i firewall *` to find `firewall` in all files in directory

`grep -iR` to account for directories

up to 28. Filters @7:12

`grep -vi firewall *` is reverse search to find everything that doesn't have `firewall` on the line

`less file-path-to-read` is a reader not an editor, can search with `/`, move with up and down arrow
`more file-path-to-read`, `q` to quit
`head file-path-to-read` to see first 10 lines or `head -20 file-path-to-read`
`tail -2 file-path-to-read` shows last 2 lines, default is last 10 lines
`tail -f file-path-to-read` is dynamic and good for reading logs as they come in

`cut -d: -f1 your-file-path` cut is only good if you have well defined delimiters separating columns like here with a colon in `etc/passwd` file

`awk -F':' '{print $1}' /etc/passwd` to get the first column. `awk` lets you use regular expressions and more advanced search

can use `vim` to find and replace by opening up the file in vim and then
`:%s/text-to-replace/replace-with-this-text`. This will only replace once per line to replace globally add a `g`, it will look like `:%s/text-to-replace/replace-with-this-text/g`

`sed 's/text-to-replace/replace-with-this-text/g' *` to do this on the terminal via bash. will print it on the screen without actually doing it yet
add `-i` to execute `sed -i 's/text-to-replace/replace-with-this-text/g' *`

29 Redirection
Default for commands print it to the screen but we can add it to another file using redirection
`uptime > /tmp/sandbox-bash.txt` will create file if it doesn't exist and will overwrite if the file does exist. Use double arrows to append
`uptime >> /tmp/sandbox-bash.txt`

`/dev/null` is a black hole, if you don't want to print anything on screen or redirect to any files you can redirect to here
`cat /dev/null/ > /tmp/sandbox-bash.txt` to clear the file

`wrong-command -m > /dev/null` will still print out the error indicating command is not found
`wrong-command -m 2> /dev/null` for standard error
`wrong-command -m 1> /dev/null` for standard out
`&>>` redirect and append all output to file

`wc -l /etc/passwd` counts the line in the file
`ls | wc -l` to count how many files are in the directory. the output of `ls` is the input to the `wc -l` command
`ls | grep host` to find all file names that contain host

`free -m` to see the free memory space

Find the file itself rather than finding whats within files
`find /etc -name host*` where `/etc` is file path, `-name` is option and `host*` files that start with `host`

`yum install mlocate -y` to install another search command
`updatedb`
`locate host` is not a real time search, searches in the database that was created from `updatedb`

30 Users and Groups

Used to control access to files
Each user will have a username `uid` stored in `/etc/passwd`
user's password stored in `/etc/shadow` in encrypted form

![[Pasted image 20240219100203.png]]

`head -1 /etc/passwd` prints out
`root:x:0:0:root:/root:/bin/bash` has 7 columns
1. root => user name
2. x => link to shadow file
3. 0 => user-id
4. 0 => group id
5. root => comment
6. /root => home directory
7. /bin/bash => login shell

`id vagrant` gets info about the vagrant user
`useradd ansible` to add a user name `ansible`
`groupadd devops` will add a group

`usermod -aG devops ansible` where `G` is suplementary group, `g` is primary group
or just open file and edit with `vim /etc/group`
`passwd ansible` to create a new password for this user

`su - ansible` as root user you can just switch to any other user without being asked a password
`exit` to log out of the user

`last` to show the login times
`who` to show current login user
`yum install lsof -y`
`lsof -u vagrant` will show all the files opened by this user

`userdel testuser` to delete user
`rm -rf /home/testuser`
``
`userdel -r testuser` to delete user and home directory
`groupdel devops` to delete group

31 File Permission
Each file has an owner and permissions
`ls -l | head -1`
`-rw-r--r-- 1 root root       3028 Aug 24  2021 adduser.conf`

- `-` is filetype
- `rw-` is permission for user
- `r--` is permission for group
- `r--` is permission for other
- r => read, can do `ls`
- w => write, can make changes and delete
- x => execute, you can do `cd` on the directory
- - => missing

check permission of directory with `ls -ld /etc`
`chown testuser:testgroup /tmp/sandboxdir` to change ownership
`chmod o-x` to change permissions for other user and remove the execute permission
`chmod o-r` to remove read permission for other user
`chmod g+r` to add read permission for group
also have number for permissions
- 4 is read
- 2 is write
- 1 is execute
- so 7 means this user has all permissions
- `chmod 640 myfile` means read and write for user, read for group and none for other

32 Sudo
`sudo -i` to switch to root user
`su - vagrant` to switch from root to vagrant
add sudo command to as user
`ls -l /etc/sudoers` 
`visudo` to open in vim editor and add `ansible` user
![[Pasted image 20240219110656.png]]

Editing the sudoer file is risky!
A better option is:
instead, we can edit the sudoer.d/ directory. There is a file for each user
`cp  ~/etc/sudoers.d/vagrant ~/etc/sudoers.d/testgroup`
group name has be to `%testgroup` because it's a group name


