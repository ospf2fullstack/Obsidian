---
title: automated environment variable configuration
publish: true
---
# Environment Variables
A quick and easy way to switch between cluster environments is to Set Context 
## Set Env Context

![[Kubernetes (k8s)#Set Context]]

## Script Env Context
An easy way to do the first part is with a simple [[Bash Script]] that reads a local .env file pre-typed with all of the environment variables that need to be set. 
![[Bash Script#Contents of myenvironment.env]]

# Set Your PATH
This is easier than you think, but is often the cause of many headaches! 
## Git Bash
```bash
cd /c/Program Files/Git/mingw64/bin
# copy the binary (.exe's) to this file path and restart git bash.
# you should now see that the binarys are executable in bash. 
```