# Github
Using [[Git]] to source control your repositories. 
Basic
![[Git#Init (initialize)]]

## Profile Stats

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3eecb6b4-0944-458c-8dc0-162b4a3e744e/Untitled.png)

```markdown
![](<https://github-readme-stats.vercel.app/api?username=ospf2fullstack&show_icons=true&hide_title=true&theme=solarized-dark&count_private=true&hide=stars>)
```


---
# Authentication
## Personal Access Token (HTTP)
```bash
# 1) create personal access token in Developer --> Settings 
github.com/settings/tokens # classic token with no expiration and necessary access for CLI. 

# 2) install GH CLI (https://github.com/cli/cli#installation)
brew install gh 

# 3) add personal access token to CLI 
gh auth login # select HTTPS and yest to authenticate
```

## SSH Key
```bash
# generate the SSH key
ssh-keygen -t ed25519 -C "emailaddress.tld.com"

# start ssh-agent in the background
eval "$(ssh-agent -s)"

# add private key to ssh-agent
ssh-add ~/.ssh/id_ed25519

# add/copy the public key values to paste into Github -> SSH -> New Key
clip < ~/.ssh/id_ed25519.pub
```


# SSO Repos

## Clone

```powershell
git config --global user.name ""
git config --global user.email ""
# verify your configuration
git config -l

# now you can clone the repository
git clone "https://url_for_repo"
...username: ""

# by now, you should be prompted to use browser or token from Github. 
```

## Add Repo to “Desktop”

Add git clone repo to github desktop after you complete the SSO setup.

Add → Add Existing Repository (local) → Choose Path → Let GitHub verify.

# Submodules

```bash
cd ./to-local-repository/home
git submodule add https://github.com/USER/REPO FOLDER-NAME

aka.,
cd ../my-cloud-deployment
git submodule add https://github.com/ospf2fullstack/my-OTHER-deploy
```
If you need to clone a repo the first time, you'll want to make sure you get all submodules too (recursively). 
```bash
git clone --recursive https://github.com/USER/REPO
```

#git #github 