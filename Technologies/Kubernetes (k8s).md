# Getting Started

## Definitions:

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