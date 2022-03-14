# Git Cheatsheet

## Useful Links
https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet  
http://ndpsoftware.com/git-cheatsheet.html   

https://en.wikipedia.org/wiki/Version_control#Common_vocabulary  
http://www.sitepoint.com/git-for-beginners/  
http://www.sitepoint.com/getting-started-git-team-environment/  
http://www.sitepoint.com/10-tips-git-next-level/  
http://www.sitepoint.com/using-git-open-source-projects/  
http://nvie.com/posts/a-successful-git-branching-model/  
http://www.sitepoint.com/understanding-version-control-diffs/  
http://www.sitepoint.com/whats-new-git-2-0  
https://try.github.io/levels/1/challenges/1  

http://marklodato.github.io/visual-git-guide/index-en.html  
https://github.com/git-tips/tips#everyday-git-in-twenty-commands-or-so  
https://learngitbranching.js.org/  
https://www.atlassian.com/git/tutorials/refs-and-the-reflog
https://www.ocpsoft.org/tutorials/git/use-reflog-and-cherry-pick-to-restore-lost-commits/


## Find file - text - search log

show in which commits a file changed  
`git log --follow Models/Widget/Content/Content.php`

shows the changes of a file, work even if the file was deleted !!!!  
`git log -- [file path]`

show all files commited on a specific commit  
`git diff-tree --no-commit-id --name-only -r bd61ad98`

Search commit log across all branches for given text (in commit messages).
`git log --all --grep='<given-text>'

Search commit log diffs after given date, for given text in diffs.
`git log -p -S 'migrations.Migration' --since='Nov 24 2021' `

List only (N=)15 commits in log
`git log -n 15`

```
# find all commits for given author (name is case sensitive), match is with like operator
# Add --all if you intend to search all branches and not just the current commit's ancestors in your repo.
git log --author=Dimas
```

## push
```bash
git push  # (sends your code to the current branch of origin remote. )

# (the code from your current active branch is pushed to the branch by the same name on the remote)
git push [remote]

git push [remote] [branch]

# overwrite remote branch with local
# (you can push from a local branch to a branch by a different name on the remote)
git push [remote] [local_branch]:[remote_branch]

# (If you supply an empty [local_branch], it will delete the remote_branch on the server)
git push [remote] :[remote_branch]

git push -f origin STAGING  
git push origin r-3.0.0/develop:r-3.0.0/develop  
## push -v (verbose), --tags (push tags), -c (config options)
git -c diff.mnemonicprefix=false -c core.quotepath=false push -v --tags origin r-3.0.0/develop:r-3.0.0/develop  
```  

## checkout
```bash
git fetch -v : # update local branch list from remote 	

git checkout -b r-2.0.0/qa --track origin/r-2.0.0/qa
```

## branches
```bash
git branch -a   # (show all local and remote brances!)  
git branch -r   # (show ONLY remote brances!)  
git branch -u origin/r-3.0.0/feature/IGC-479  # (set current local branch to track remote - upstream branch, -u: upstream)
git branch --unset-upstream # to remove the tracking to the remote branch
# Simply delete your remote tracking branch (this will not delete the branch on the remote repo!):
git branch -d -r origin/<remote branch name> 
# display local to remote (upstream) branch mapping!
git branch -vv   # doubly verbose!


# (To create a new branch from current HEAD and switch to it immediately, use:)  

# new branch points to current branch HEAD  
git checkout -b [new_branch_name]  

# Go back in time: new branch points to a previous commit and NOT the current branch HEAD  
git checkout -b [new_branch_name]  [hash of previous commit from current branch]  

git fetch [remote]		# (Fetch all of the branches from the repository)
# (create a local branch and checkout a remote branch to it. The new branch is tracked - git push works!)  
git checkout --track [remote]/[a_remote_branch]   
# push local branch and track at the same time (-u is short for --set-upstream).  
3. git push -u origin <branch>  

# (create a branch based on an old commit append the hash that identifies a commit with the command.)  
git checkout -b [branch_name] [commit_hash]  

# replace / overwrite local branch with remote version of branch
git reset --hard [remote]/[branch_name]

git push [remote] --delete [branch_name]  # delete a branch from a remote  
git push [remote] :[branch_name]		  # delete a branch from a remote  
git branch -D [branch_name]               # delete local branch irrespective of its merged status  
git remote prune [remote]				  # prune stale (deleted but not garbage collected?) branches from [remote]

# Renaming <old_name> Git Branch to <new_name>
# Checkout old branch:
git checkout <old_name>
# Rename the local branch by typing:
git branch -m <new_name>
#If you’ve already pushed the <old_name> branch to the remote repository delete the <old_name> remote branch with new_name:
git push origin :<old_name> <new_name>
#Finally push the <new_name> local branch and reset the upstream branch
git push origin -u <new_name>

# overwrite remote branch with local
git push [remote] [local_branch]:[remote_branch]
# Overwrite branch contents (force) from other branch and push (overwrite hotfixes with master)
git push origin +master:hotfixes

# Rebasing on another branch
# Will rebase a feature on devel branch:
# --preserve-merges preserves the merge commits!!!
# A_B_C_D_E      <-(devel)
#        \_F_G_H <-(feature)
# After rebase:
# A_B_C_D_E_F_G_H  <-(feature)
git checkout devel
git pull
git checkout feature
git rebase --preserve-merges devel

# alternatively, if not in feature branch the following checks out
# automatically feature branch to do a rebase on it.
git rebase devel feature
# to rebase over a remote <origin> <remote_ref: branch, tag, etc> :
git pull --rebase <origin> <remote_ref>

# Rebasing on another branch with --onto
# Will rebase a branch2 on devel branch:
# A_B_C_D_E           <-(devel)
#        \_F_G_H      <-(branch1)
#             \_K_L_M <-(branch2)
# After rebase:
# A_B_C_D_E           <-(devel)
#        \ \_K_L_M    <-(branch2)
#         \_F_G_H     <-(branch1)
git rebase --onto devel branch1 branch2
``` 

## merge
```bash
# (checkout master and merge the desired branch to it)
git checkout master 
git merge [branch_name]

# (picking a single commit from a different branch and merging it with your current one)
git cherry-pick [commit_hash]

# picking a range of commits from a different branch and merging it with your current one
# Postfix with ^ the starting commit to include it
git cherry-pick [start commit_hash]^..[end commit_hash]
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

# pretty print tree of branches:
git log --graph --decorate --oneline  
git log --graph --pretty=oneline --abbrev-commit  
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
git reset --hard # removes staged and working directory changes

## !! be very careful with these !!
## you may end up deleting what you don't want to
## read comments and manual.
git clean -f -d # remove untracked
git clean -f -x -d # CAUTION: as above but removes ignored files like config.
# CAUTION: as above, but cleans untracked and ignored files through the entire 
# repo (without :/, the operation affects only the current directory)
git clean -fxd :/ 

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

# TURN BACK TIME!!!!! Use reflog to inspect all past history and turn back to a point in history ;)
git reflog
git reset --hard HEAD@{5}

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

https://help.github.com/en/articles/changing-a-commit-message
#(amend last commit comment BEFORE PUSHING)  
git commit --amend  

#(amend last commit message comment BEFORE PUSHING)  
git commit --amend -m "New commit message for previous commit."  

# amend/add files to a freshly pushed commit (eg noone has pulled the commit or pushed as a Pull Request)
git commit --amend --no-edit # to amend locally without editing commit message (no-edit)
git push --force    # to force pushing to remote

# remove unwanted file(s) from git commit:
# moving the mistakenly committed files back to the staging area from the previous commit, 
# without cancelling the changes done to them.
# 1.
git reset --soft HEAD^ 
# 1. (alternative)
git reset --soft HEAD~1
# 2. Then reset the unwanted files in order to leave them out from the commit:
git reset HEAD path/to/unwanted_file
# 3. Now commit again, you can even re-use the same commit message:
git commit -c ORIG_HEAD 

# Amending the message of older or multiple commit messages
git rebase -i HEAD~3 # Displays a list of the last 3 commits on the current branch
# Replace pick with reword before each commit message you want to change and then push ...
git push --force

# return back origin to previous commit 
# -------------------------------------
git reset --hard f0fcac15ae4ed32fa3038ab2a7f1fdf91dc9e8e7  # 1. reset local branch to a previous good commit
git push [remote] :[branch_name]		  # 2. delete the same branch from the remote
git push origin STAGING					  # 3. push again corrected branch to remote!!!

# master branch has new direct contributions (not through devel branch)
# so the 2 branches have diverged and we need to refresh devel branch 
# with what's in master. Let's say last master branch commit is <hash_master_br>
git checkout devel
git update-ref HEAD <hash_master_br>
git push -f

```  

## remove / delete files
```bash
# (Remove from git no file / folder delete):
git rm --cached [file_name]  
git rm -r --cached [folder_name]  

# (Remove from git and from file system)  
git rm [file_name]  
```  

## diff (show differences)
```bash
# show all commited files differences  
git show <hash>  

## (View differences in working area)
git diff <file>

## (View differences in staging area)
git diff --cached
git diff --staged

## (View differences of SPECIFIC COMMIT:)
git diff COMMIT_HASH^ COMMIT_HASH

## (View differences of certain file between commits)
git diff HEAD^^ HEAD main.c

git diff [branch_name1]..[branch_name2]  
``` 

## repos
```bash
# (origin: a remote repo you have write access)
# (add a remote as origin:)
git remote add origin https://github.com/sdaityari/my_git_project.git

git remote -v #(verbose, show remote repo)

git remote show [remote-name]  # (eg. git remote show origin)

git remote remove myOrigin     # remove myOrigin remote

# add a remote
git remote add origin ssh://git@example.com:1234/myRepo.git  
```

## stashing
```bash
# (Stash essentially takes all your changes and stores them for further use)  
git stash save 'helpfull message about the stashed version'  

git stash list  

git stash pop   # apply the last stash and remove it from stash list  

git stash apply # apply the last stash and KEEP it in stash list  
git stash apply stash@{2}

#To show files changed in the last stash
git stash show

#To view the content of the most recent stash, run
git stash show -p

#To view the content of an arbitrary stash, run something like
git stash show -p stash@{1}

#Since git 2.13, there is a command to save a specific path to the stash:
git stash push -m stash_description path/to/file.json
```

## rebase
```bash
# use --preserve-merges to preserve the merge commits
# it’s best to use when having a base feature “temp” with multiple feature branches, 
# as it preserves the merge commits
# https://stackoverflow.com/questions/15915430/what-exactly-does-gits-rebase-preserve-merges-do-and-why#:~:text=As%20with%20a%20normal%20git,replaying%20works%20for%20merge%20commits.
# For example, take commit graph where m is a merge commit with parents E and G.

#   B---C <-- master
#  /                     
# A-------D------E----m----H <-- topic
#          \         /
#           F-------G

# Suppose we rebased topic (H) on top of master (C) using a normal, non-merge-preserving rebase:
# (For example, checkout topic; rebase master.) In that case, git would select the following commits for replay:

# pick D
# pick E
# pick F
# pick G
# pick H
# and then update the commit graph like so:
# Note that merge commit m is not selected for replay.
#   B---C <-- master
#  /     \                
# A       D'---E'---F'---G'---H' <-- topic

# If we instead did a --preserve-merges rebase of H on top of C. (For example, checkout topic; rebase --preserve-merges master.) 
# In this new case, git would select the following commits for replay:

# pick D
# pick E
# pick F (onto D' in the 'subtopic' branch)
# pick G (onto F' in the 'subtopic' branch)
# pick Merge branch 'subtopic' into topic
# pick H
# Now m was chosen for replay. Also note that merge parents E and G were picked for inclusion before merge commit m.
# Here is the resulting commit graph:
#  B---C <-- master
# /     \                
# A      D'-----E'----m'----H' <-- topic
#         \          / 
#          F'-------G'

# (squash number_of_commits into one)
# https://stackoverflow.com/questions/5189560/squash-my-last-x-commits-together-using-git
git rebase -i HEAD~[number_of_commits]

# Rebase with squash  
# -----------------------------------  
git rebase -i HEAD~n    # n : number of commits

# if it breaks you write :
git rebase --abort

# if there are conflicts you amend them and continue the rebase  
git rebase --continue  

# Rebase for fast-forward  
# -----------------------------------
# https://git-scm.com/book/en/v2/Git-Branching-Rebasing  

$ git checkout experiment_branch  
$ git rebase master  
First, rewinding head to replay your work on top of it...  
Applying: added staged command  
$ git checkout master  
$ git merge experiment  

```

* Change previous commit(s) adding files or commit. It's done with an interactive rebase:
https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History
```bash
# stash changes
➜ git stash
# get back to the parent of the commit you want to edit, eg go back 2 commits
➜ git rebase -i HEAD~2
# you are dropped in VI, choose EDIT (edit) to the commits you want to modify!!!
# then save the file
Stopped at 23d33cd...  Fix synching finds more translation files
You can amend the commit now, with

  git commit --amend '-S'

Once you are satisfied with your changes, run

  git rebase --continue


# Add / Change files from the commit here 
➜ git stash pop
# add files to staging area and commit
➜ git commit --amend '-S'
# then continue the rebase 
➜ Git Rebase --continue
# finally force push to PR
➜ git push -f
```

* Splitting commits (https://embeddedartistry.com/blog/2018/2/19/code-cleanup-splitting-up-git-commits-in-the-middle-of-a-branch):
Here's how I approach splitting up a git commit buried in the middle of a branch:

1. Checkout the branch that you want to modify (e.g. pj/feature)
2. Start an interactive rebase which includes your commit.
   At a minimum, ```git rebase -i commit^``` will start the rebase at the commit you want to split
   You can also rebase the whole branch, which I usually do to split all target commits in one go
3. Mark the commit(s) that you want to split with edit
4. When git presents the commit that you want to split:
   Reset state to the previous commit using ```git reset HEAD^```
5. Use git add to carefully and incrementally add changes to the index
6. Run git commit when you have a set of atomic changes that you are ready to commit
7. Repeat the git add and git commit process until you have processed each set of changes represented by the commit
8. Run ```git rebase --continue``` to resume or finish the rebase

## tags
```bash
git tag  # show all tags

git log --decorate=full  # show log with tags info

git tag -d v1.2.001 # delete lightweight(?) tag

# create lightweight tag for a given commit
git tag v1.2.001 090cb257b5b
git tag 3.1.0006 48d0b269a

git show v1.2.001  # show the commit of a tag

git push origin 3.1.0006  # push tag to repo

git push --delete origin 3.1.0017  # delete remote tag

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
```  

## FAQ - Various

* Show all commits after a given commit and HEAD:  
`git rev-list 090cb257b5b..HEAD`

* Retrieve lost commit message. The commit message is stored in .git/COMMIT_EDITMSG. After a "failed" committing attempt, you could run:

```bash
git commit --edit --file=.git/COMMIT_EDITMSG
# shorter syntax
git commit -eF .git/COMMIT_EDITMSG
```

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

* create a patch
`git diff [branch1] [branch2] > [file_name]`

* apply patch
`git apply [file_name]`

