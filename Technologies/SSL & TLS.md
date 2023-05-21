---
title: TLS, SSL, SSH
description: TLS, SSL, and SSH basics/notes. 
publish: true
permalink: TLS-SSL-SSH
tag:
	- TLS
	- SSL
	- SSH
image: "Assets/innerarity.png"
---
# TLS Certificates
Used to guarantee trust between user and server. 

## Symmetric Encryption
The Secret Key is shared from user to server. 

## Asymmetric Encryption
Key Pair (private and public)
Public Key = Publicly accessible Lock 
Private Key = Key used to unlock public (key) lock. 

### Generate Key Pair (Client Side)
Generate a key pair, add the public key to the public server ssh keys file.
> cat ~/.ssh/authorized_keys 
```bash
ssh-keygen 
> 
> id_rsa id_rsa.pub
> 
```

When you [[SSH]], make sure to include the reference to the private key file. 
```bash
ssh -i id_rsa user1@serveraddress.com
```

> Public keys that you generate, can be added to any server that you want as many times as you want as long as your private key is not shared or compromised. 

### Generate Key Pair (Server Side)
```bash
openssl genrsa -out my_key.key 1024

openssl rsa -in my_key.key -pubout > my_key.pem
```


