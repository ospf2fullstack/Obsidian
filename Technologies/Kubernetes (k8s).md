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

kubectl create
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

Deploy the yml file to make your pod: 
```yml
kubectl create -f pod-deployment.yml
kubectl run -f pod-deployment.yml # new deployment
kubectl apply -f pod-deployment.yml # updates to deployment file 
```

### Basic Troubleshooting
```bash
kubectl describe pods # all pods
kubectl describe pod my-app # single pod 
kubectl logs my-app # recent logs for my-app
```

---

# Definitions:
>[!key definitions]
>*Pod* - One or more containers. Best practice is one container per pod bc kube can mark the entire pod unhealthy.
>*Replica Set* - (sets of pods) specify the number of pods available.
>*Replication Controller* - being deprecated with replica set.
>*template* - specifies the details from the pod deployment to match the configuration of original deployment pod.

## Pod
![[Kubernetes (k8s)#required fields for deployment.yml]]

## Replication Controller (being deprecated)
```yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc # name of pod after deployed

  labels:
    app: myapp
    tier: front-end
    type: production
    classification: non-cui
spec:
  template: # specify the pod template from the definition.yml
    metadata: # This is from the definition.yml file
      name: myapp-pod
      labels:
        app: myapp
        tier: front-end
        type: production
        classification: non-cui
	spec:
	  containers:
	  - name: nginx-controller
	    image: nginx
	replicas: 3 # sibling of spec
```

deploy
```bash
kubectl create -f rc-definition.yml
```

check status
```bash
kubectl get replicationcontroller
> NAME DESIRED CURRENT READY AGE
```

---

## Replica Set 
```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    tier
spec:
  template:
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
replicas: 3
selector: # specify which pods fall under it <-- the main difference between replica controller and replica set
    matchLabels:
      type: production
      tier: front-end
      classification: non-cui
```

deploy
```bash
kubectl create -f replicaset-definition.yml
```

check status
```bash
kubectl get replicaset
> NAME DESIRED CURRENT READY AGE 
```

scale replica set
```bash
nano replicaset-definition.yml
> replicas: # update this value
# then 
kubectl replace -f replicaset-definition.yml

# OR
kubectl scale --replicas=6 -f replicaset-definition.yml

# OR
kubectl scale --replicas=6 replicaset myapp-replicaset # not recommended
```

## Deployments 
Deployments supersede Replica sets, but are basically exactly the same. 
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    tier
spec:
  template:
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
replicas: 3
selector: # specify which pods fall under it <-- the main difference between replica controller and replica set
    matchLabels:
      type: production
      tier: front-end
      classification: non-cui
```

deploy
```bash
kubectl create -f deployment-definition.yml
```

check status 
```bash
kubectl get deployments 
kubectl get replicaset 
> NAME DESIRED CURRENT READY AGE # name now has deployment name
kubectl get pods 

kubectl get all
```

## Networking
### NodePort
Service: NodePort - Maps internal port to port on the Node
- 3 ports to map
	- targetPort (on pod)
	- Port (on service)
	- nodePort (on node (external) port range of 30000-32767)
```yml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80 # pod, if you don't provide it, it'll default to 'port'
      port: 80 # service
      nodePort: 30008 # node, if you don't provide it, it'll default to random within the 30000-32767 range...
  selector:
    labels: # should match what you have in the pod-definition.yml
      app: myapp
      type: front-end
```
deploy
```bash
kubectl create -f service-definition.yml
```

check status
```bash
kubectl get services
```


### ClusterIP
Service: ClusterIP - creates virtual IP on service for service to service communication. 
```yml
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    type: back-end
```
### Load Balancer
Service: LoadBalancer - Distributes load among front-end tier
```yml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: back-end
```
## Storage Class
>[!content coming soon]

---

# Set:
## Alias
```powershell
# set syntax/command alias in terminal (powershell)
Set-Alias k kubectl

# set syntax/command alias in terminal (bash)
alias k="kubectl"
```

## Context
```bash
# configure your environment variables, so if you're using AWS to host your EKS cluster, run 'aws configure'
aws configure [aws access key, aws secret key, region, format]

# pull your context down
aws eks update-kubeconfig --region <region> --name <cluster-name>
```

# List:
## Context
```bash
kubectl config get-contexts # get the current context contents from local machine (aka., configuration)
kubectl get pods --all-namespaces # list all active pods and their status across all namespaces
```
## Pods
```bash
kubectl get pods
kubectl get pod my-app
kubectl get pod my-app -n namespace
```
## Logs
```bash
kubectl logs my-app
kubectl logs my-app -n namespace
```

# Context
## AWS EKS
```bash
# make sure you have AWS CLI authentication completed ('aws configure')
aws eks update-kubeconfig --region us-east-1 --name my-eks
```

## k3s (rancher)
'cat /etc/rancher/k3s/k3s.yaml' then copy that to your local disk. 
![[Rancher (k3s)#K3S Remote Cluster]]


---

# kubectl controls:
>[!tip]
>Operate 2 Master Nodes for HA/Redundancy
>[Best Practices](https://tansanrao.com/kubernetes-ha-cluster-with-kubeadm/)
## Cluster Info

```bash
kubectl cluster-info
```

## Create Pod
```bash
kubectl create -f <file>.yaml
# -f flag signals "file" 
# or # 
kubectl run -f pod-deployment.yml # new deployment
kubectl apply -f pod-deployment.yml # updates to deployment file 
```

## Update Pod
```bash
kubectl apply -f <file>.yaml
```

## Delete Pod
```bash
kubectl delete pod <name> -n namespace
```

## Check ReplicaSets
```markdown
kubectl get replicaset -n namespace
kubectl delete replicaset <name> 
```

## Check nodes
```bash
# check node status of all nodes. 
kubectl get nodes
```


---
# Rollouts
## Rollout Commands
```bash
kubectl rollout status deployment/myapp-deployment # status
kubectl rollout history deployment/myapp-deployment # history
```
Rolling updates is the default update strategy. 
### Updates
```bash
# modify the definition file (deploy)
kubectl apply -f deployment-definition.yml
```
#### Revert a Rollout
```bash
kubectl rollout undo deployment/myapp-deployment
```

## Force Restart
## Pods (pulls new image)
If you have a new image, or just want to pull the latest image again without updating the code, use the below commands to force Kubernetes to restart (deploy a new pod, when it's active, replace the old pod)
```bash
# Get Deployment name
kubectl get deployments -n namespace

# Paste the deployment name into the 'deployment-deploy-name' rollout command
kubectl rollout restart deployment deployment-deploy-name -n namespace

# monitor the process, should take about 90s
kubectl get pod -o wide
```

---

# Troubleshooting 
```bash
kubectl logs pod-name # pulls latest logs from pod

kubectl describe pod-name # pull deploy details of a specified pod
kubectl describe service # pull details of a particular service
kubectl describe deployments -n namespace 
kubectl describe deployment deployment-name
kubectl get services
kubectl describe service service-name

kubectl get deployments

kubectl get pods -o wide # get all pods 

kubectl get replicaset
kubectl delete replicaset replicaset-name

kubectl explain replicaset

kubectl get all -n namespace 
```


#kubernetes #cloudnative 