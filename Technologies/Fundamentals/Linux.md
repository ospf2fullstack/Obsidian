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

---

# Common Commands
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


#cloud #cloudnative #systems 