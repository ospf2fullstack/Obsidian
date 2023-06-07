---
title: Helm Notes
description: My personal helm notes, covering any training, gotchas and random things that I just cannot remember. 
publish: true
---
# Helm
## File Structure
Application (Root)
- templates
	- services
	- deployment
	- claims
- values.yml
- chart.yml
- LICENSE
- README.md
- charts
	- additional chart dependencies

## Base Syntax
```bash
# install a package
helm install wordpress 

# upgrade a package
helm upgrade wordpress

# rollback a package upgrade
helm rollback wordpress

# uninstall a package deployment
helm uninstall wordpress

# download and unzip package
helm pull --untar bitnami/wordpress

# view current deployment
helm list -n namespace --all
```

To remove a deployment you can use: 
```bash
helm list
helm delete deploy-name
```

## Add Custom Repo
```bash
helm repo add repo-detail
```

# Advanced 
## Set
```bash
helm install ... -set valueObject=something
# set will take priority over what you have in the values.yaml file! 
```


# Troubleshooting
```bash
helm install my-application ./helm-repo --dry-run --debug

# gives you full output of errors to anticipate
helm lint ./helm-chart-repo

# show the output of what is being created
helm template ./helm-chart-repo
```
[[Cloud]], [[Kubernetes (k8s)]]

#kubernetes #rancher #cloudnative
