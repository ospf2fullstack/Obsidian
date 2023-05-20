---
title: Kubernetes Notes
publish: true
permalink: kubernetes
description: Everything I have been learning, gotchas included, about Kubernetes. 
---
# Kubernetes (k8s)
The most known Orchestration Technologies: 
[[Docker Swarm]], [[Kubernetes (k8s)]], and [[MESOS]]

## Base Architecture 
Master Node - Kubernetes 
- API Server
- etcd
- controller 
- scheduler

Worker Nodes - Minion nodes host the containers
- container runtime (rkt and cri-o are alternatives)
- kubelet agent

## Core Services
- API Server - Front End to interact with k8s cluster
- etcd - Key-value store to store all data to manage the cluster
- kubelet - agent that runs on each cluster node (ensures containers are running as expected)
- container runtime - used to run containers (docker)
- controller - orchestration brain, meant to respond to node activity (failure)
- scheduler - distributing work across worker nodes

---

## kubectl basics
tool used to manage applications on kubernetes cluster
```bash
# run is used to run a container on the k8s cluster
kubectl run hello-minikube

# cluster-info is used to gather cluster information from k8s
kubectl cluster-info

# get is used to get the nodes or pods from a cluster
kubectl get nodes
kubectl get pods 
```

## Pods
>[!best practice]
>Have a 1:1 pod to container ratio. 

### basic deployment
```bash
kubectl run nginx --image nginx
> pod/nginx created

kubectl get pods
> NAME  READY  STATUS  RESTARTS  AGE 

kubectl describe pod nginx
> Name:
> Namespace:
> ...

kubectl get pods -o wide 
> # same as 'get pods', but with IP added

```
deployments can be built with YAML Syntax, see: ![[YAML#YAML sample syntax]]. 

Yaml files are used as inputs for orchestrating a Kubernetes pod deployment. 

### required fields for deployment.yml
>[!pod-deployment.yml]
```yml, pod-deployment.yml
apiVersion: v1 # could be apps/v1 for ReplicaSet and Deployment
kind: Pod # case sensitive & could be Service, Deployment, ReplicaSet, etc...
metadata: # in the form of a dictionary... (child objects)
  name: myapp-pod 
  labels: # labels should be at the same level as 'name:'
    app: myapp # you can add any key:value pair you want under labels.
    tier: front-end
    type: production
    
spec:
  containers: # list/array
    - name: nginx-container # '-' indicates first item in the list
      image: nginx
```

'kubectl apply -f pod-deployment.yml' to deploy the pod deployment. 



---

# Definitions:

Pod - One or more containers. Best practice is one container per pod bc kube can mark the entire pod unhealthy.

Replica Set - specify the number of pods available.

Deployments - Replicas wrapped into deployments. (yaml file)

---

# Kubectl Config Options

## Set Alias Commands

```powershell
# set syntax/command alias in terminal (powershell)
Set-Alias k kubectl

# set syntax/command alias in terminal (bash)
alias k="kubectl"
```

## List Contexts

```bash
kubectl config get-contexts # get the current context contents from local machine (aka., configuration)
kubectl get pods --all-namespaces # list all active pods and their status across all namespaces
```

## Set Contexts from Remote Cluster
```bash
# configure your environment variables, so if you're using AWS to host your EKS cluster, run 'aws configure'
aws configure [aws access key, aws secret key, region, format]

# pull your context down
aws eks update-kubeconfig --region <region> --name <cluster-name>
```

# Basics

## Deployment

<aside> 📌 DON’T DELTE YAML FILES BEFORE DELETING IT WITH KUBECTL!!

</aside>

1.  Create your YAML file for your deployment
    
    ```yaml
    # sample webapp
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: hello-app-deployment
      labels:
        app: hello-app
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: hello-app
      template:
        metadata:
          labels:
            app: hello-app
        spec:
          nodeSelector:
            type: backend
          containers:
            - name: hello-app
              image: hello-app:v.01
              ports:
                - containerPort: 8080
    ```
    
2.  Kick off the build
    
    ```yaml
    kubectl create -f app.yaml
    ```
    
3.  Verify Pod creation status
    
    ```yaml
    kubectl get pods
    or 
    kubectl get pods --all-namespaces
    ```
    
4.  Clean up
    
    ```yaml
    kubectl delte -f app.yaml
    ```
    

---

# Sort

Best Practices

Run 2 Master nodes for redundancy

1 Application per Pod

use service (permanent IP) for internal services that communicate with static IP

Gather cluster information

```jsx
kubectl cluster-info
```

Create Pod

```markdown
kubectl create -f <file>.yaml
# -f flag signals "file" 
```

Update changes from file

```markdown
kubectl apply -f <file>.yaml
```

Delete Pod

```markdown
kubectl delete pod <name> 
```

Check Replicasets

```markdown
kubectl get replicaset
kubectl delete replicaset <name> 
```

Check nodes

```bash
# check node status of all nodes. 
kubectl get nodes
```

Connect subordinate cluster nodes to master

# Force Restart PODs 
If you have a new image, or just want to pull the latest image again without updating the code, use the below commands to force Kubernetes to restart (deploy a new pod, when it's active, replace the old pod)
```bash
# Get Deployment name
kubectl get deployments -n namespace

# Paste the deployment name into the 'deployment-deploy-name' rollout command
kubectl rollout restart deployment deployment-deploy-name -n namespace

# monitor the process, should take about 90s
kubectl get pod -o wide
```



#kubernetes #cloudnative 