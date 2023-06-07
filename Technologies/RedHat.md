---
title: Redhat
publish: true
---
# RedHat
[[Linux]] UBI image 

## Install Packages
```bash
sudo yum install nodejs
```

# RPM (Red Hat Package manager)
Install a package
```
rpm -i telnet.rpm
```

Uninstall a package
```
rpm -e telnet.rpm
```

Query package
```
rpm -q telnet.rpm
```

## Advanced Management with YUM
YUM searches remote repositories and pulls the dependencies automatically. This is stored in the */etc/yum.repos.d*  path
```
yum install ansible
```

List repos and dependencies
```
yum repolist
```

advanced view
```
ls /etc/yum.repos.d/
```

Output the actual url(s) that YUM will pull from.
```
cat /etc/yum.repos.d/CentOS-Base.repo 
```

List installed and available packages
```
yum list ansible
```

Remove a package
```
yum remove ansible
```

List duplicate packages
```
yum --showduplicates list ansible
```

install specific version
```
yum install ansible-2.4.2.0
```





#ubi #redhat #linux 