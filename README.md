# Git notes on [codeschool](https://www.codeschool.com/paths/git)

Git is a DVCS (Distributed Version Control System)

```
git help
git help COMMAND_NAME
```

Common settings
```
git config --global user.name "niqdev"
git config --global user.email niqdev@gmail.com
git config --global color.ui true
```

Create new LOCAL repository
```
mkdir REPOSITORY_NAME
cd REPOSITORY_NAME
git init
```

Basic workflow: `untracked --> staged --> committed`
```
// changes since last commit
git status

// add files to staging area
git add .
git add <list of files> (add the list of files)
git add --all (add all files)
git add *.txt (add all txt files in current directory)
git add docs/*.txt (add all txt files in docs directory)
git add docs/ (add all files in docs directory)
git add "*.txt" (add all txt files in the whole project)

// commit changes
git commit -m "COMMENT"

// timeline history
git log
```
