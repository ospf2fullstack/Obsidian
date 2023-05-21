# k3s
## Raspbian Image
Use Raspberry Pi (other) → Lite Weight (64bit, no gui)

Before removing from disk, make sure you edit this file now, or you will need to edit the /boot/cmdline.txt later

-   Use the imager to set these parameters
    -   SSH (user/pass)
    -   disable WiFi (messes up SSH communication later)
    -   Hostname
    -   location

### Boot
Add this to the end of your cmdline.txt file in the Boot disk root directory (while it’s still plugged into your PC) - or skip

```bash
cgroup_enable=memory 
# add the below line as well, if you want to set static from the device. I set mine in the firewall. 
ip=10.255.255.254::10.255.255.1:255.255.255.0:rpimaster:eth0:off
```

## Configure Raspberry Pi
In raspberry pi, update and install rancher k3s

### SSH
```bash
ssh username@raspberrypi-ip-address
# ssh pi@192.168.1.2
```

### Updates
```bash
# run your damn updates
sudo apt-get update && apt-get upgrade
```

### Bc, you’re lazy
```bash
# I know you didn't do what I told you above, so here it is again
sudo nano /boot/cmdline.txt

# add at the end of line 1 text
cgroup_memory=1 cgroup_enable=memory

ctrl-x, then yes
sudo reboot
```

## The real Control
### Rancher (k3s)
```bash
# now you're ready to install rancher
# do this on the MASTER node only. Use the command down below on the worker nodes. 
curl -sfL <https://get.k3s.io> | K3S_KUBECONFIG_MODE="644" sh -s -

# that's it... 
kubectl get nodes # to get the status of the cluster ndoes 

# oh, you're going to need the token for the other nodes: 
# get master node token
cat /var/lib/rancher/k3s/server/node-token
```

### Worker Nodes
<aside> 📌 (repeat for every worker node)</aside>

Do everything above, but use the below command to register it with the master node:

<aside> ⚠️ GET TOKEN FROM “Rancher” code above</aside>

```bash
# look up for the process to get the token
# use this to register nodes
curl -sfL <https://get.k3s.io> | K3S_TOKEN="[token]" K3S_URL="https://[k3smaster-ip]:6443" K3S_NODE_NAME="[workernode-name]" sh -

```


## K3S Remote Cluster

Add [[Rancher (k3s)]] cluster group to remote PC

```yaml
delete everything in ~/.kube/config
replace it with ~/etc/rancher/k3s/k3s.yaml
/$ kubectl get pods --all-namespaces
```

If you get weird errors from copying your context files from SSH, use SCP to copy the remote context to a local destination.

```json
scp innerarity@10.255.255.254:/etc/rancher/k3s/k3s.yaml . 
# copies the remote rancher yaml to '.' local. 
```

[k3s kubernetes dashboard](https://docs.k3s.io/installation/kube-dashboard)

[[Linux]], [[Cloud]]

#rancher #cloudnative 