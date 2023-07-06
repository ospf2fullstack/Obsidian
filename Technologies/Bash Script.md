---
title: bash scripting for automation engineers and solution architects
publish: true
---
# Bash Script
## Execute .env file
Maintaining a local, ***NOT SYNC'd*** with version control, environment variable is the easiest way to maintain easy context between environments. 

### Contents of myenvironment.env
```env
AWS_ACCESS_KEY_ID=awsvalues
AWS_SECRET_ACCESS_KEY=awsvalues
AWS_REGION=awsvalues
AWS_DEFAULT_REGION=awsvalues
```

### Shell Script to execute .env
```sh
#!/bin/bash

while read l; do                       # while read line, do...
  name=$(cut -d'=' -f1 <<< $l)         
  val=$(cut -d'=' -f2 <<< $l)
  if [[ ! -z ${name// } && ! $name == *"#"* ]]; then
    echo "export $name=$val"           # output to terminal the values of envs
    export $name=$val
  fi
done < myenvironment.env
```

We can see at the end of the shell script that 'done < myenvironment.env' is what identifies the file to execute against.  You can make this a uniform name if you want to share the script among several engineers (aka., 'customer.env'). 

