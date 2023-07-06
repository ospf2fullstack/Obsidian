---
title: Terraform
description: Terraform Notes
publish: true
permalink: terraform
<<<<<<< Updated upstream
tag: terraform
=======
tag: terraform, aws, ec2, s3, resource, data, module
>>>>>>> Stashed changes
image: "Assets/innerarity.png"
---
# Terraform
## Install
```bash
# windows 
choco install terraform
```

## Upgrade
```bash
# Easy
Download the .zip
Extract .zip
copy terraform .exe to the root directory (C:\\terraform)

# use an elevated terminal 
> choco upgrade terraform
> A[all]
```

# Commands
## Basics
### Dependency
```bash
terraform init     # initialize the environment
terraform fmt      # formats the code for your environment
terraform validate # validates that there are no errors.
```

### Prep/Push
```bash
terraform plan
terraform apply
```

### Common Commands
```yaml
terraform
terraform apply # apply the changes
  terraform apply -auto-approve # no confirmation
terraform plan # create an execution plan
terraform console # creates an interactive console
terraform destroy # destroys terraform-managed infrastructure
terraform force-unlock # remove the lock on the state of the current configuration
terraform fmt # rewrites terraform configuration files with proper formatting
terraform get # download and update modules
terraform graph # generate a graph
terraform import # import existing resources
terraform init # initialize working directory
terraform output # extract value of an output variable
terraform providers # current provider list
terraform push # upload configuration
terraform refresh # update state file
terraform show # provide output in a human-readable format
terraform state # advanced state management
terraform taint # marks resources as tainted
terraform validate # to unmark a resource as tainted
terraform workspace # manage workspaces
```

### Timeout
```tf
resource "aws_instance" "myresource" {
# ...
  timeouts {
    create = "20m"
    }
}
```

---

# Variables
>[!variables]
>variables.tf contains variable declarations

# Outputs
>[!outputs]
>outputs.tf contains outputs from resources.

# Provider
>[!provider]
>provider.tf contains provider definition.

# Terraform
>[!terraform]
>terraform.tf configure terraform behavior. 

# Main
>[!main]
>main.tf main configuration file containing resource definition. 

---

# Attributes

```json
# own resource
self.ATTRIBUTE

# other resources
TYPE.NAME.ATTRIBUTE

# data source
data.TYPE.NAME.ATTRIBUTE

# module outputs
MODULE.NAME.OUTPUT
```

# Structure
## Load order

Alphabetical order
tf or tf.json formats (other formats ignored)
The same named files will be appended, not merged…

# Blocks
## Resource
```.tf
resource "local_file" "pet" {
  filename = "/root/pets.txt"
  content = "We love pets!"
}

---

resource = resource block name
"local file" = resource type {local=provider, file=resource}
"pet" = Resource Name
filename/content = arguments
```

### EC2 
```aws.tf
resource "aws_instance" "web" {
  ami = "ami-xyz"
  instance_type = "t2.micro"
}
```

### S3 Bucket 
```aws.tf
resource "aws_s3_bucket "data" {
  bucket = "webserver-bucket-org-2020"
  acl = "private"
}
```


---
# Workflow
## Pass Value to child module
```
# variables.tf in the root level

variable "ami"
  type = string
  description = "default ami to use"
  value = "xyz123"


# ./modules/variables.tf

variable "ami" {}
```

## Create a resource (main.tf)
```
# main.tf
resource "aws_instance" "web_server" {
  ami = var.ami
  instance_type = var.ec2_instance_type
}
```

## Output the resource references (output.tf)
```
# output.tf
output "instance_public_ip" {
  description = "Public IP of EC2 for xyz web server"
  value = aws_instance.web_server.public_ip
}
```

## Declare the output in the root module
```
# main.tf
module "ec2" {
  source = "./modules/ec2"
  public_ip = module.aws_instance.instance_public_ip
}
```


>[! TIP]
>Pass child module outputs to root parent module
>module.<module_name>.<output_value_name>
---
references: 
https://spacelift.io/blog/terraform-output 

#aws #cloud #cli 