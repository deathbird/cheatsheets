# Linux / Bash CLI Cheatsheet

**You should use "double quotes" for any argument that contains expansions (such as $variable or $(command) expansions)
and 'single quotes' for any other arguments. Single quotes make sure that everything in the quotes remains literal.**


## Links

https://superuser.com/questions/176783/what-is-the-difference-between-executing-a-bash-script-vs-sourcing-it/176788#176788
https://serverfault.com/questions/261802/what-are-the-functional-differences-between-profile-bash-profile-and-bashrc

http://johnstowers.co.nz/pages/bash-cheat-sheet.html
https://github.com/LeCoupa/awesome-cheatsheets/blob/master/languages/bash.sh
https://www.panix.com/~elflord/unix/bash-tute.html
https://learnpythonthehardway.org/book/appendixa.html
https://guide.bash.academy/
https://tiswww.case.edu/php/chet/bash/bashref.html
http://mywiki.wooledge.org/BashGuide
http://mywiki.wooledge.org/BashFAQ
http://wiki.bash-hackers.org/
https://terminalsare.sexy/

http://www.gnu.org/software/bash/manual/bash.html

DDOS attack investigation:
https://www.2daygeek.com/count-check-apache-concurrent-connections-using-netstat-command/#

## Tools
* [ShellCheck - finds bugs in your shell scripts](https://www.shellcheck.net/)

## Variables
* Environment vars: Defined for the current shell and inherited by child shells and processes.
* Shell vars: Contained exclusively in the shell that are defined.

```bash
# print all enviroment variables
printenv

# print specific env variable
printenv SHELL
echo $SHELL

# show shell variables (may display also functions), use less to paginate
set
set | less

# to not display functions
set -o posix

# set a variable VAR
VAR=TEST

# display variable VAR value
set | grep VAR
echo $VAR

# Export a shell variable to the environment, becomes environment variable!
export VAR
# or more short version of set and export in one step
export VAR=TEST

# unset environment variable
env -u VAR

#unsert shell variable
unset VAR

#display shell options
set -o

#set on a shell option
set +o posix

```

## Scripting tips
**Difference of ${} vs $()**
The expression $(command) is a modern synonym for `command` which stands for command substitution; it means, run command and put its output here. So
```
echo "Today is $(date). A fine day."
```
will run the date command and include its output in the argument to echo.

By contrast, ${variable} is just a disambiguation mechanism, so you can say ${var}text when you mean, the contents of the variable var, followed by text (as opposed to $vartext which means, the contents of the variable vartext).

REMEMBER TO ALWAYS **use "double quotes" for any argument that contains expansions (such as $variable or $(command) expansions) and 'single quotes' for any other arguments. Single quotes make sure that everything in the quotes remains literal.**

```
# execute a command with setting some env variables before-hand
TRANSIFEX_TOKEN=***** \
TRANSIFEX_PROJECT=txclie \
TRANSIFEX_USER=test_client \
TX_HOST=http://tx.loc:8000 \
TX_CLONE_FOLDER=txci_folder \
RANDOM=123 \
bash -c "/Users/jack_dimas/tx/transifex-client/contrib/tx_commands.sh"

# Exit script on fail
set -e

# Retrying logic to execute a command that might fail with a sleep timeout in secs in between tries.
# Note the use of && break to break out of the loop on success and || operator to execute sleep on error.
# Also note that the previous set -e command has no influence in this compound command (we effectively
# catch the error and retry)!!!
for i in 1 2 3 4; do echo "Attempt to execute command..."; <command here> && break || sleep 20; done

```

## Various shell profile files useful for setting variables, aliases and functions
TODO

## Tricks
* Press Ctrl+R for searching the shell history. Type in some characters and then Ctrl+R to search for similar text in the shell command history.
* Use the history command to display the list of the previously executed shell commands
* !! execute the previous command
```bash
# execute previous command
!!
# execute the 4th previous command from the history
!-4
# execute the 7th command in history
!7
# execute command with the previous command arguments
cd /var/log
ls !*
# append previous command on the end of current command
cat /etc/shadow
sudo !!



```


## BASH keyboard shortcuts
[bash shortcuts](keybindingsCheatsheet.md#bash-shortcuts)


## Various BASH useful
```bash
# last boot time:
who -a

# where bash finds the program to run for a command name, the -a switch shows all posibilities
$ type -a echo
echo is a shell builtin
echo is /bin/echo

# set environment variable
$ PATH=$PATH:~/bin:/usr/local/bin:/bin:/usr/bin

# set environment variable permanently for user by editing ~/.profile file, for example last line:
# set PATH so it includes user's private bin directories
PATH="$HOME/bin:$HOME/.local/bin:$PATH:/opt/PhpStorm-183.4886.46/bin"

# show environment variable
echo "$PATH"

# You should use "double quotes" for any argument that contains expansions (such as $variable or $(command) expansions)
# and 'single quotes' for any other arguments. Single quotes make sure that everything in the quotes remains literal.
# Escape using backslash \ inside double quotes.

# Follow log:
tail -100f /path/to/file | grep the_string_to_search_for

# show users
# last field for every user is the shell used, ie /bin/bash
cat /etc/passwd

# show groups
cat /etc/group

# show groups the current user is in:
groups $USER

# add user to group
sudo usermod -aG docker <username>

# display configuration system variables
sudo sysctl -A | grep inotify

# edit/add configuration system variables
sudo vi /etc/sysctl.conf
sudo sysctl -p --system  # apply changes and then restart your application(s) to get changed value(s).

# determine linux version - variant
cat /etc/os-release
hostnamectl
# find linux kernel version
uname -r

# $- is a string listing of all the current shell option flags. It will contain i if the shell is interactive.
echo $i
himBHs
```

## sudo vs su

1. sudo runs a command as the root user
2. su <username> opens a shell impersonating <username>.
`su <username> -c "command to execute"  # to execute a command`
In Ubuntu root user has no password so there is a difficulty to run su as root user ...


gksu / gksudo : Run a graphical command as root / another user
`$ gksu nautilus`

## dpkg

Packages are manually installed via the dpkg command (Debian Package Management System). dpkg is the backend to commands like apt-get and aptitude, which in turn are the backend for GUI install apps like the Software Center and Synaptic.

https://askubuntu.com/questions/40779/how-do-i-install-a-deb-file-via-the-command-line

```bash
# Install a package
sudo dpkg -i {package_name}
sudo dpkg -i skype-ubuntu-precise_4.2.0.11-1_i386.deb
# Remove a package
sudo dpkg -r {package_name}
sudo dpkg -r vlc
# Remove a package and its configuration files
sudo dpkg -P {package_name}
sudo dpkg -P vlc
# List all installed packages.
# You can pipe the command λ less (a pager) so you can more easily scroll the content:
dpkg -l | less
# Check if the package is installed or not
dpkg -l {package_name}
dpkg -l vlc
# See whether a package is installed or not
# And this will show the location where the package will be installed. Here -S (capital S) to search whether the package was installed or not.
sudo dpkg -S {package_name}
sudo dpkg -S skype
```

## apt-get
```bash
sudo apt list --installed | grep php   # list installed php packages

sudo apt-cache search php7-*    # find the name of a package from repos

sudo apt-get install php7.1-mbstring

# SSH install in Ubuntu:
sudo apt-get install openssh-server
sudo service ssh status

# completely uninstall (application + system configuration files) an application
$ sudo apt purge gitkraken
```

## yum (CentOS package manager)
```bash
# check if app is installed (centos)
yum list installed httpd
yum list installed mysql-server

# check if apache is installed alternative (centos)
rpm -qa | grep httpd

# CentOS 6.5 setup apache, mysql, php
------------------------------------
sudo yum install httpd

# mysql 5.6 installation
sudo yum localinstall mysql-community-release-el6-5.noarch.rpm
sudo yum install mysql-community-server
sudo service mysqld start
sudo /usr/bin/mysql_secure_installation
sudo vim /etc/my.cnf
sudo service mysqld restart

# php 5.5 set repo (https://webtatic.com/packages/php55/)

rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm

# install php
sudo yum install php55w php55w-opcache php55w-cli php55w-common php55w-gd php55w-mbstring php55w-mcrypt php55w-mysql php55w-pdo php55w-soap php55w-xml

# restart apache

```

## files (find, compare, zip...)
**Everything is a file: even a device (monitor, printer), a directory etc.**
```bash
# list only hidden files
$ ls -ld .?*

# count how many files of certain pattern exist in a folder
find . -name '*.json' -maxdepth 1 -printf '.' | wc -m

# Create and extract a .tar.gz archive using command line
# create a tar.gz archive from a given folder you can use the following command
tar -zcvf tar-archive-name.tar.gz source-folder-name.
# extract - unzip a tar.gz compressed archive you can use the following command.
tar -zxvf tar-archive-name.tar.gz.
# list files in tar.gz file
tar -tvf tar-archive-name.tar.gz.

# unzip *.gz
$ gunzip file1.gz

# create a file.zip file from the contents of a directory (recursively)
zip -r file.zip folder_to_zip/

# Display file or file system status, timestamps (Access, Modify, Change)
# https://unix.stackexchange.com/questions/2464/timestamp-modification-time-and-created-time-of-a-file
$ stat <filename>

# Locate the binary file/cmd
$ which apache2

# Find if cmd is builtin or external
type -a cd
cd is a shell builtin
type -a awk
awk is /usr/bin/awk

# Locate the binary, source, and manual-page files for a command
$ whereis apache2

# Show all possibilities of a command
$type -a echo
echo is a shell builtin
echo is /bin/echo

# compare files / folders:
# -r : recursive, -q : short syntax, -l : paginate
diff -rql  nlo-toto/Public/static/cms/ CanvasDocker/Public/static/cms/

# will show if two binary files are the same or not:
[me@host ~]$ diff 1.bin 2.bin
Binary files 1.bin and 2.bin differ
# If there is no output from the command, it means that the files have no differences.
```

## Get Help
```bash
# for shell built-in commands use help:
help cd

# for external commands:
man <term> # term can be a bash command, a config file ( or daemon (ex. apache2)

# search for command in manual pages
man -k <search-term>
apropos <search-term>

# GLOBAL search for command in manual pages
man -K <search-term>

# find info for files. Linux is extensionless (extension dont mean a thing).
file <filename>

# description of manual page
$ whatis apache2

# Locate the binary, source, and manual-page files for a command
$ whereis apache2

# man sections to open as: man <section> <file>
       1   Executable programs or shell commands
       2   System calls (functions provided by the kernel)
       3   Library calls (functions within program libraries)
       4   Special files (usually found in /dev)
       5   File formats and conventions eg /etc/passwd
       6   Games
       7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
       8   System administration commands (usually only for root)
       9   Kernel routines [Non standard]

# for example passwd is a command and a file (*.gz are man pages)
$ whereis passwd
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man5/passwd.5.gz /usr/share/man/man1/passwd.1ssl.gz /usr/share/man/man1/passwd.1.gz
$ man passwd     # opens the first manual found
$ man 5 passwd   # opens a page from section 5 -> man <section> <file>


# Explanation of linux folder hierarchy
$ man hier
```
## Process manage / info

system services mgmt: https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units
logging :             https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs
unit files:           https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files

```bash
# linux version:
	cat /etc/*release*
	cat /proc/version
	cat /proc/cpuinfo

# Using systemctl is the latest way:

> systemctl list-units --all | grep apache
  apache2.service                           loaded    inactive dead      LSB: Apache2 web server

> systemctl                                        # see a list of all the active (ONLY) units
> systemctl list-units --all --state=inactive      # filter inactive services
> systemctl list-units --type=service              # filter units of type service

> sudo systemctl status apache2
> systemctl cat apache2.service                    # To display the unit file that systemd has loaded into its system
> sudo systemctl start apache2
> sudo systemctl stop apache2
> sudo systemctl restart apache2
> sudo systemctl reload apache2                     # reload service configuration without restart if supported!!
> sudo systemctl reload-or-restart apache2.service
> sudo systemctl enable application.service         # enalbe service for autostart on system boot
> sudo systemctl disable application.service        # disable
> systemctl is-active application.service
> systemctl is-enabled application.service
> systemctl is-failed application.service
> systemctl list-dependencies sshd.service          # see a unit's dependency tree
> systemctl show sshd.service                       # To see the low-level properties of a unit
# 'mask' a unit to prevent the X service from being started, automatically or manually, for as long as it is masked.
> sudo systemctl mask nginx.service
> sudo systemctl unmask nginx.service               # Undo 'mask'ing a unit
> systemctl list-unit-files                         # to show also masked services!!!
> timedatectl                                       # !!! timezone info


# Other ways to do services management:

	sudo /etc/init.d/apache stop

	sudo service httpd start
	sudo service mysqld start
	sudo service mysqld restart
	sudo service memcached restart && service session_cache restart

	sudo service mysql status
	sudo service mysql start

       # set service to autostart
	sudo chkconfig httpd on
	sudo chkconfig mysqld on

       # list autostart services
	sudo chkconfig --list | grep mysqld

# services running check:

	service httpd status
	service apache2 status
	service mysql status
	ps aux | grep apache
        # check all running servers on machine!!!
	sudo netstat -tulpen

# task management:

	top

		# press k (kill) PID (enter twice)
		# press h (help)

# kill all processes of a program

	# with one command!!!
	killall slack
	# or
	ps aux | grep firefox | awk '{ print $2 }' | xargs kill -9

        # show pids of program's processes
        pidof slack
	sudo kill -SIGTERM `pidof slack`


# kill process by pid:

	ps aux | grep java	# find process by name. Alternatively use : pidof java
	kill -SIGTERM <pid>	# kill sending termination signal to the process (the safest way).
        kill -9 <pid>           # if above kill and anything else fails!!!

# EXAMPLE: stopping a misbehaving memchache to restart memcache. Session_cache below also runs in Memcache.
[root@igcdemo current]> service --status-all | grep memcach
memcached (pid  3237) is running...
memcached dead but pid file exists

[root@igcdemo ~]> ls -al /var/run/memcached/
total 12
drwxr-xr-x   2 memcached memcached 4096 2018-05-30 14:16 .
drwxr-xr-x. 22 root      root      4096 2018-03-28 14:47 ..
-rw-r--r--   1 memcached memcached    5 2017-12-05 22:02 session_cache.pid

[root@igcdemo ~]> rm /var/run/memcached/session_cache.pid
rm: remove regular file `/var/run/memcached/session_cache.pid'? y

[root@igcdemo ~]> service --status-all | grep memcach
memcached is stopped
memcached is stopped
[root@igcdemo ~]> service session_cache start
Starting memcached:                                        [  OK  ]
[root@igcdemo ~]> service memcache start
memcache: unrecognized service
[root@igcdemo ~]> service memcached start
Starting memcached:                                        [  OK  ]
[root@igcdemo ~]> service --status-all | grep memcach
memcached (pid  5217) is running...
memcached (pid  5192) is running...
```
## Directories
```bash
# delete non empty directory:
rm -rf <dir_name>

# copy folder recursively, verbose, with all attributes:
cp -avr ./leaderboard /home/vagrant/

# zip folder and subfolders:
zip -r zip_filename.zip /path/to/folder


# show tree directory structure (install tree utility -> sudo apt install tree)
tree -d .

# Back to home dir:
cd
```

## Free disk space
```bash
# -h : human readable (1M = 1024Kb etc)
df -h

# find folder size (du summarizes disk usage of each FILE, recursively for directories,)
du -hs /path/to/directory
```


## Disks - partitions
```bash
# friendly listing
$ sudo lsblk -f

# device <-> mount point mapping !!!!
$ df -h --output=source,target

# alternative, not so human friendly
$ sudo fdisk -l

# list mounted filesystems
$ findmnt

# list all mounts
$ cat /proc/mounts
$ cat /proc/mounts | grep ext4  # or grep vfat (to display usb drives)
```

## Network
```bash
# show used ports
sudo lsof -i

# show processes using specific port
sudo lsof -i :3306

# get binary script from PID from lsof listing:
COMMAND     PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
firefox   22745  jdi   50u  IPv4 2778942      0t0  TCP 10.70.30.223:38378->40.100.174.2:https (ESTABLISHED)

$ls -l /proc/22745/exe
lrwxrwxrwx 1 jdi jdi 0 Νοέ   3 09:55 /proc/22745/exe -> /usr/lib/firefox/firefox

$ whatis firefox
firefox (1)          - a free and open source web browser from Mozilla

# report working directory of a process PID
$pwdx 22745

# connect to host + port via telnet
$telnet localhost 6899

# connect to host + port via Netcat
sudo apt-get install Netcat
nc -vz localhost 6899
```


## Redirections
```bash
$ ls -l a b >myfiles.ls 2>/dev/null

# Redirections: Make FD2 write to where FD1 is writing (FD copying: The connection to the stream used by FD y is copied to FD x.)
$ ls -l a b >myfiles.ls 2>&1            # CORRECT! Redirecting standard output and standard error.
$ ls -l a b &>myfiles.ls		# CORRECT! Shortcut of the above.
$ ls -l a b >myfiles.ls 2>myfiles.ls    # WRONG! WRONG! The streams end up mixed in the output file!!!! Use the above way.
$ ls -l a b 2>&1 >myfiles.ls		# ALSO WRONG! Copy FD2 to FD1 but FD1 hasn't been redirected (to file) yet so it is still the terminal output.

# File redirection: Make FD x write to / read from file.
$ echo Hello >~/world	# Default FD for writing is FD 1.
$ rm file 2>/dev/null
$ read line <file	# Default FD for reading is FD 0.

# Appending file redirection
$ echo World >>~/world	# Append also FD1 to file.
$ echo World &>>~/world	# Append also FD2 (error) to file.

# copy to clipboard (install xclip command first):
$ xclip -selection clipboard < ~/.ssh/id_rsa.pub   # copy contents of private key file to clipboard
$ php -i | xclip -selection clipboard              # send/pipe command output to clipboard!!
$ xclip -selection clipboard - o                   # paste to std output clipboard contents!!
```

## grep
```bash

# -r: recursive, --color=... use color highlight , use more for paging
grep -v --color=always "Edge::app()->getConfig" * | more

# -v FIRST exclude a pattern before selecting a pattern with LAST grep
grep -v -r "Edge::app()->i18n" * | grep --color=always  "Edge::app()->" | more

# -v exclude a series of patterns before last grep that selects remaining pattern
grep -v -r "Edge::app()->i18n" * | grep -v "Edge::app()->getConfig" | grep -v "Edge::app()->logger" | grep -v "Edge::app()->cache" | grep -v "Edge::app()->db" | grep -v "Edge::app()->user" | grep -v "Edge::app()->router" | grep -v "Edge::app()->request" | grep -v "Edge::app()->response" | grep -v "Edge::app()->session" |grep --color=always  "Edge::app()->" | more

# print only filenames for --include pattern files, excluding exclude-dir folder
grep -lr --include=*.js --exclude-dir=*node_modules* 'setTimeout('
```

## Find the location of an icon of a launcher in use
```bash
Most of the time, the icon will be chosen from your current icon theme, rather than being referred to as an absolute path.
(usr/share/applications)

Open Gedit
Drag the launcher into the Gedit window
Look for the Icon definition:

Icon=gnome-panel-launcher
You can then find the icon somewhere in /usr/share/icons, depending on your theme.

Other possible folders containing icons:
/usr/share/icons/     (typically contains pre-installed themes shared by all users)
/usr/share/pixmaps/
/usr/share/app-install/icons/
~/.local/share/icons
~/.local/icons
~/.icons/
./home/jdi/.local/share/applications/jetbrains-phpstorm.desktop
```

## apachectl commands - apache
```bash
# Show apache vhosts and runtime config !!!
apachectl -S

# show modules loaded
apachectl -M

# old apache installation
$ cd /etc/httpd
$ ls -al
drwxr-xr-x    4 root root  4096 2015-10-19 12:02 .
drwxr-xr-x. 100 root root 12288 2018-01-04 15:44 ..
drwxr-xr-x    2 root root  4096 2015-10-19 12:02 conf
drwxr-xr-x    2 root root  4096 2018-02-20 11:07 conf.d
lrwxrwxrwx    1 root root    19 2015-10-19 12:02 logs -> ../../var/log/httpd
lrwxrwxrwx    1 root root    29 2015-10-19 12:02 modules -> ../../usr/lib64/httpd/modules
lrwxrwxrwx    1 root root    19 2015-10-19 12:02 run -> ../../var/run/httpd

# /etc/httpd/conf folder contains httpd.conf file that states which other conf (vhost) files  the server imports. Generally they are under conf.d dir.
$ ls conf.d/
oddset.conf  php.conf  README  welcome.conf  # here we are interested for oddset.conf to see DocumentRoot, ServerName directives.
```

## redis useful
```bash
# flush from container
$ docker exec -it igc-redis bash
$ redis-cli -h redis -p 6379
> flushall
```

## Secure copy SCP (http://www.hypexr.org/linux_scp_help.php)
```bash

Copy the file "foobar.txt" from a remote host to the local host

    $ scp your_username@remotehost.edu:foobar.txt /some/local/directory

Copy the file "foobar.txt" from the local host to a remote host

    $ scp foobar.txt your_username@remotehost.edu:/some/remote/directory

Copy the directory "foo" from the local host to a remote host's directory "bar"

    $ scp -r foo your_username@remotehost.edu:/some/remote/directory/bar

Copy the file "foobar.txt" from remote host "rh1.edu" to remote host "rh2.edu"

    $ scp your_username@rh1.edu:/some/remote/directory/foobar.txt \
    your_username@rh2.edu:/some/remote/directory/
```

## Usefull Linux services
xinetd: https://www.cyberciti.biz/faq/linux-how-do-i-configure-xinetd-service/
xinetd example: http://sysbible.org/2008/12/04/having-haproxy-check-mysql-status-through-a-xinetd-script/

## nslookup

https://medium.com/@jgefroh/demystifying-dns-for-web-developers-ace5a3ae559f
https://dougrathbone.com/blog/2012/03/03/devops-dns-for-developers-ndash-now-therersquos-no-excuse-not-to-know

```bash
# <subdomain of subdomain>.<subdomain>.<domain>.<TLD>
# TLD: top level domain

# Simple case, 1 IP returned
$ nslookup translate.verint.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	translate.verint.com
Address: 23.185.0.4

# More IPs serve site content ie for load-balancing
# To resolve to 1 IP use either ping, curl, traceroute:
# ping cf8d-5-203-220-107.ngrok-free.app
# curl -v htts://cf8d-5-203-220-107.ngrok-free.app
# traceroute cf8d-5-203-220-107.ngrok-free.app
$ nslookup https://cf8d-5-203-220-107.ngrok-free.app
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 3.124.142.205
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 18.158.249.75
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 3.125.209.94
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 3.125.223.134
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 3.125.102.39
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 18.192.31.165
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 2a05:d014:21b:8e02::6e:2
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 2a05:d014:21b:8e00::6e:0
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 2a05:d014:21b:8e01::6e:4
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 2a05:d014:21b:8e01::6e:1
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 2a05:d014:21b:8e00::6e:3
Name:	https://cf8d-5-203-220-107.ngrok-free.app
Address: 2a05:d014:21b:8e02::6e:5

$ nslookup
> atcom.gr
... απάντηση εμφανίζοντας Α records
> set type=CNAME
> atcom.gr
... απάντηση εμφανίζοντας CNAME records
> set type=MX
> atcom.gr
... απάντηση εμφανίζοντας MX records

# How do I check what DNS server is authorative for my domain name?
> set type=NS
> atcom.gr
... απάντηση εμφανίζοντας NS records

# show current DNS server, by default localhost
> server

# change to Google DNS server to get an external view
> server 8.8.8.8
```


## vim

### Buffer mgmt

:ls : list buffers
:b <TAB> : switch between buffers
:b <part of filename><TAB> : switch to buffer
:bp(/bn) : switch to previous/next buffer
:bd[elete] - unload the current buffer and then close the current window

### Splits

:vsplit [path]: split window vertically opening given file in [path]
:vs [path]: Exactly equivalent with above
C - w v: Exactly equivalent with above
:split [path]: split window horizontally opening given file in [path]
:sp [path]: Exactly equivalent with above
C - w s: Exactly equivalent with above
C - w |: Expand window vertically
C - w _: Expand window horizontally
C - w =: Resize previously expanded window in either direction

### Move between windows

C - j / k / l / h: Move down, up, right, left between buffers/panes

### Search buffer wide:

*/# : search forward (*) / backwords (#) for the word currently under cursor

### Search line wide:

f(some char): find next (some char)
; :Move to next (some char) occurrence
, :Move to previous (some char) occurrence

### Move around / Motions:

h: Left
j: Down
k: Up
l: Right
0, ^ : start of line(0), first not blank char(^)
$, g_ : end of line ($), last none blank char(g_)
w : until the start of the next word, EXCLUDING its first character.
e : to the end of the current word, INCLUDING the last character.
$ : to the end of the line, INCLUDING the last character.
): end of current sentence
(: start of current sententce
}: end of current paragraph
{: start of current paragraph

C - u: Half page up
C - d: Page down
C - b: One page backwards (up)
C - f: One pade forwards (down)
C - e: Move content down while leaving cursor static
C - y: Move content up while leaving cursor static
H: Move to the top section of current page
M: Move to the middle section of current page
L: Move to the bottom section of current page
zt: Scroll page so that current line is at the top of the screen
zz: Scroll page so that current line is at the middle of the screen
zb: Scroll page so that current line is at the bottom of the screen

C - o: Goto (old) previous position in jumps list (:jumps command)
C - i: Goto next position in jumps list (:jumps command)
% : Type  %  to find a matching ),], or }
`. : Return to last editing position
g;  : Move backwards in your edit locations (:changes command)
g,  : Move forwards in your edit locations (:changes command)

Macro:
qq: start recording
q: quit macro
@q: playback

Editing:
:e <filepath> : edit the file in the current window
p: paste from yank register after cursor position.
P: paste from yank register before cursor position.
yy: yank current line (plus carriage return)
yw: yank word (from cursor position to end of current word)(and put contents to yank register ie "0).
yiw : yank whole current word (and put contents to yank register ie "0)
x : delete character under cursor (Normal mode)
dw: delete current word
d$: delete from cursor to end of line.
d + optional<number> + <motion> : motion can be w, e, $ to delete as directed by respective motion.
optional<number>dd : delete <number> line(s)
a/(A) : append text (a) after the cursor or (A) on the end of line (enters Insert mode)
rx: replace character under cursor with x or which other char you type in place of x.
R<various characters>: Opposite to r command which replaces a single character, R replaces
                       all characters you type.
ce: deletes the word and places you in Insert mode.
cc: DELETE the whole line and enter INSERT mode.
c + optional<number> + <motion> : motion can be w, e, $ to change as directed by respective motion.
*cgn : Multiple search - replace. * starts a search for the word under cursor, c changes, gn goto next match, do the replacement, hit <ESC>, then press `.` to change next occurence!
o/(0) : open a new line below(above) and enter insert mode
xp : toggle characters (x cuts char under cursor, p pastes char from def. register after current character).
ddp : toggle lines (dd cuts current line, p pastes line from def. register after current line).
yyp : duplicate line (yy copies current line to def. register, p pastes from def. register after current line).

Undo:
U: undo changes in the whole line (like pressing u many times)
C - R : re-do previous undos

Substitute command:
:#,#s/old/new/g :  where #,# are the line numbers of the range of lines where the substitution is to be done. Note that in visual mode (having selected a range) automatically populates the range expression after you press : to start writing the substitution command!
:%s/old/new/g   :  to change every occurrence in the whole file.
:%s/old/new/gc  :  to find every occurrence in the whole file,
                   with a prompt whether to substitute or not.


Select all text in a window:
1. Goto start of text: gg
2. Enter visual mode:  v
3. Goto end of text:   G
4. Do action: d (delete), y (yank)

Show lines/columns indication in status-bar:
:set ruler

Wrap /Nowrap lines:
;set wrap(/nowrap)


## tmux key bindings / commands
```bash
## key bindings
C + b, PageUp: Scroll mode (use arrows up/down, page up/down to scroll cli output)
Esc: exit scroll mode

C + b, ?: Help

# window - panes
C + b, w:  Show windows list
C + b, ,:  Rename current window
C + b, c:  Create new window
C + b, 0-9: Switch between windows (You can see all created windows in the bottom list of Tmux window)
C + b, arrow key: Switch between panes (ctrl + b + arrow keys)
C + b, %: split pane horizontally
C + b, ": split pane verticaly
C + b, z: toggle on/off maximize pane !!

# keyboard Copy + Paste (https://www.rockyourcode.com/copy-and-paste-in-tmux/):
1. Enter copy mode: C + b [
2. Use the arrow keys to go to the position from where you want to start copying. Press C + SPACE to start copying.
3. Use arrow keys to go to the end of text you want to copy. Press ALT + w or C + w to copy into Tmux buffer.
4. Press C + b, ] to paste in a possibly different Tmux pane/window.

# searh text on a pane (https://superuser.com/questions/231002/how-can-i-search-within-the-output-buffer-of-a-tmux-shell):
C + b [, C + s, type string to find, Enter, n (find next occcurence)

# find window (based on text displayed on it):
C + b f, type text to find, Enter (might display list of results)

## tmux commands
# Start tmux
tmux

# Start saved session
tmux attach

# Display sessions
tmux ls
# Pick whatever session you want to re-attach. Then do ... to re-attach it to a new tmux instance and release it from the old one.
tmux attach -d -t <session id>

# Start a new shared session
# for more details -> https://www.howtoforge.com/sharing-terminal-sessions-with-tmux-and-screen
tmux -S /tmp/shared_session
chmod 777 /tmp/shared_session #Anyone can now join your session
# Attach to a shared session
tmux -S /tmp/shared_session attach
```

## Resolving Disk Space Issues on `/boot` in Ubuntu 22.04.5 LTS

**Problem:**
You encountered a disk space issue on the `/boot` partition while trying to apply software updates. The system reported that it needs more space, and suggested removing old kernels using `sudo apt autoremove`, but no old kernels were removed.

### Steps Taken:

#### 1. Check Installed Kernels:
You ran `dpkg --list | grep linux-image` to list the installed kernels. Based on the output, the following kernels were identified:
- **Current Kernel**: `linux-image-6.8.0-49-generic` (likely in use)
- **Older Kernels**: `linux-image-6.8.0-48-generic`, `linux-image-6.8.0-47-generic`, `linux-image-5.15.0-128-generic`

#### 2. Identify Active Kernel:
You confirmed the active kernel with `uname -r`, which helped ensure that you don't remove the currently running kernel.

#### 3. Remove Old Kernels:
You manually removed the old kernels (excluding the one currently in use) with commands like:
```bash
sudo apt-get remove --purge linux-image-6.8.0-48-generic
sudo apt-get remove --purge linux-image-6.8.0-47-generic
sudo apt-get remove --purge linux-image-5.15.0-128-generic
```

#### 4. Clean Up Residual Packages:
After removing the old kernels, you ran:

```bash
sudo apt autoremove
```
to clean up unnecessary dependencies.

#### 5. Regenerate initramfs:
Optionally, you updated the initramfs to ensure everything was up-to-date:

```bash
sudo update-initramfs -u
```

#### 6. Check Available Space:
After cleaning up, you checked the available space on /boot using:

```bash
df -h /boot
```
to confirm that sufficient space was freed.

#### 7. (optional) Configuration Review:
You noted that the COMPRESS=zstd setting in /etc/initramfs-tools/initramfs.conf was already set, which is optimal for both compression efficiency and speed. This setting is sufficient and did not need to be changed to xz.