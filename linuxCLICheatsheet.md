# Linux / Bash CLI Cheatsheet  

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

## Tools
* [ShellCheck - finds bugs in your shell scripts](https://www.shellcheck.net/)  

## BASH keyboard shortcuts

https://ss64.com/bash/syntax-keyboard.html 
https://clementc.github.io/blog/2018/01/25/moving_cli/

CTRL + A : goto start of line  
CTRL + E : goto end of line  
CTRL + L : clear screen above current line 
CTRL + R : search command history recursively  
Ctrl + o   Execute the command found via Ctrl+r or Ctrl+s  
Ctrl + g   Escape from history searching mode  
Ctrl + U : cut from cursor to START of line also clearing all the line if you are at line end  
Ctrl + W : cut from cursor to START of word also clearing all the line if you are at line end  
Ctrl + K : cut from cursor to END of line also clearing all the line if you are at line start  
Ctrl + Y : paste  

Use !! to prefix to the last command:  
sudo !!  

!:n selects ONLY the nth argument of the last command  
!$ selects the last argument of the last command. 

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





