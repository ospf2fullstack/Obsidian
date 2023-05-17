
## Git Bash for Windows 
This is a fantastic SCM for Windows users. 
(Git Bash for Windows)[https://gitforwindows.org/]
# Install

```bash
brew install git 
```

# Setting Up AWS

## Profile

```bash
# Default Profile 
aws configure

# If you want to name a profile
aws configure --profile <name> 
```

### MFA Temporary Session

```bash
# This is necessary if you have MFA enabled on your account 
aws sts get-session-token --serial-number arn-of-the-mfa-device --token-code code-from-token
```

```bash
# Sample output 
{
    "Credentials": {
        "AccessKeyId": "access-key-id",
        "SecretAccessKey": "secret-access-key",
        "SessionToken": "session-token",
        "Expiration": "2023-01-12T02:02:49+00:00"
    }
}
```

# Environment Variables

## Set Environment Variables

```bash
# If you followed this process before, you need to unset the prior env var.
set AWS_ACCESS_KEY_ID=example-access-key-as-in-previous-output
set AWS_SECRET_ACCESS_KEY=example-secret-access-key-as-in-previous-output
set AWS_SESSION_TOKEN=example-session-Token-as-in-previous-output
```

### Unset

```bash
# This should remove the prior conifguration so you can 'set' the new variables. 
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_SESSION_TOKEN
```

## Verify

```bash
# printenv | grep <nameofenvvar>
printenv | grep AWS_SESSION_KEY_ID
```


#git 