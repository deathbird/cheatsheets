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

