# Dell XPS-13-9380 with Ubuntu 18.04 LTS installed s/w

Documenting software installations on an Dell XPS-13-9380 with Ubuntu 18.04 LTS.

For all s/w cloned from repos we use folder:  **~/cloned_software**

## Install Solarized theme in Gnome terminal

* Installed under ~/cloned_software

Follow https://github.com/aruhier/gnome-terminal-colors-solarized, 'Installation and usage' instructions. Also during install download and dircolors:

```The new dircolors have been installed as /home/jdi/.dir_colors/dircolors```

You can also run the set_dark.sh or set_light.sh script, to directly set the dark or light solarized theme to the actived gnome-terminal profile. 

## Install powerline for bash

```sudo apt-get install powerline
...
The following additional packages will be installed:
  fonts-powerline python-powerline python-psutil python3-powerline python3-psutil
Suggested packages:
  python-powerline-doc vim-addon-manager python-psutil-doc
The following NEW packages will be installed:
  fonts-powerline powerline python-powerline python-psutil python3-powerline python3-psutil
...
```

Also add in ~/.bashrc:
```
# powerline for bash
if [ -f /usr/share/powerline/bindings/bash/powerline.sh ]; then
  powerline-daemon -q
  POWERLINE_BASH_CONTINUATION=1
  POWERLINE_BASH_SELECT=1
  source /usr/share/powerline/bindings/bash/powerline.sh
fi
```
as described in powerline's doc (https://powerline.readthedocs.io/en/latest/usage/shell-prompts.html#bash-prompt).

To configure appearance: https://sources.debian.org/src/powerline/1.2-2/debian/README.Debian/

as described there:
```
The default configuration files for Powerline are located in
/usr/share/powerline/config_files

A user may customize Powerline by copying the contents of this directory into
~/.config/powerline and modifying the JSON-formatted configuration files.

Documentation for the various configuration options is found in the
python-powerline-doc package, at this URL:
file:///usr/share/doc/python-powerline-doc/html/configuration.html#main-configuration
```



