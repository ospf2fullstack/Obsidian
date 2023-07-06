---
title: Redhat
publish: true
<<<<<<< Updated upstream
=======
tag: redhat,linux
>>>>>>> Stashed changes
---
# RedHat
[[Linux]] UBI image 

## Install Packages
```bash
sudo yum install nodejs
```

# RPM 
RedHat Package Manager
## Install
Install a package
```bash
rpm -i telnet.rpm
```

## Uninstall
Uninstall a package
```bash
rpm -e telnet.rpm
```

## Query
Query package
```bash
rpm -q telnet.rpm
```

# YUM
YUM searches remote repositories and pulls the dependencies automatically. This is stored in the */etc/yum.repos.d*  path
## Install
```bash
yum install ansible
```

### Specified Version
install specific version
```bash
yum install ansible-2.4.2.0
```

## View
advanced view
```bash
ls /etc/yum.repos.d/
```

## Cat
Output the actual url(s) that YUM will pull from.
```bash
cat /etc/yum.repos.d/CentOS-Base.repo 
```

## List
List repos and dependencies
```bash
yum repolist
```

## List (specifically)
List installed and available packages
```bash
yum list ansible
```

## Remove
Remove a package
```bash
yum remove ansible
```

## Show Duplicates
List duplicate packages
```bash
yum --showduplicates list ansible
```

#ubi #redhat #linux 