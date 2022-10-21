#### Upgrade the current version of kubernetes from 1.23.0 to 1.24.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the controlplane node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.

#### Upgrade controlplane node first and drain node node01 before upgrading it. Pods for gold-nginx should run on the controlplane node subsequently.

weight 15
#### Cluster Upgraded? 

#### pods 'gold-nginx' running on controlplane?

### Here is the solution for this task. Please note that the output of these commands have not been added here.

``` 
On the controlplane node:

root@controlplane:~# kubectl drain controlplane --ignore-daemonsets
root@controlplane:~# apt update
root@controlplane:~# apt-get install kubeadm=1.24.0-00
root@controlplane:~# kubeadm upgrade plan v1.24.0
root@controlplane:~# kubeadm upgrade apply v1.24.0
root@controlplane:~# apt-get install kubelet=1.24.0-00
root@controlplane:~# systemctl daemon-reload
root@controlplane:~# systemctl restart kubelet
root@controlplane:~# kubectl uncordon controlplane
Before draining node01, we need to remove the taint from the controlplane node.

# Identify the taint first. 
root@controlplane:~# kubectl describe node controlplane | grep -i taint

# Remove the taint with help of "kubectl taint" command.
root@controlplane:~# kubectl taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-

# Verify it, the taint has been removed successfully.  
root@controlplane:~# kubectl describe node controlplane | grep -i taint

Now, drain the node01 as follows: -

root@controlplane:~# kubectl drain node01 --ignore-daemonsets
SSH to the node01 and perform the below steps as follows:

root@node01:~# apt update
root@node01:~# apt-get install kubeadm=1.24.0-00
root@node01:~# kubeadm upgrade node
root@node01:~# apt-get install kubelet=1.24.0-00
root@node01:~# systemctl daemon-reload
root@node01:~# systemctl restart kubelet

To exit from the specific node, type exit or logout on the terminal.

Back on the controlplane node:

root@controlplane:~# kubectl uncordon node01
root@controlplane:~# kubectl get pods -o wide | grep gold (make sure this is scheduled on
``` 
##### on controle plan :
``` 
controlplane ~ ➜  kubectl get nodes
NAME           STATUS   ROLES                  AGE     VERSION
controlplane   Ready    control-plane,master   3m35s   v1.23.0
node01         Ready    <none>                 2m56s   v1.23.0

controlplane ~ ✖ apt update
Hit:2 http://archive.ubuntu.com/ubuntu bionic InRelease                                                                     
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [9,383 B]                                                    
Hit:3 http://archive.ubuntu.com/ubuntu bionic-updates InRelease                                                                       
Hit:4 https://download.docker.com/linux/ubuntu bionic InRelease                                                                       
Hit:5 http://archive.ubuntu.com/ubuntu bionic-backports InRelease                                                          
Hit:6 http://security.ubuntu.com/ubuntu bionic-security InRelease                                    
Fetched 9,383 B in 2s (4,319 B/s)                 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
31 packages can be upgraded. Run 'apt list --upgradable' to see them.
   
# find the latest 1.25 version in the list
# it should look like 1.25.x-00, where x is the latest patch
  
controlplane ~ ➜  apt-cache madison kubeadm
   kubeadm |  1.25.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.20-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.19-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.18-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.17-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.4-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.17-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.7-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.11-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.15.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.15.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.15.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.14.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.13.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.13.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.13.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.12.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.11.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.9.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.9.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.1-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.0-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.3-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.11-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.5.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages

controlplane ~ ➜  apt-mark unhold kubeadm
Canceled hold on kubeadm.
  
controlplane ~ ➜  apt-get update && apt-get install -y kubeadm=1.24.0-00
Hit:1 https://packages.cloud.google.com/apt kubernetes-xenial InRelease                                                               
Hit:2 https://download.docker.com/linux/ubuntu bionic InRelease                                                                       
Hit:3 http://archive.ubuntu.com/ubuntu bionic InRelease                                                              
Hit:4 http://archive.ubuntu.com/ubuntu bionic-updates InRelease                                                      
Hit:5 http://security.ubuntu.com/ubuntu bionic-security InRelease
Hit:6 http://archive.ubuntu.com/ubuntu bionic-backports InRelease
Reading package lists... Done                      
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 30 not upgraded.
Need to get 9,003 kB of archives.
After this operation, 836 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.24.0-00 [9,003 kB]
Fetched 9,003 kB in 0s (32.2 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 15453 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.24.0-00_amd64.deb ...
Unpacking kubeadm (1.24.0-00) over (1.23.0-00) ...
Setting up kubeadm (1.24.0-00) ...
  
controlplane ~ ➜  apt-mark hold kubeadm
kubeadm set on hold.
  
  controlplane ~ ➜  kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.0", GitCommit:"4ce5a8954017644c5420bae81d72b09b735c21f0", GitTreeState:"clean", BuildDate:"2022-05-03T13:44:24Z", GoVersion:"go1.18.1", Compiler:"gc", Platform:"linux/amd64"}
  
  
controlplane ~ ➜  kubeadm upgrade plan
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
W1011 05:12:18.973914    9047 initconfiguration.go:120] Usage of CRI endpoints without URL scheme is deprecated and can cause kubelet errors in the future. Automatically prepending scheme "unix" to the "criSocket" with value "/run/containerd/containerd.sock". Please update your configuration!
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: v1.24.0
[upgrade/versions] kubeadm version: v1.24.0
I1011 05:12:27.141153    9047 version.go:255] remote version is much newer: v1.25.2; falling back to: stable-1.24
[upgrade/versions] Target version: v1.24.6
[upgrade/versions] Latest version in the v1.24 series: v1.24.6

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       TARGET
kubelet     2 x v1.23.0   v1.24.6

Upgrade to the latest version in the v1.24 series:

COMPONENT                 CURRENT   TARGET
kube-apiserver            v1.24.0   v1.24.6
kube-controller-manager   v1.24.0   v1.24.6
kube-scheduler            v1.24.0   v1.24.6
kube-proxy                v1.24.0   v1.24.6
CoreDNS                   v1.8.6    v1.8.6
etcd                      3.5.1-0   3.5.3-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.24.6

Note: Before you can perform this upgrade, you have to update kubeadm to v1.24.6.

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________
  
  
 controlplane ~ ✖ kubeadm upgrade apply v1.24.0
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
W1011 05:15:30.123021    9270 initconfiguration.go:120] Usage of CRI endpoints without URL scheme is deprecated and can cause kubelet errors in the future. Automatically prepending scheme "unix" to the "criSocket" with value "/run/containerd/containerd.sock". Please update your configuration!
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade/version] You have chosen to change the cluster version to "v1.24.0"
[upgrade/versions] Cluster version: v1.24.0
[upgrade/versions] kubeadm version: v1.24.0
[upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]: y
[upgrade/prepull] Pulling images required for setting up a Kubernetes cluster
[upgrade/prepull] This might take a minute or two, depending on the speed of your internet connection
[upgrade/prepull] You can also perform this action in beforehand using 'kubeadm config images pull'
[upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.24.0" (timeout: 5m0s)...
[upgrade/etcd] Upgrading to TLS for etcd
[upgrade/staticpods] Preparing for "etcd" upgrade
[upgrade/staticpods] Renewing etcd-server certificate
[upgrade/staticpods] Renewing etcd-peer certificate
[upgrade/staticpods] Renewing etcd-healthcheck-client certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/etcd.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2022-10-11-05-15-44/etcd.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=etcd
[upgrade/staticpods] Component "etcd" upgraded successfully!
[upgrade/etcd] Waiting for etcd to become available
[upgrade/staticpods] Writing new Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests3786595109"
[upgrade/staticpods] Preparing for "kube-apiserver" upgrade
[upgrade/staticpods] Current and new manifests of kube-apiserver are equal, skipping upgrade
[upgrade/staticpods] Preparing for "kube-controller-manager" upgrade
[upgrade/staticpods] Current and new manifests of kube-controller-manager are equal, skipping upgrade
[upgrade/staticpods] Preparing for "kube-scheduler" upgrade
[upgrade/staticpods] Current and new manifests of kube-scheduler are equal, skipping upgrade
[upgrade/postupgrade] Removing the deprecated label node-role.kubernetes.io/master='' from all control plane Nodes. After this step only the label node-role.kubernetes.io/control-plane='' will be present on control plane Nodes.
[upgrade/postupgrade] Adding the new taint &Taint{Key:node-role.kubernetes.io/control-plane,Value:,Effect:NoSchedule,TimeAdded:<nil>,} to all control plane Nodes. After this step both taints &Taint{Key:node-role.kubernetes.io/control-plane,Value:,Effect:NoSchedule,TimeAdded:<nil>,} and &Taint{Key:node-role.kubernetes.io/master,Value:,Effect:NoSchedule,TimeAdded:<nil>,} should be present on control plane Nodes.
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.24.0". Enjoy!

[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.

controlplane ~ ✖ kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   14m   v1.23.0
node01         Ready    <none>          14m   v1.23.0

#### It wil Still pointing to older version because what we seehere is version of kubelet and kubeadm upgrade command does not upgrade kubelet. 

controlplane ~ ✖ kubectl drain controlplane --ignore-daemonsets
node/controlplane already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-proxy-kxffv, kube-system/weave-net-h48tg
evicting pod kube-system/coredns-6d4b75cb6d-txfbl
evicting pod kube-system/coredns-6d4b75cb6d-bp5ns
pod/coredns-6d4b75cb6d-bp5ns evicted
pod/coredns-6d4b75cb6d-txfbl evicted
node/controlplane drained

controlplane ~ ➜  apt-mark unhold kubelet kubectl
Canceled hold on kubelet.
Canceled hold on kubectl.


controlplane ~ ➜  apt-get update 
Hit:1 https://packages.cloud.google.com/apt kubernetes-xenial InRelease                   
Hit:2 https://download.docker.com/linux/ubuntu bionic InRelease                                                                       
Hit:3 http://security.ubuntu.com/ubuntu bionic-security InRelease                                                                     
Hit:4 http://archive.ubuntu.com/ubuntu bionic InRelease                                                              
Hit:5 http://archive.ubuntu.com/ubuntu bionic-updates InRelease
Hit:6 http://archive.ubuntu.com/ubuntu bionic-backports InRelease
Reading package lists... Done

controlplane ~ ✖ apt-get install -y kubelet=1.24.0-00  kubectl=1.24.0-00
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be upgraded:
  kubelet
1 upgraded, 0 newly installed, 0 to remove and 30 not upgraded.
Need to get 19.2 MB of archives.
After this operation, 8,159 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.24.0-00 [19.2 MB]
Fetched 19.2 MB in 0s (63.0 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 15453 files and directories currently installed.)
Preparing to unpack .../kubelet_1.24.0-00_amd64.deb ...
/usr/sbin/policy-rc.d returned 101, not running 'stop kubelet.service'
Unpacking kubelet (1.24.0-00) over (1.23.0-00) ...
Setting up kubelet (1.24.0-00) ...
/usr/sbin/policy-rc.d returned 101, not running 'start kubelet.service'

controlplane ~ ➜  systemctl start kubelet.service 

controlplane ~ ➜  apt-get install -y kubelet=1.24.0-00  kubectl=1.24.0-00
Reading package lists... Done
Building dependency tree       
Reading state information... Done
kubelet is already the newest version (1.24.0-00).
0 upgraded, 0 newly installed, 0 to remove and 30 not upgraded.

controlplane ~ ➜  sudo systemctl daemon-reload 
controlplane ~ ➜  sudo systemctl restart kubelet.service 

controlplane ~ ✖ kubectl uncordon controlplane 
node/controlplane uncordoned

controlplane ~ ➜  kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   21m   v1.24.0
node01         Ready    <none>          21m   v1.23.0

```
### WORKER NODE PATCHING: 
``` 
controlplane ~ ➜  kubectl taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-
node/controlplane untainted

controlplane ~ ✖ kubectl describe node controlplane | grep -i taint
Taints:             <none>

controlplane ~ ✖ kubectl drain node01 --ignore-daemonsets 
node/node01 already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-proxy-mhpdk, kube-system/weave-net-hbjp7
evicting pod kube-system/coredns-6d4b75cb6d-97frr
evicting pod admin2406/deploy4-6c65b8cd4d-zjsvb
evicting pod admin2406/deploy2-5d7ffdf7db-7nlrr
evicting pod admin2406/deploy1-575f6fd5fd-5lrgr
evicting pod admin2406/deploy5-7cc6cf4785-5mlvq
evicting pod default/gold-nginx-7cf65dbf6d-hvxvc
evicting pod admin2406/deploy3-6d99bdb45d-6vsmx
evicting pod kube-system/coredns-6d4b75cb6d-446x5
pod/deploy3-6d99bdb45d-6vsmx evicted
pod/deploy4-6c65b8cd4d-zjsvb evicted
pod/deploy5-7cc6cf4785-5mlvq evicted
pod/deploy2-5d7ffdf7db-7nlrr evicted
I1014 04:48:52.461732   14449 request.go:601] Waited for 1.105226178s due to client-side throttling, not priority and fairness, request: GET:https://controlplane:6443/api/v1/namespaces/kube-system/pods/coredns-6d4b75cb6d-446x5
pod/deploy1-575f6fd5fd-5lrgr evicted
pod/gold-nginx-7cf65dbf6d-hvxvc evicted
pod/coredns-6d4b75cb6d-446x5 evicted
pod/coredns-6d4b75cb6d-97frr evicted
node/node01 drained

controlplane ~ ➜  kubectl get pods -A
NAMESPACE     NAME                                   READY   STATUS    RESTARTS        AGE
admin2406     deploy1-575f6fd5fd-sxfzf               1/1     Running   0               4m36s
admin2406     deploy2-5d7ffdf7db-ldxc5               1/1     Running   0               4m36s
admin2406     deploy3-6d99bdb45d-w4z96               1/1     Running   0               4m36s
admin2406     deploy4-6c65b8cd4d-kqgsx               1/1     Running   0               4m36s
admin2406     deploy5-7cc6cf4785-w5gmn               1/1     Running   0               4m36s
alpha         alpha-mysql-56bdcdf8f7-gwzrw           0/1     Pending   0               19m
default       gold-nginx-7cf65dbf6d-b78t6            1/1     Running   0               4m36s
kube-system   coredns-6d4b75cb6d-hlrvs               1/1     Running   0               4m36s
kube-system   coredns-6d4b75cb6d-kf5nv               1/1     Running   0               4m36s
kube-system   etcd-controlplane                      1/1     Running   0               9m31s
kube-system   kube-apiserver-controlplane            1/1     Running   1 (9m41s ago)   9m32s
kube-system   kube-controller-manager-controlplane   1/1     Running   0               9m32s
kube-system   kube-proxy-4wwr9                       1/1     Running   0               132m
kube-system   kube-proxy-mhpdk                       1/1     Running   0               132m
kube-system   kube-scheduler-controlplane            1/1     Running   0               9m27s
kube-system   weave-net-hbjp7                        2/2     Running   1 (132m ago)    132m
kube-system   weave-net-xz6h8                        2/2     Running   1 (132m ago)    132m

controlplane ~ ✖ kubectl describe node controlplane 
Name:               controlplane
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=controlplane
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Fri, 14 Oct 2022 02:40:11 +0000
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  controlplane
  AcquireTime:     <unset>
  RenewTime:       Fri, 14 Oct 2022 04:54:25 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Fri, 14 Oct 2022 02:40:40 +0000   Fri, 14 Oct 2022 02:40:40 +0000   WeaveIsUp                    Weave pod has set this
  MemoryPressure       False   Fri, 14 Oct 2022 04:53:14 +0000   Fri, 14 Oct 2022 02:40:06 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Fri, 14 Oct 2022 04:53:14 +0000   Fri, 14 Oct 2022 02:40:06 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Fri, 14 Oct 2022 04:53:14 +0000   Fri, 14 Oct 2022 02:40:06 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Fri, 14 Oct 2022 04:53:14 +0000   Fri, 14 Oct 2022 04:43:42 +0000   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  10.2.5.9
  Hostname:    controlplane
Capacity:
  cpu:                36
  ephemeral-storage:  507930276Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             214587048Ki
  pods:               110
Allocatable:
  cpu:                36
  ephemeral-storage:  468108541587
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             214484648Ki
  pods:               110
System Info:
  Machine ID:                 22837d9ed1354c2cabf80a40bcb59f78
  System UUID:                524e974f-0193-94b9-6a6f-6b31bb30bab2
  Boot ID:                    82458608-de76-401f-b1fd-76b622befd2a
  Kernel Version:             5.4.0-1089-gcp
  OS Image:                   Ubuntu 18.04.6 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.6.6
  Kubelet Version:            v1.24.0
  Kube-Proxy Version:         v1.24.0
PodCIDR:                      10.244.0.0/24
PodCIDRs:                     10.244.0.0/24
Non-terminated Pods:          (14 in total)
  Namespace                   Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                    ------------  ----------  ---------------  -------------  ---
  admin2406                   deploy1-575f6fd5fd-sxfzf                0 (0%)        0 (0%)      0 (0%)           0 (0%)         5m43s
  admin2406                   deploy2-5d7ffdf7db-ldxc5                0 (0%)        0 (0%)      0 (0%)           0 (0%)         5m43s
  admin2406                   deploy3-6d99bdb45d-w4z96                0 (0%)        0 (0%)      0 (0%)           0 (0%)         5m43s
  admin2406                   deploy4-6c65b8cd4d-kqgsx                0 (0%)        0 (0%)      0 (0%)           0 (0%)         5m43s
  admin2406                   deploy5-7cc6cf4785-w5gmn                0 (0%)        0 (0%)      0 (0%)           0 (0%)         5m43s
  default                     gold-nginx-7cf65dbf6d-b78t6             0 (0%)        0 (0%)      0 (0%)           0 (0%)         5m43s
  kube-system                 coredns-6d4b75cb6d-hlrvs                100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     5m43s
  kube-system                 coredns-6d4b75cb6d-kf5nv                100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     5m43s
  kube-system                 etcd-controlplane                       100m (0%)     0 (0%)      100Mi (0%)       0 (0%)         10m
  kube-system                 kube-apiserver-controlplane             250m (0%)     0 (0%)      0 (0%)           0 (0%)         10m
  kube-system                 kube-controller-manager-controlplane    200m (0%)     0 (0%)      0 (0%)           0 (0%)         10m
  kube-system                 kube-proxy-4wwr9                        0 (0%)        0 (0%)      0 (0%)           0 (0%)         134m
  kube-system                 kube-scheduler-controlplane             100m (0%)     0 (0%)      0 (0%)           0 (0%)         10m
  kube-system                 weave-net-xz6h8                         100m (0%)     0 (0%)      200Mi (0%)       0 (0%)         134m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  
  controlplane ~ ➜  ssh node01
Last login: Fri Oct 14 04:48:06 2022 from 10.2.5.9

root@node01 ~ ➜  apt update; apt-mark unhold kubeadm ; apt-cache madison kubeadm ; apt-get update ; apt-get install kubeadm=1.24.0-00Get:1 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [9383 B]                             
Get:2 https://download.docker.com/linux/ubuntu bionic InRelease [64.4 kB]                                                             
Get:3 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 Packages [60.6 kB]                                           
Get:4 https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages [32.4 kB]                                                 
Get:5 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]    
Hit:6 http://archive.ubuntu.com/ubuntu bionic InRelease   
Get:7 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]           
Get:8 http://security.ubuntu.com/ubuntu bionic-security/main amd64 Packages [3018 kB]
Get:9 http://archive.ubuntu.com/ubuntu bionic-backports InRelease [83.3 kB]       
Get:10 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 Packages [3452 kB]
Get:11 http://security.ubuntu.com/ubuntu bionic-security/restricted amd64 Packages [1187 kB]
Get:12 http://security.ubuntu.com/ubuntu bionic-security/universe amd64 Packages [1551 kB]   
Get:13 http://archive.ubuntu.com/ubuntu bionic-updates/restricted amd64 Packages [1230 kB]   
Get:14 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 Packages [2329 kB]
Get:15 http://archive.ubuntu.com/ubuntu bionic-backports/universe amd64 Packages [20.6 kB]
Get:16 http://archive.ubuntu.com/ubuntu bionic-backports/main amd64 Packages [64.0 kB]
Fetched 13.3 MB in 2s (5857 kB/s)                          
Reading package lists... Done
Building dependency tree       
Reading state information... Done
32 packages can be upgraded. Run 'apt list --upgradable' to see them.
Canceled hold on kubeadm.
   kubeadm |  1.25.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.24.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.23.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.23.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.22.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.22.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.21.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.21.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.20.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.20.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.19.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.19.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.20-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.19-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.18-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.17-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.18.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.4-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.18.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.17-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.17.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.7-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.17.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.11-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.16.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.16.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.15.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.15.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.15.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.15.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.14.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.14.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.13.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.13.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.13.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.13.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.12.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.12.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.11.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.11.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm | 1.10.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.10.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.9.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.9.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.9.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.8.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.1-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.0-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.8.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.16-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.15-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.14-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.11-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.7.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.3-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.7.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.13-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.12-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.11-01 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.6.10-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.9-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.8-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.6-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.5-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.4-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.3-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.2-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.6.1-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |   1.5.7-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
Hit:1 https://packages.cloud.google.com/apt kubernetes-xenial InRelease                                                               
Hit:2 https://download.docker.com/linux/ubuntu bionic InRelease                                                                       
Hit:3 http://security.ubuntu.com/ubuntu bionic-security InRelease                                                                     
Hit:4 http://archive.ubuntu.com/ubuntu bionic InRelease
Hit:5 http://archive.ubuntu.com/ubuntu bionic-updates InRelease
Hit:6 http://archive.ubuntu.com/ubuntu bionic-backports InRelease
Reading package lists... Done                      
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 31 not upgraded.
Need to get 9003 kB of archives.
After this operation, 836 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.24.0-00 [9003 kB]
Fetched 9003 kB in 0s (61.1 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 15316 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.24.0-00_amd64.deb ...
Unpacking kubeadm (1.24.0-00) over (1.23.0-00) ...
Setting up kubeadm (1.24.0-00) ...

root@node01 ~ ➜  kubeadm version 
kubeadm version: &version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.0", GitCommit:"4ce5a8954017644c5420bae81d72b09b735c21f0", GitTreeState:"clean", BuildDate:"2022-05-03T13:44:24Z", GoVersion:"go1.18.1", Compiler:"gc", Platform:"linux/amd64"}

root@node01 ~ ➜  apt-mark hold kubeadm 
kubeadm set on hold.

root@node01 ~ ➜  apt-mark unhold kubelet kubectl 
Canceled hold on kubelet.
Canceled hold on kubectl.

root@node01 ~ ➜  apt-get install kubelet=1.24.0-00 kubectl=1.24.0-00
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be upgraded:
  kubectl kubelet
2 upgraded, 0 newly installed, 0 to remove and 30 not upgraded.
Need to get 28.6 MB of archives.
After this operation, 9032 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.24.0-00 [9316 kB]
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.24.0-00 [19.2 MB]
Fetched 28.6 MB in 1s (30.3 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 15316 files and directories currently installed.)
Preparing to unpack .../kubectl_1.24.0-00_amd64.deb ...
Unpacking kubectl (1.24.0-00) over (1.23.0-00) ...
Preparing to unpack .../kubelet_1.24.0-00_amd64.deb ...
/usr/sbin/policy-rc.d returned 101, not running 'stop kubelet.service'
Unpacking kubelet (1.24.0-00) over (1.23.0-00) ...
Setting up kubelet (1.24.0-00) ...
/usr/sbin/policy-rc.d returned 101, not running 'start kubelet.service'
Setting up kubectl (1.24.0-00) ...

root@node01 ~ ➜  sudo systemctl daemon-reload 

root@node01 ~ ➜  sudo systemctl restart kubelet.service 

root@node01 ~ ➜  exit
logout
Connection to node01 closed.

controlplane ~ ➜  kubectl uncordon node01
node/node01 uncordoned

controlplane ~ ➜  kubectl get nodes
NAME           STATUS   ROLES                  AGE    VERSION
controlplane   Ready    control-plane,master   141m   v1.24.0
node01         Ready    <none>                 140m   v1.24.0
