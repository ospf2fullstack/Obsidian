---
title: SSH
description: SSH Basics
publish: true
permalink: SSH
tag: ssh
image: "Assets/innerarity.png"
---
# SSH
## CLI Command
```bash
ssh user@127.0.0.1 
```

# Remote Host (JumpBox)
## Create Key Pair
[[RSA]]
create an SSH key pair
```bash 
# -t flag = type [[RSA]]
ssh-keygen -t rsa 
```

## 
put .pem on the server user (~./ssh)
```bash
cat /home/user/.ssh/key.pem
```

## Share Public Key
put .ppk on the remote user ((~./ssh) or, any folder as long as you [[Linux#Change Directory]] to it.)

## Initiate SSH
Open terminal -> cd to the appropriate folder, initiate SSH. 

```bash
# verify that the ppk is there
cd /to/my/folder/with/ppk
myppk.ppk

# -i flag = identity file, which you are identifying in your command: 
ssh -i myppk.ppk my-server-user@ip.address

# repeat the process from the jumpbox to access internal machines
```

