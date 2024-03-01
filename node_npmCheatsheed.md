# node - npm Cheatsheet

## LINKS  
[include in project js libraries that don't use the module pattern](https://medium.com/@stefanledin/webpack-2-jquery-plugins-and-imports-loader-e0d984650058)

## Basic usage
```bash
npm init   # init project in current dir (creates package.json)  

# install jquery in ./node_modules dir and save (--save) it as a dependency in package.json.  
npm install jquery --save  

# uninstall raw-loader as a dependency in package.json.  
npm uninstall raw-loader --save  

# install package globally.  
npm install -g webpack  

# run webpack on the folder where webpack.config.js is.  
webpack 

#  will watch your files and re-execute webpack whenever any of the files Webpack is concerned about changes.  
webpack -w  

# ship to production, and that will run through the normal transformations as well as minify your code.  
webpack -p   
```  

## Local node installation done via NVM!!!!

nvm: node version manager

usage instructions:  
https://github.com/creationix/nvm

Links:
https://github.com/creationix/nvm
https://davidwalsh.name/nvm

```bash
# TO USE NODE execute:
$ nvm use node

#list installed versions
$ nvm ls

# install latest node lts version
$ nvm install --lts

# uninstall previous version
$ nvm uninstall v8.9.4

# path of nvm installation
$ echo $NVM_DIR

$sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

...

=> Appending nvm source string to /home/jdi/.bashrc
=> Appending bash_completion source string to /home/jdi/.bashrc
=> You currently have modules installed globally with `npm`. These will no
=> longer be linked to the active version of Node when you install a new node
=> with `nvm`; and they may (depending on how you construct your `$PATH`)
=> override the binaries of modules installed with `nvm`:

/usr/lib
├── create-react-app@1.4.1
├── electron-packager@9.1.0
└── webpack@3.8.1
=> If you wish to uninstall them at a later point (or re-install them under your
=> `nvm` Nodes), you can remove them from the system Node as follows:

     $ nvm use system
     $ npm uninstall -g a_module

=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

$nvm ls                   # list what versions of Node.js are installed on your machin
$nvm install 8.9.4        # install and switch to new version
$nvm use <some version>   # switch version
```  
