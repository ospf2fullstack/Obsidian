# About Linux
## Leading OS's
Preferred OS in market as of 2023: 
Ubuntu 
RHEL
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
List contents of a directory
```
ls
```

Make Directory
```
mkdir new_directory
```

Change Directory 
```
cd ../../home
```

Create File in PWD (present working directory)
This would be helpful when adding a Readme to a local repo for [[Github]] or [[Gitlab]].
```
cd ./to_some_directory
touch readme.txt 
```

Add Contents to the new File
```
cat > readme.txt
# 
# 
# 
Ctrl + D to exit contents file
```

## Combos
Combine Multiple Commands
creates new directory, makes new directory and prints present working directory
```
cd new_directory; mkdir www; pwd 
```

Create a directory and all of it's Succeeding Folders
```
mkdir -p /new_directory/sub_folder/sub-folder
```

Remove Directory AND contents
```
rm -r /my_directory/dir
```

Copy Directory and Contents
```
cp -r my_dir1 /new_directory/location
```

Copy file from one dir to the next dir
```
cp file.txt copy_of_file.txt
```

Move file from one dir to the next dir
```
mv file.txt ~/etc/tmp/dir
```

## User Accounts
verify current user
```
whoami
```

get the current user id's
```
id
```

switch users (su)
```
su different_user
```

## advanced file management
download files from the internet
```
curl http://www.domain.com/file.txt -O
```
OR
```
wget http://www.domain.com/file.txt -O somefile_name.txt
```

Verify OS Version
```
ls /etc/*release*

or 

cat /etc/*release*
```


# Services
## Managing Services
Start a Service
```
service httpd start
OR 
systemctl start httpd <-- preferred method! 
```

Stop a Service
```
systemctl stop httpd
```

Check the Status of a Service
```
systemctl status httpd
```

Start service on Boot
```
systemctl enable httpd
```

Prevent Service start during boot
```
systemctl disable httpd
```

Start and Stop a custom application (my_webapp)
*/etc/systemd/system* files are similar to MacOS and Windows "Startup folder"
```
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
```
repeat the commands above, but add 
[Unit]
Description=My python web application

[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py
Restart=always

[Install]
WantedBy=multi-user.target
```












#cloud #cloudnative #systems 