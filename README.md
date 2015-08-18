# Git notes from [codeschool](https://www.codeschool.com/paths/git)

### Introduction

Git is a DVCS (Distributed Version Control System)

```bash
git help
git help COMMAND_NAME
```

Common settings
```bash
git config --global user.name "niqdev"
git config --global user.email niqdev@gmail.com
git config --global color.ui true
```

Create new LOCAL repository
```bash
mkdir REPOSITORY_NAME
cd REPOSITORY_NAME
git init
```

Basic workflow: `untracked --> staged --> committed`
```bash
# changes since last commit
git status

# add files to staging area
git add .
git add <list of files> (add the list of files)
git add --all (add all files)
git add *.txt (add all txt files in current directory)
git add docs/*.txt (add all txt files in docs directory)
git add docs/ (add all files in docs directory)
git add "*.txt" (add all txt files in the whole project)

# commit changes
git commit -m "COMMENT"

# timeline history
git log
```

### Staging & remotes

```bash
# show unstaged differences since last commit
git diff

# view staged differences (after add)
git diff --staged

# HEAD refers to last commit on current branch/timeline

# unstage files
git reset HEAD FILE_NAME

# discard/reject all change since last commit
git checkout -- FILE_NAME

# add to staged area and commit at the same time
# NOTE: this do NOT add any NEW (untracked) files
git commit -a -m "COMMENT"

# undo last commit: put all changes into staging i.e. move to commit before HEAD
git reset --soft HEAD^

# add file to the last commit and override comment
git add FILE_NAME
git commit --amend -m "COMMENT"

# undo last (1|2) commit and all changes
git reset --hard HEAD^
git reset --hard HEAD^^

# NOTE: do NOT do this after push: it breaks history!
```

Git doesn't take care of access control (who has access to what)
* hosted: GitHub, BitBucket
* self managed: Gitois, Gitorious

So create a new repository on GitHub and push an existing repository

```bash
# ORIGIN: name of the remote (just a bookmark to an url)

# push an existing repository
git remote add origin https://github.com/niqdev/git-notes.git

# show remote repository
git remote -v

# [origin= remote repository name]
# [master= local branch to push]
git push -u origin master

# pull changes down from the remote
git pull
```

Having multiple remotes
```bash
# add new remote (origin|test|production) - associate name to url
git remote add NAME ADDRESS

# remove remote
git remote rm NAME

# push to remote (branch usually is master)
# '-u' option means:
# next time you run 'git push' you do not need to specify the name of the branch
git push -u NAME BRANCH
```

Tips with heroku:
```bash
# create and add remote
heroku create
git remote -v
# after push trigger re-deploy
git push heroku master
```

### Cloning & branching

Clone:
```bash
# clone remote repository in a directory named 'git-notes'
git clone https://github.com/niqdev/git-notes.git
# clone remote repository in a directory named 'git-notes-alias'
git clone https://github.com/niqdev/git-notes.git git-notes-alias
```

Branch:
```bash
# create new local branch (do NOT switch or move HEAD)
git branch BRANCH_NAME

# list local branch
git branch
# list remote branch
git branch -r
# list local and remote branch
git branch -a

# move HEAD to branch timeline
git checkout BRANCH_NAME

# creat new branch and switch timeline
git checkout -b BRANCH_NAME
```

Merge:

`master --(develope feature on)--> BRANCH_NAME --(back to)--> master`

```bash
# merge from-to (fast forward: no commit of merge)
# [BRANCH_TO= master][BRANCH_FROM= BRANCH_NAME]
git checkout BRANCH_TO
git merge BRANCH_FROM

# force no fast forward merge
git merge --no-ff BRANCH_FROM

# when merging, if change were made on both branches,
# git can't fast-forward so need to add merge commit (automatically)
```

### Branching
```bash
git checkout -b BRANCH_NAME
# create remote branch
# links local branch to the remote branch (start tracking)
git push origin BRANCH_NAME

# show remote branches and if are tracked/sync
git remote show origin
```

Remove branch:
```bash
# delete REMOTE branch
git push origin :REMOTE_BRANCH_NAME
# delete LOCAL branch (warning if uncommitted changes)
git branch -d LOCAL_BRANCH_NAME
# delete LOCAL branch (without warnings)
git branch -D LOCAL_BRANCH_NAME

# if try to push to a deleted remote branch
# to clean up deleted remote branches
git remote prune origin
git fetch -p
```

Tips with heroku:
```bash
# heroku deploys only MASTER branch
# push and deploy local branch on heroku
git push heroku LOCAL_BRANCH_NAME:master
```

Tag:
```bash
# a TAG is a reference to a commit (used mostly for reference versioning)
# list all tags
git tag
# checkout code at specific tag i.e TAG_NAME= v0.0.X
git checkout TAG_NAME
# add a new tag
git tag -a TAG_NAME -m "v0.0.X"
# push new tags
git push --tags
```
