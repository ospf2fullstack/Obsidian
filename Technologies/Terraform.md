---
title: Terraform
description: Terraform Notes
publish: true
permalink: terraform
tag: terraform
image: "Assets/innerarity.png"
---
# Terraform

<aside> 🛠 UPGRADE TO A NEW VERSION
```bash
# Easy
Download the .zip
Extract .zip
copy terraform .exe to the root directory (C:\\terraform)

# use an elevated terminal 
> choco upgrade terraform
> A[all]
```
</aside>

# Commands
## Getting Started

```jsx
terraform init     # initialize the environment
terraform fmt      # formats the code for your environment
terraform validate # validates that there are no errors.

terraform plan
terraform apply 
```

## Basics

```json
terraform
terraform apply # apply the changes
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

# Variables

```json
# single variable interpolation 
var.x

# user map variable with key
var.MAP["key"]

# variable list
"${var.LIST}"
```

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

#aws #cloud #cli 