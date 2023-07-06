# AWS
AWS is dense and constantly changing. The core of their services follow the [Well-Architected Framework](https://aws.amazon.com/architecture/well-architected), which should truly be followed - at least until you gain valuable knowledge to refute it and back it up with better decisions. 

The [[Well-Architected Six Pillars]], consists of just that, six pillars. 

## Basic Account Setup
Create an IAM user with "least privilege" only permissions. 

Once the account is configured, login, change the password and proceed to set MFA immediately. 

Before you start using [[STS]], you need to download your access keys to fulfill the STS request. 
>[!Tip]
>STS is a temporary access key for the day... make sure you download AWS CLI after you download the access key from the AWS security center. 

Try to structure everything in the AWS CLI as a profile, leaving 'Default' blank. This will get you in the habbit of context switching. 
```bash
aws configure --profile my-profile

# the --profile flag will need to be used for each command. 
# follow all prompts
```

## Tossing in K8s
It'll help if your [[Kubernetes (k8s)]] Context matches the name of your AWS Profiles; as an engineer. 


---

[[EC2]]
[[IoT Core]]
[[S3]]
[[STS]]
