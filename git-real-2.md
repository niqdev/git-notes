# Git Real 2

> work in progress

Course index:
* [Interactive rebase](#interactive-rebase)
* [Stashing](#stashing)
* [Purging history](#purging-history)
* [Working together](#working-together)
* [Submodules](#submodules)

### Interactive rebase

Alter commit in the same branch:
```bash
# redo last 3 commits (3 commit before curret HEAD)
git rebase -i HEAD~3

# pick: move commits in a temporary area then rerun each commit one at a time
# interactive rebase alters every commit after the one specified

# there's no commit after HEAD: does nothing
git rebase -i HEAD

# go to the parent of HEAD: replay last commit
git rebase -i HEAD^
```

Reorder commits:
```bash
# show list of commits from newest to oldest
git logs --oneline

# 'pick' chooses a commit for replay
# interactive rebase opens a script in an editor
git rebase -i HEAD~3
# default script is in the original chronological order (from oldest to newest)
# simply swap the order, save and close the editor (:wq)
# so commits get replayed (picked) in the specified new order
```

Change messages:
```bash
# replay N commits
git rebase -i HEAD~N

# modify script in editor:
# change 'pick' to 'reword', then save and exit
# opens a new editor with existing message as the default
# then change message, save and exit
```

Split commits:
```bash
# replay N commits
git rebase -i HEAD~N

# modify script in editor:
# change 'pick' to 'edit', then save and exit
# 'edit' replays changes, but stop without committing

# undo changes that where just replayed (unstage all files)
git reset HEAD^

# stage and commit interested files multiple times
git add FILE_LIST
git commit -m "splitted commit"

# after new commits are in place, finish to rebase
# resume replaying changes from temporary area
git rebase --continue
```

Squash commits:
```bash
# replay N commits
git rebase -i HEAD~N

# modify script in editor:
# change 'pick' to 'squash', then save and exit
# 'squash' merges a commit with the previous commit
# another editor pops up, with the commits being squashed
```

### Stashing

Take same file and store away in a temporary area so that you can restore them at a later time
```bash
# save modified files and restore last commit
# push stash onto the stash-stack
git stash save

# bring stashed files back
git stash apply

# list all stashes (WIP = work in progress)
git stash list

# apply specific N stash (does not remove from stash-stack)
git stash apply stash@{N}

# discard a stash
git stash drop
```

Shortcuts:
```bash
# shortcut                  # same as
git stash           --->    git stash save
git stash apply     --->    git stash apply stash@{0}
git stash drop      --->    git stash drop stash@{0}
git stash pop       --->    git stash apply + git stash drop
git stash show      --->    git stash show stash@{0}
```

Conflicts:
```bash
# possible conflict
git stash apply

# commit or reset any local changes, as appropriate
git reset --hard HEAD
# then
git stash apply

# when conflict with 'pop', after merging conflict
git stash pop
# then stash won't be dropped automatically, so manually drop it
git stash drop
```

More options:
```bash
# when stashing, both staged and unstaged are stored/restored
# '--keep-index' option causes the staging area not to be stashed
git stash save --keep-index

# normally only tracked files are stashed
# '--include-untracked' option causes untracked files to be stashed, too
git stash save --include-untracked
git stash save -u

# 'git stash list' can take any option 'git log' can
git stash list --stat

# show diff of N stash
git stash show stash@{N}

# 'git stash show' also can take any option 'git log' can
# '--patch' shows file diff
git stash show --patch

# provide a stash message when saving
git stash save "COMMENT"

# clear all stashes at once
git stash clear
```

Branching:

If accidentally a branch with stash N is deleted you need a new branch to restore stashed changes
```bash
# checks a new branch out and drops the stash automatically
git stash branch BRANCH_NAME stash@{N}
# then the new branch is an ordinary branch, ready for commits
```

### Purging history

If you commit a file that you shouldn't (like passwords), even if you delete it, its content is still visible in history

There are commands in git that can rewrite history, but *with great power comes great responsability*
```bash
# backs up entire repo before lose work when rewriting history
git clone REPOSITORY_NAME REPOSITORY_NAME_BACKUP

# checks each commit out into working directory, runs COMMAND and re-commit
git filter-branch --tree-filter COMMAND

# NOTE: if the COMMAND fails for any reason, the filter will stop

# examples (COMMAND is any shell command)
# remove passwords.txt from project root
# '-f' option means that also if file is not present to do not fail
--tree-filter 'rm -f passwords.txt'
# remove video files from any directory
--tree-filter 'find . -name "*.mp4" -exec rm {} \;'

# '--all' option means filter all commits in all branches
git filter-branch --tree-filter 'rm -f passwords.txt' -- --all
# filter only current branch
git filter-branch --tree-filter 'rm -f passwords.txt' -- HEAD

# COMMAND must operate on staging area
# runs command against each commit, but without checking it out first (so it's faster)
git filter-branch --index-filter COMMAND

# this does NOT work: operates on working directory
--index-filter 'rm -f passwords.txt'
# this it works: operates on staging area
# '--ignore-unmatch' option succeeds even if file isn't present
--index-filter 'git rm --cached --ignore-unmatch passwords.txt'

# after running 'filter-branch' git leaves a backup of your tree in the '.git' directory
# so by default you can't run it again because it won't overwrite the backup
# '-f' option force to re-run
git filter-branch -f --tree-filter 'rm -f passwords.txt'

# filters may result in some empty commits
# '--prune-empty' option drops commits that don't alter any files
git filter-branch -f --prune-empty -- --all
# can prune during filtering too
git filter-branch --tree-filter 'rm -f passwords.txt' --prune-empty -- --all
```

### Working together

Handling line endings format in different OS: git can auto-correct it
```bash
# unix OS: \n
# windows OS \r\n

# on unix OS: changes CR/LF to LF on commit
# fixes any windows OS line endings that get introduced
git config --global core.autocrlf input

# on windows OS: changes LF to CR/LF on checkout
# but converts back to LF on commit
git config --global core.autocrlf true

# on windows OS ONLY projects: does no conversion
# conversion isn't needed if everyone expects CR/LF
git config --global core.autocrlf false
```

Git attribute files:
```bash
# edit .gitattributes files in project root: file types/conversion settings
# files tipe: *, *.txt, *.jpg, ...
# conversion settings:
# choose conversion automatically
text=auto
# treat files as text - convert to OS's line ending on checkout, back to LF on commit
text
# convert to specified format on checkout, back to LF on commit
text eol=crlf
text eol=lf
# treat file as binary - do no conversion
binary

# Rule examples:
# by default, auto-convert line endings
* text=auto
# treat HTML and CSS files as text
*.html text
*.css text
# treat image files as binary
*.jpg binary
*.png binary
# keep shell script in unix format
*.sh text eol=lf
# batch files in windows format
*.bat text eol=crlf
```

Cherry-pick: we need a commit in a branch into another branch
```bash
# in BRANCH_FROM list commits sha
git log --oneline
# switch to BRANCH_TO
git checkout BRANCH_TO
# cherry-pick commit in BRANCH_FROM into BRANCH_TO
# copy a single commit to the current branch
git cherry-pick SHA

# cherry-pick and change message of commit
git cherry-pick --edit SHA
# opens an editor, then save and quit

# cherry-pick and combine multiple commits
# '--no-commit' option pulls in changes and stage them, but doesn't commit
git cherry-pick --no-commit <SHA_LIST>
# after you need to manually commit changes
git commit -m "COMMENT"

# track which commit we cherry-picked from
# '-x' option adds source SHA to commit message
# only useful with public branches: don't use for local branch
git cherry-pick -x SHA

# track who cherry-picked the commit along with the original committer
# '--signoff' option adds current user's name to commit message
git cherry-pick --signoff SHA
```

### Submodules

A Git repo *inside* a Git repo
* pull down updates easily
* test your changes with an actual dependent project
* share changes easily
* history independent of containig repo
```bash
# use case: share a directory between different repository
# 1) convert directory into a new repository
# 2) add the shared repository as a submodule to another repository
git submodule add git@ORIGIN_URL:DIRECTORY_NAME.git
# git adds a config file named .gitmodules
# finally commit and push

# modify submodule
cd DIRECTORY_SUBMOD
git checkout master
# make changes to submdule
git commit -am "COMMENT in submodule"
git push
# update parent project also
cd ..
git add DIRECTORY_SUBMOD
git commit -am "COMMENT in parent submodule"
git push

# when submodule change, also parent need to be updated

# clone a project with submodules
git clone git@URL:REPO.git
# but initially the submodules directories are empty!
# initialize directory as submodule
cd DIRECTORY_SUBMOD
git submodule init
# clone the repo and checkout the commit specified by the parent project
git submodule update
# the pull as usual
git pull
# but to get changes in the submodule itself and retrieve file update
# from the parent project
git submdule update

# NOTE: 'git submodule update' checks out submodule in a HEADLESS state
# i.e. active branch= '* (no branch)'
git checkout master
# when git warn about ORPHANED commit: merge it directly
git merge SHA
git push
# and don't forget to update also parent directory
cd ..
git commit -a -m "COMMENT in parent"
git push

# always push in order
# 1) /submodule     ---> git push
# 2) /              ---> git push
# if forgot to push in submodule
# other contributor that will run 'git submodule update'
# will have and error like: 'reference is not a tree ... unable to checkout in sobmodule'

# worried about forgetting push in root porject
# will abort a push if you haven't pushed a submodule
git push --recurse-submodules=check

# to push always all repo (also submodule)
git push --recurse-submodules=on-demand

# alias when work with submodule!
git config alias.pushall "push --recurse-submodules=on-demand"
```
