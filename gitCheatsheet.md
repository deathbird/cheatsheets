# Git Cheatsheet

## Useful Links
https://en.wikipedia.org/wiki/Version_control#Common_vocabulary  
http://www.sitepoint.com/git-for-beginners/  
http://www.sitepoint.com/getting-started-git-team-environment/  
http://www.sitepoint.com/10-tips-git-next-level/  
http://www.sitepoint.com/using-git-open-source-projects/  
http://nvie.com/posts/a-successful-git-branching-model/  
http://www.sitepoint.com/understanding-version-control-diffs/  
http://www.sitepoint.com/whats-new-git-2-0  
https://try.github.io/levels/1/challenges/1  

## Find file - text

show in which commits a file changed  
`git log --follow Models/Widget/Content/Content.php`

shows the changes of a file, work even if the file was deleted !!!!  
`git log -- [file path]`

show all files commited on a specific commit  
`git diff-tree --no-commit-id --name-only -r bd61ad98`

Search commit log across all branches for given text  
`git log --all --grep='<given-text>'  
`  

## push
```bash
git push -f origin STAGING  
git push origin r-3.0.0/develop:r-3.0.0/develop  
## push -v (verbose), --tags (push tags), -c (config options)
git -c diff.mnemonicprefix=false -c core.quotepath=false push -v --tags origin r-3.0.0/develop:r-3.0.0/develop  
```  

## checkout
```bash
git checkout -b r-2.0.0/qa --track origin/r-2.0.0/qa
```
## submodules
```bash
git submodule update --init --recursive   #(That worked!!!!)  
git submodule update --recursive  
git submodule update --recursive --remote  #(updating to latest tips of all remote branches)  

# checkout with submodules  
# checkout a branch (here master) plus submodules  
git clone --recursive ssh://git@stash.intralot.com:7999/igcprojects/moroccodesktop.git  

# checkout a remote branch and then it's submodules  
$ git checkout -b mobileApp --track origin/mobileApp  
Branch mobileApp set up to track remote branch mobileApp from origin.   
Switched to a new branch 'mobileApp'  
$ git pull  
$ git submodule update  
Submodule path 'Edge': checked out 'cbdb02f1aed1bd3828db17c91c0d08c369045202'  
Submodule path 'Sportsbook': checked out 'ad330075757f1886976efcd601555726a7c403d3'  

# clone latest version of project WITH submodules  
~/projects/www$ git clone --recursive ssh://git@stash.intralot.com:7999/igcprojects/oddsetmobile.git  
...
~/projects/www$ cd oddsetmobile/  
~/projects/www/oddsetmobile$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

~/projects/www$ cd oddsetmobile/
~/projects/www/oddsetmobile$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

~/projects/www/oddsetmobile$ git submodule update --recursive --remote
Submodule path 'Cms': checked out '554ab914293239f2ba45086cec2e585de08d8a2c'
Submodule path 'Edge': checked out 'a630946a9a52af3386f9839cffebf403e7742a19'
```  

## log
```bash  
git log --graph --all (--oneline)  
git log --graph --all --oneline --decorate=full # see log graph with branches / tags etc.  
git log --oneline --decorate  # one line output with all references (branches, tags, etc)  
```  

## config  
```bash
# dont output crlf warnings  
git config --global core.autocrlf true   

# what file global config is saved in  
git config --global --edit
```  

## UNDO!!!!

```bash
# Usage with path specifier (dot matches all files)   
# https://stackoverflow.com/questions/14460595/git-checkout-with-dot  
# If you want to revert changes made to the index (i.e., that you have added). It undoes unstaged local modification.  
git checkout .   

# To undo all staged (added) modifications, use git reset
git reset  

# undo last commit: rewind your current HEAD branch return to the one before the current revision
# After running the command, you'll find the changes as uncommitted local modifications in your working copy.
git reset --soft HEAD~1  

# If you don't want to keep these changes, simply use the --hard flag
git reset --hard HEAD~1

# DELETE LAST LOCAL COMMIT:
git reset HEAD~  ή git reset HEAD^ --hard

# delete last 3 local commits
git reset --hard HEAD~3

# DELETE LAST LOCAL + REMOTE COMMIT:
git reset HEAD^ --hard  
git push origin -f  

# If you want to revert a change that you have committed  
git revert ...  

# ABORT MERGE!!!!!  
git merge --abort  
```  

## diff (show differences)
```bash
# show all commited files differences  
git show <hash>  

## (View differences:)
git diff <file>

## (View differences of SPECIFIC COMMIT:)
git diff COMMIT_HASH^ COMMIT_HASH

## (View differences of certain file between commits)
git diff HEAD^^ HEAD main.c
```  

## FAQ - Various

* Show all commits after a given commit and HEAD:  
`git rev-list 090cb257b5b..HEAD`

* make-the-current-commit-the-only-initial-commit-in-a-git-repository:  
http://stackoverflow.com/questions/9683279/make-the-current-commit-the-only-initial-commit-in-a-git-repository

    Here's the brute-force approach. It also removes the configuration of the repository:  
    http://stackoverflow.com/questions/1257592/how-do-i-remove-files-saying-old-mode-100755-new-mode-100644-from-unstaged-cha

    Note: This does NOT work if the repository has submodules! If you are using submodules, you should use e.g. interactive rebase

    Step 1: remove all history  
    `rm -rf .git`

    Step 2: reconstruct the Git repo with only the current content  
    `
    git init
    git add .
    git commit -m "Initial commit"
    `

    Step 3: push to GitHub.  
    `
    git remote add origin <github-uri>    # add a remote repo
    git push -u --force origin master
    `

* How do I make Git ignore file mode (chmod) changes? (I have a project in which I have to change the mode of files with chmod to 777 while developing, but which should not change in the main repo.)  
`git config core.filemode false`

