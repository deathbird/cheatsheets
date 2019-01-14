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


