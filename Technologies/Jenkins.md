---
title: Jenkins
description: Jenkins notes. 
publish: true
---
# Jenkins
## Installation
```bash
# installed on ubuntu 20.04 [Install on Linux](https://www.jenkins.io/doc/book/installing/linux/)
SSH into@x.x.x.x # public virtual machine

# get updates 
sudo apt-get update && sudo apt-get upgrade

# install JAVA
sudo apt install openjdk-11-jdk -y # or whatever the current version is

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update -y

sudo apt-get install jenkins
```

## Start
```bash
sudo systemctl enable jenkins

sudo systemctl start jenkins

sudo systemctl status jenkins
```

## Open Firewall Port
```bash
sudo ufw allow 8080

sudo ufw status
```
>[!tip]
>if you're not able to connect still, make sure your cloud service is allowing traffic to pass. 

## Get Password
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```



---
# References
[Jenkins Main Paige](https://www.jenkins.io/)