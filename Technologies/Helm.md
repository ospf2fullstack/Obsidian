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
<<<<<<< Updated upstream

=======
>[!tip]
>$ is the same as root level
>{{  $.Values.name }} (useful when using with blocks)
>{{ .Values.name }}
>>>>>>> Stashed changes
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
<<<<<<< Updated upstream
=======
```

## Create
>[!gotcha]
>DO NOT CAPITALIZE THE VALUES.YAML FILE!!! 'values.yaml' is the correct name. 
```bash
# quickly creates a local folder with the specified name in the command with a templated chart
helm create mychart-name 
```

## Remove
To remove a deployment you can use: 
```bash
helm list
helm delete deploy-name
```

## Add Custom Repo
```bash
helm repo add repo-detail
```

---

# Advanced 
## Upgrade (install)
```bash
kubectl create namespace $new-namespace

helm upgrade my-chart ./path-to-chart --values ./path-to-values/values.yaml
```
## Set
```bash
helm install ... -set valueObject=something
# set will take priority over what you have in the values.yaml file! 
```

## UPPER
```yaml
# upper case the value
{{ upper .Values.image.name }} --> image: NGINX

# or
{{ .Values.image.name | upper }}
```

## quote
```yaml
# add quotes
{{ quote .Values.image.name }} --> image: "nginx"
```

## replace
```yaml
# replace x with y
{{ replace "x" "y" .Values.image.name }} --> image: nginy
```

## shuffle
```yaml
# shuffle the values
{{ shuffle .Values.image.name }} --> image: xignn
```

## Compound (concatenate)
```yaml
{{ .Values.image.name | upper | quote | shuffle }} --> image: GN"XIN"
```

# Conditionals
## *if*
```yaml
  labels:
    type: {{ .Values.labels.type }}

# with conditional 'if'
# so, if value is entered here, add it, else, end. 
  {{ if .Values.labels.type }}
  labels:
    type: {{ .Values.labels.type }}
  {{ end }}

# now trim the whitespace
  {{- if .Values.labels.type }}
  labels:
    type: {{ .Values.labels.type }}
  {{- end }}

```

## *else if*
```yaml
  {{- if .Values.labels.type }}
  labels:
    type: {{ .Values.labels.type }}
  {{- else if empty .Values.labels.type "hr"}}
  labels:
    type: human resources
  {{- end }}
```

# Functions
eq - equal
ne - not equal
lt - less than
le - less than or equal to
gt - greater than
ge - greater than or equal
not - negative
empty - value is empty

# With Blocks
```yaml
# without '*with blocks*'
ports:
  port: {{ .Values.service.port }}
  targetPort: {{ .Values.service.targetPort }}
  nodePort:{{ .Values.service.nodePort }}

# with blocks added, sets the context for the entire block
ports:
  {{- with .Values.service }}
  port: {{ .port }}
  targetPort: {{ .targetPort }}
  nodePort:{{ .nodePort }}
  {{- end }}
```

# Loops & Range
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-regioninfo
data:
  regions:
  {{- range .Values.regions }}
  - {{ . | quote }} # loops through the range
  {{- end}}

# output
regions:
  - "us-east-1"
  - "us-east-2"
```

# Helper File
## tpl (template file)
```yaml
_helpers.tpl

{{- define "labels" }}
  app.kubernetes.io/name: nginx
  app.kubernetes.io/instance: nginx
{{-end }}

or, use dynamic values: 

{{- define "labels" }}
  app.kubernetes.io/name: {{ .Release.Name }}
  app.kubernetes.io/instance: {{ .Release.Name }}
{{-end }}


---
# reference later like a CSS block

labels:
  {{- template "labels" . }}

# output
labels:
  app.kubernetes.io/name: nginx
  app.kubernetes.io/instance: nginx
```
BUT, if you need to add nested indents with a template reference: 
```yaml
labels:
  {{- template "labels" .}}
selector:
  matchLabels:
  {{- include "labels" . | indent 2 }}
# very important to add the "include" statement and '| indent' statements. 
```

# Hooks
Initiate backups, email notifications, etc... 
```bash
helm upgrade ... [verify, render, upgrade]
```

## Annotations & Weights
>[!tip]
>annotations are used to define a hook in a chart
```yaml
metadata:
  name:
  annotations:
    "helm.sh/hook": pre-upgrade
    "helm.sh/hook-weight": "5"
```

# Sign and Upload
```bash
# package
helm package ./my-chart-path

# sign
gpg --quick-generate-key "My-Name"
or 
gpg --full-generate-key "My-Name"

# upload public key to gpg public server to verify keys
# keep private key PRIVATE! 

# package again with new keys
helm package --sign --key 'My-Name' --keyring ~/.name.gpg ./my-chart-path

# list keys
gpg -list-keys 



```

---

# Troubleshooting
## Lint
```bash
helm lint ./helm-chart-directory
```

## Template
```bash
# show the output of what is being created
helm template ./helm-chart-repo
```

## Debug
```bash
helm install my-application ./helm-repo --dry-run --debug
>>>>>>> Stashed changes
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
