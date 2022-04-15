https://egghead.io/lessons/git-squash-commits-before-they-are-pushed-with-interactive-rebase

# this will fix last commit message that has not been pushed yet
git commit --amend -m "Adding index to whatever" 

# to see latest log message:
git log --oneline

# to unstage
git reset HEAD filename
git restore --staged filename
rm filename

# see what we are about to push
git diff origin/master HEAD

# we want to reset and come back to a specific commit or via HEAD minus 1
# need to be careful we can do this when we have not push yet!
git reset HEAD~1 
git reset 85da456

# help
git --help reset
# this is just moving back that commit into the staging area
git reset --soft HEAD~1
# this --mixed is the default for reset, this one removes the commit and unstaged those changes
git reset --mixed HEAD~1 
# this --hard will remove commit, unstage and remove it from the working directory
git reset --hard HEAD~1

# git reflog => what you have done in your local repo, only works if is not garbage collected
git reset --hard 86332122

# use git revert when something is alredy in upstream to have a clean history is someone pull is like a merge commit
# will open a editor to add a message etc
git revert f23481b

# create branch 2 ways:
git branch js-changes
git checkout -b js-changes

# list branches and if they are linked, example:
# * js-changes 9e14eee Revert "take 3"
#  master     9e14eee [origin/master] Revert "take 3"
# nice way to list branches:
git branch -vv


# push changes from local branch 2 ways:
git push --set-upstream origin js-changes
git push -u origin js-changes
# we will see now new branch is linked!
# * js-changes 11a001b [origin/js-changes] adding funtion to app.js
#  master     9e14eee [origin/master] Revert "take 3"

# git cherry-pick could be useful when picking up from other branchs to master
git cherry-pick 11a001b 

# when you accidently commit to master 
# do a cherry-pick from the branch you want to get the commit 
# get the commit hash from master
# all good
# remove commit from master via reset hash or git reset HEAD~1 for example
git stash list
git stash drop stash@{0}
# stash dont get deleted when pop if you are in a merge conflict


# this will save message : doing crazy things
git stash save doing crazy things


# when you do git checkout hash to explore a commit then you will be in a detached HEAD state
# 
# that is not good because if you do commit chnages will be lost so better do:
git checkout -b exploring-js-feature
# then do git branch -vv to see that we are in a a new branch locally
# you should see HEAD not pointing to a branch when you do: git log --oneline

# when doing this from a local branch this is to merge conflicts locally and sort it before pushing to the upstream
# this what I normally do with: git pull origin master 
# into my current branch so I sort out conflicts first locally
git merge master

git log --oneline --graph

# to see what branches are not needed:
git remote prune origin --dry-run
# this will actually remove the pointer
git remote prune origin

# and then you could see something like "gone":
#  js-changes           607c4f0 [origin/js-changes: gone] fixes merge conflict
# stilll you need to remnove branch locally
git branch -d name-of-branch

# I want to change commit message from the past
git rebase -i HEAD~3
# we have interactive rebase so we have "pick" as default but we can use "reword" for a speficiv commit message to change it.

# trick for removing file remotely when ignorig file in .gitignore
git rm -r --cached .

# lets say we want to add a file to a commit then we do rebase interactive
# keep same commit message because I'm adding a new file
git commit --amend --no-edit

# git rebase -i also allow you to also combine commits into 1 with squash option in the rebase -i 

# ####################################################### 
If we want to completely remove a file from github - including all history - there is a tool that we can use called the BFG.

The github help article is here:

https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository

And the BGF itself is available here:

https://rtyley.github.io/bfg-repo-cleaner/

We'll start by downloading the BFG jar file, and then cloning a mirror of our repo with:

git clone --mirror [repo-url]

Then we can delete our .env file with:

java -jar ~/Downloads/bfg-1.13.0.jar --delete-files .env my-repo.git

which will delete the .env file. Then we can use the following command to prune the entire history and garbage collect the remains:

git reflog expire --expire=now --all && git gc --prune=now --aggressive

And finally, use git push to push that change to github, and remove the .env file from all of the history on github as well.