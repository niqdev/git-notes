# Git Real 2

> work in progress

Course index:
* [Interactive rebase](#interactive-rebase)
* [Stashing](#stashing)

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

Reorder commits
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
```bash
# take same file and store away in a temporary area
# so that you can restore them at a later time

# save modified files and restore last commit
# push stash onto the stash-stack
git stash save

# bring stashed files back
git stash apply

# list all stashes (WIP = work in progress)
git stash list

# apply specific N stash (do not remove)
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
```bash
# accidentally delete branch with stash N
# need a new branch to restore stashed changes
# checks a new branch out and drops the stash automatically
git stash branch BRANCH_NAME stash@{N}
# then new branch is an ordinary branch, ready for commits
```
