# GIT (SCM - Source Control Manager)
Source Control Management so you can "version" control software repositories. This is highly efficient when working by branch or in teams. 

# Process
## Init (initialize)
```bash
yum install git # install git on CentOS
git version # to verify the current version
git init someRepoName # initialize a repository

# clone a remote branch (github repo)
git clone https://github.com/username/repo/id...

# stage a branch
git branch someBranchName

# checkout
git checkout someBranchName

# track files
git status # verify which files are not currently tracked
git add . # adds all changed files to the git push stage 
git add readme.txt # add a file explicitly 

# commit 
git commit -m "some commit message"

# push for merge
git push 
git pull 
```

## Repository Management

You must associate your working directory with a branch and that branch should be associated with a Jira ticket. 

Open Jira issue → Create a branch from the main branch using the Jira syntax

```powershell
git branch feature/ncam-###    
git branch # view active branch
git checkout feature/ncam-####
git branch # verify again that you switched branches
git status # habit to make sure you're clean and good to code 
```

# Basic Syntax
## Authentication
### HTTPS
![[Github#Personal Access Token (HTTP)]]

### SSH
![[Github#SSH Key]]


## Credentials

```powershell
git config --global user.name ""  # set username
git config --global user.email "" # set email
git config --global -list (or -l) # list configuration
```

## Repository

```powershell
git clone https://repo_url.com/repo.git
```

## Publish Changes

```powershell
# save file 
git status # see changes
git add .  # set the changes 
git commit -m "put your comment here" # commit the changes
git push   # publish the changes 
```

# Advanced
## Get Latest
```bash
git pull
```

## Get Latest to branch
```bash
git checkout main
git checkout your-local-branch && git rebase main
```