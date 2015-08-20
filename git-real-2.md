# Git Real 2

> work in progress

Course index:
* [Interactive rebase](#interactive-rebase)

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
