---
title: Helm Notes
description: My personal helm notes, covering any training, gotchas and random things that I just cannot remember. 
publish: true
---
# Helm
## Base Syntax
```bash
helm install wordpress 
helm upgrade wordpress
helm rollback wordpress
helm uninstall wordpress
```

To remove a deployment you can use: 
```bash
helm list
helm delete deploy-name
```

[[Cloud]], [[Kubernetes (k8s)]]

#kubernetes #rancher #cloudnative
