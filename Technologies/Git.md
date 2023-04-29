
# Process

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

# Pull Main and Merge
