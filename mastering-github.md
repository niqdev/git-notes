# Mastering GitHub

Course index:
* [Fork based workflows](#fork-based-workflows)

### Fork based workflows
Different level of configuration
```bash
# to set configuration for a single repo
--local
git config --local user.email local@repo.com
git config --local --list
cat .git/config

# to set configuration for your user
--global
git config --global user.name
git config --global user.name "my name"
git config --global user.email
git config --global user.email global@repo.com
git config --global --list
cat ~/.gitconfig

# to set configuration for all users
--system
git config --system color.ui true
```

Configure line endings: both strip carriage returns, on saving file to the repository
```bash
# linux/Mac
git config --global core.autocrlf input
# Windows: "true" adds the CRs back when you check files out to the working directory
git config --global core.autocrlf true
```

Configure default
```bash
# push default
git config --global push.default simple
# matching: pushes all matching branches up to GitHub
# simple: just pushes current branch to GitHub

# pull default
git config --global pull.rebase true
# git pull = fetch + merge (adds commit of merge: useless merge)
# git pull --rebase = fetch + rebase

# Reuse Recorded Resolution (ReReRe)
# records all fixes to merge conflicts
# reuse them automatically if the same conflicts recurs
# particular useful when cherry picking to multiple branches or constantly rebasing
git config --global rerere.enabled true
```

Configure aliases
```bash
# silent status output
git config --global alias.s "status -s"
# concise useful log output
git config --global alias.lg "log --oneline --decorate --all --graph"
```

*Fork on GitHub*: if you aren't a collaborator, you can clone locally and commit, but you can't push.

*Submitting a Pull Request on GitHub*

Step: `Pull requests > New pull request > Create pull request > Send pull request`

then, only the owner or collaborators can accept/reject PRs i.e. `Merge pull request`

Updating fork: every time a PR is accepted in upstream repository
```bash
# add remote for upstream
git remote add upstream PATH_TO_REPO

# fetch changes
git fetch upstream
# merge them into master
git merge upstream/master master

# or equivalent
git pull upstream master

# push changes to your master
git push origin master
```
