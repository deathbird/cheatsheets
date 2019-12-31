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

https://ss64.com/bash/syntax-keyboard.html 
https://clementc.github.io/blog/2018/01/25/moving_cli/
In the following C - <keyboard key> means pressing Ctrl key And <keyboard key> together.
	
C - d : exit  
C - l : clear screen above current line  
C - [ : Equivalent to ESC key.  
C - V : Makes the next character typed verbatim  
C - X : Lists the possible filename completions of the current word.  

Movement:  
C - a : goto start of line  
C - e : goto end of line  
C - f : Move forward one character  
C - B : To move backward one character  
C - XX : Move between start of command line and current cursor position (and back again).  

Editing:  
C - D : Delete one character backward  
C - H : Delete the characters before the cursor, same as BASKSPACE.  
C - K : Delete all characters after the cursor  
C - u : cut from cursor to START of line also clearing all the line if you are at line end  
C - w : cut from cursor to START of word also clearing all the line if you are at line end  
C - k : cut from cursor to END of line also clearing all the line if you are at line start  
C - y : paste  
C - T : Swaps the last two characters.  


History:  
C - r : search command history recursively  
C - o   Execute the command found via C - r or C - s  
C - g   Escape from history searching mode  
C - N : Display next line in command history.  
C - P : Displays the previous line in command history.  
C - S : Searches the history forward.  

Commands:  
C - C : Stop the currently running command  
C - J / C - M : Same as ENTER/RETURN key.  
C - Z : Stops the current command.  

Use !! to prefix to the last command:  
sudo !!  

!:n selects ONLY the nth argument of the last command  
!$ selects the last argument of the last command. 

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

#You should use "double quotes" for any argument that contains expansions (such as $variable or $(command) expansions) 
# and 'single quotes' for any other arguments. Single quotes make sure that everything in the quotes remains literal.

# Follow log:
tail -100f /path/to/file | grep the_string_to_search_for

# show users
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

# unzip *.tar.gz  
$ tar -xzf file1.tar.gz  

# unzip *.gz  
$ gunzip file1.gz  

# Display file or file system status, timestamps (Access, Modify, Change)  
# https://unix.stackexchange.com/questions/2464/timestamp-modification-time-and-created-time-of-a-file  
$ stat <filename>  

# Locate the binary file  
$ which apache2  

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

Move around:  
H: Left  
J: Down  
K: Up  
L: Right  
w: start of next word  
e: end-of current word  
): end of current sentence  
(: start of current sententce  
}: end of current paragraph  
{: start of current paragraph  

Show lines/columns indication in status-bar:
:set ruler












