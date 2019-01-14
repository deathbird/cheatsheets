https://en.wikipedia.org/wiki/Version_control#Common_vocabulary
http://www.sitepoint.com/git-for-beginners/
http://www.sitepoint.com/getting-started-git-team-environment/
http://www.sitepoint.com/10-tips-git-next-level/
http://www.sitepoint.com/using-git-open-source-projects/
http://nvie.com/posts/a-successful-git-branching-model/
http://www.sitepoint.com/understanding-version-control-diffs/
http://www.sitepoint.com/whats-new-git-2-0

https://try.github.io/levels/1/challenges/1

http://stackoverflow.com/questions/9683279/make-the-current-commit-the-only-initial-commit-in-a-git-repository
Here's the brute-force approach. It also removes the configuration of the repository.

http://stackoverflow.com/questions/1257592/how-do-i-remove-files-saying-old-mode-100755-new-mode-100644-from-unstaged-cha
git config core.filemode false

Note: This does NOT work if the repository has submodules! If you are using submodules, you should use e.g. interactive rebase

Step 1: remove all history

rm -rf .git
Step 2: reconstruct the Git repo with only the current content

git init
git add .
git commit -m "Initial commit"
Step 3: push to GitHub.

git remote add origin <github-uri>    # add a remote repo
git push -u --force origin master


########################################################
# git tricks
########################################################

# show in which commits a file changed
$ git log --follow Models/Widget/Content/Content.php

# shows the changes of a file, work even if the file was deleted !!!!
git log -- [file path]

# show all files commited on a specific commit
$ git diff-tree --no-commit-id --name-only -r bd61ad98

# Search Commit log across all branches for given text
git log --all --grep='<given-text>'
git log --graph --all (--oneline)
git log --graph --all --oneline --decorate=full # see log graph with branches / tags etc.
git log --oneline --decorate			# one line output with all references (branches, tags, etc)


git push -f origin STAGING

git push origin r-3.0.0/develop:r-3.0.0/develop

git -c diff.mnemonicprefix=false -c core.quotepath=false push -v --tags origin r-3.0.0/develop:r-3.0.0/develop

git checkout -b r-2.0.0/qa --track origin/r-2.0.0/qa

git submodule update --init --recursive   (That worked!!!!)
git submodule update --recursive
git submodule update --recursive --remote (updating to latest tips of all remote branches)

# checkout with submodules
# checkout a branch (here master) plus submodules
$git clone --recursive ssh://git@stash.intralot.com:7999/igcprojects/moroccodesktop.git  

# checkout a remote branch and then it's submodules
$git checkout -b mobileApp --track origin/mobileApp
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
jdi@jdi-LIFEBOOK-AH532-G21:~/projects/www/oddsetmobile$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

~/projects/www$ cd oddsetmobile/
jdi@jdi-LIFEBOOK-AH532-G21:~/projects/www/oddsetmobile$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

~/projects/www/oddsetmobile$ git submodule update --recursive --remote
Submodule path 'Cms': checked out '554ab914293239f2ba45086cec2e585de08d8a2c'
Submodule path 'Edge': checked out 'a630946a9a52af3386f9839cffebf403e7742a19'





CONFIG
--------------------------
# dont output crlf warnings
git config --global core.autocrlf true 

# what file global config is saved in
git config --global --edit

UNDO!!!!
---------------

git checkout .
If you want to revert changes made to the index (i.e., that you have added), do this:

git reset
If you want to revert a change that you have committed, do this:

git revert ...

# undo last commit: rewind your current HEAD branch return to the one before the current revision
# After running the command, you'll find the changes as uncommitted local modifications in your working copy.
git reset --soft HEAD~1  

# If you don't want to keep these changes, simply use the --hard flag
git reset --hard HEAD~1



ABORT MERGE!!!!!
git merge --abort 

DELETE LAST LOCAL COMMIT:
git reset HEAD~  ή git reset HEAD^ --hard

# delete last 3 local commits
git reset --hard HEAD~3

DELETE LAST LOCAL + REMOTE COMMIT:
git reset HEAD^ --hard
git push origin -f


git show <hash>

## (View differences:)
git diff <file>

## (View differences of SPECIFIC COMMIT:)
git diff COMMIT_HASH^ COMMIT_HASH

## (View differences of certain file between commits)
git diff HEAD^^ HEAD main.c

(Remove from git no file / folder delete):
git rm --cached [file_name]
git rm -r --cached [folder_name]

(Remove from git and from file system):
git rm [file_name]

git add myfile2 myfile3

(origin: a remote repo you have write access)

(add a remote origin:)(
git remote add origin https://github.com/sdaityari/my_git_project.git

(push your code to origin:)
git push -u origin master

#(amend last commit comment BEFORE PUSHING)
git commit --amend

#(amend last commit message comment BEFORE PUSHING)
git commit --amend -m "New commit message for previous commit."


BRANCHES
=======================
git remote -v (verbose, show remote repo)

git remote show [remote-name]   (eg. git remote show origin)

git branch -a   (show all local and remote brances!)
git branch -r   (show ONLY remote brances!)
git branch -u origin/r-3.0.0/feature/IGC-479  (current local branch track remote - upstream branch, -u: upstream)



(To create a new branch from current HEAD and switch to it immediately, use:)

# new branch points to current branch HEAD
git checkout -b [new_branch_name]

# Go back in time: new branch points to a previous commit and NOT the current branch HEAD
git checkout -b [new_branch_name]  [hash of previous commit from current branch]

1. git fetch [remote]		(Fetch all of the branches from the repository)
2. git checkout --track [remote]/[a_remote_branch] (create a local branch and checkout a remote branch to it. The new branch is tracked - git push works!)
# push local branch and track at the same time (-u is short for --set-upstream).
3. git push -u origin <branch>

(create a branch based on an old commit append the hash that identifies a commit with the command.)
git checkout -b [branch_name] [commit_hash]

git push [remote] --delete [branch_name]  # delete a branch from a remote
git push [remote] :[branch_name]		  # delete a branch from a remote
git branch -D [branch_name]               # delete local branch irrespective of its merged status
git remote prune [remote]				  # prune stale (deleted but not garbage collected?) branches from [remote]



git diff [branch_name1]..[branch_name2]
git diff [branch_name1]...[branch_name2]

(checkout master and merge the desired branch to it)
git checkout master 
git merge [branch_name]

(sends your code to the current branch of origin remote. )
git push 

(the code from your current active branch is pushed to the branch by the same name on the remote)
git push [remote]


git push [remote] [branch]

(you can push from a local branch to a branch by a different name on the remote)
git push [remote] [local_branch]:[remote_branch]

(If you supply an empty [local_branch], it will delete the remote_branch on the server)
git push [remote] :[remote_branch]


git pull --rebase [remote] [branch]

(create a patch)
git diff [branch1] [branch2] > [file_name]

(apply patch)
git apply [file_name]


(squash number_of_commits into one)
git rebase -i HEAD~[number_of_commits]

STASHING
=================

(Stash essentially takes all your changes and stores them for further use)
git stash save 'helpfull message about the stashed version'

git stash list

git stash pop   # apply the last stash and remove it from stash list

git stash apply # apply the last stash and KEEP it in stash list
git stash apply stash@{2}


(picking a single commit from a different branch and merging it with your current one)
git cherry-pick [commit_hash]



git fetch -v : ενημερώνει τοπική λίστα branch με το remote 	

pretty print tree of branches:
------------------------------
git log --graph --decorate --oneline
git log --graph --pretty=oneline --abbrev-commit

return back origin to previous commit 
-------------------------------------
git reset --hard f0fcac15ae4ed32fa3038ab2a7f1fdf91dc9e8e7  # 1. reset local branch to a previous good commit
git push [remote] :[branch_name]		  # 2. delete the same branch from the remote
git push origin STAGING					  # 3. push again corrected branch to remote!!!


Rebase with squash
-----------------------------------
git rebase -i HEAD~n    # n : number of commits

#if it breaks you write :
git rebase --abort

# if there are conflicts you amend them and continue the rebase
git rebase --continue

Rebase for fast-forward
-----------------------------------
https://git-scm.com/book/en/v2/Git-Branching-Rebasing

$ git checkout experiment_branch
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
$ git checkout master
$ git merge experiment


TAGS
--------------------------
# show all tags
git tag

# show log with tags info
git log --decorate=full

# delete lightweight(?) tag
git tag -d v1.2.001

# create lightweight tag for a given commit
git tag v1.2.001 090cb257b5b
git tag 3.1.0006 48d0b269a

# show the commit of a tag
git show v1.2.001

# push tag to repo
git push origin 3.1.0006

# delete remote tag
git push --delete origin 3.1.0017

# If you also need to delete the local tag, use:
git tag --delete 3.1.0017

##### bash script to assign tags ##########

indx=0
for commit in $(git rev-list 48e33402103..HEAD | tac)
do
        echo $commit
        # zero pad indx -> indxf variable
        printf -v indxf "%04d" $indx
        echo $indxf
        # tag commit
        git tag 2.0.$indxf $commit
        # increment indx
        indx=$((indx+1))
#        echo $indx
done



Various
--------------------------
# show all commits after a given commit and HEAD
git rev-list 090cb257b5b..HEAD
