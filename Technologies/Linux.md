---
title: linux notes by Gary Innerarity
publish: true
tags: linux, commands
---
# Linux
## Leading OS's
Preferred OS in market as of 2023: 
Ubuntu 
RHEL [[RedHat]]
CentOS 

## Shell Types
There are several shell types with bash being the preferred
Shell (Bourne Shell) = bash 
Z Shell (zsh)
C Shell (csh)

I prefer using [[Git Bash]] for my shell environment. 

## assumed knowledge
root = super user
The etc file can be modified to allow regular users sudo privileges 

---
# Commands
##  Basic Commands
### List 
contents of a directory
```bash
# general way to list contents in a directory
ls

# list hidden
ls -a

# list recursively
ls -R

# list long format
ls -l

# list file size
ls -h

## COMBINE THEM! 
ls -aRlh
```

### Make 
Directory
```bash
mkdir new_directory
```

### Change 
Directory 
```bash
# general command
cd

# backup two directories
cd ../../home

# move down
cd home/desktop/folder
```

### Create File 
in PWD (present working directory)
This would be helpful when adding a Readme to a local repo for [[Github]] or [[Gitlab]].
```bash
cd ./to_some_directory
touch readme.txt 
```

### Add Contents 
to the new File
```bash
cat > readme.txt
# 
# 
# 
Ctrl + D to exit contents file
```

## Intermediate Commands
Combine Multiple Commands
### Create, Make, List
creates new directory, makes new directory and prints present working directory
```bash
cd new_directory; mkdir www; pwd 
```

### Create Subdirectory
Create a directory and all of it's Succeeding Folders
```bash
mkdir -p /new_directory/sub_folder/sub-folder
```

### Remove
Files and Folders recursively
```bash
rm -r /my_directory/dir
```

### Copy Dir to Dir
Files and Folders recursively
```bash
cp -r my_dir1 /new_directory/location
```

### Copy file to file
Copy file from one dir to the next dir
```bash
cp file.txt copy_of_file.txt
```

### Move
from one dir to the next dir
```bash
mv file.txt ~/etc/tmp/dir
```

## Find
Find files
```bash
find . -name "logfile.txt"

# find all directories
find . -type d

# find all files greater than
find . -type f -size +10M

# find and delete
find . -name "logfile.txt" -delete 
```

## User Management
### Verify Current User
```bash
whoami
```

### Create User
new user 
```bash
useradd [options] USERNAME
useradd -m username -p password
```

### Create Group
```bash
# append -G (group name)
usermod -a -G groupName username
```

### List User ID
get the current user id's
```bash
id
```

### Switch User
switch users (su)
```bash
su different_user
```

# advanced 
## apt-get
```bash
# sudo = root
sudo apt-get update
sudo apt-get upgrade

# combine
sudo apt-get update && sudo apt-get upgrade -y
```

## file management
### download
files from the internet
```bash
curl http://www.domain.com/file.txt -O
```
OR
```bash
wget http://www.domain.com/file.txt -O somefile_name.txt
```

### permissions
```bash
chmod
# rwx (read, write, execute)
```

Verify OS Version
```bash
ls /etc/*release*

or 

cat /etc/*release*
```


# Services
## Managing Services
Start a Service
```bash
service httpd start
OR 
systemctl start httpd <-- preferred method! 
```

Stop a Service
```bash
systemctl stop httpd
```

Check the Status of a Service
```bash
systemctl status httpd
```

Start service on Boot
```bash
systemctl enable httpd
```

Prevent Service start during boot
```bash
systemctl disable httpd
```

Start and Stop a custom application (my_webapp)
*/etc/systemd/system* files are similar to MacOS and Windows "Startup folder"
```bash
# create a unit file in /etc/systemd/system
> touch my_app.service
> sudo nano my_app.service
[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py

> systemctl daemon-reload
> systemctl start my_app
> systemctl status my_app
```

Now, configure your application to run as an app during boot. with *systemctl enable app*
```bash
# repeat the commands above, but add 
[Unit]
Description=My python web application

[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

# Troubleshooting
## System Information
### Usage
```bash
# lists top resource usage
top
```

### Processes
```bash
# running processes
ps
```

### Disk Space
```bash
# disk space usage
df
```

### Memory usage
```bash
free
```


## Logs
### Display all logs
```bash
cd /var/log
ls
```

### List Bottom Lines of Log
```bash
tail /var/log/file.log
```

### View a single log file 
```bash 
sudo less apache2.log 
```

### Search Log File
```bash
# grep can be used to filter out matching text
sudo less apache2.log grep 500
```

## History
```bash
# history command is used to view your command history
history
```

#cloud #cloudnative #systems 