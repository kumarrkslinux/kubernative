#### Upgrade the current version of kubernetes from 1.23.0 to 1.24.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the controlplane node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.

#### Upgrade controlplane node first and drain node node01 before upgrading it. Pods for gold-nginx should run on the controlplane node subsequently.

weight 15
#### Cluster Upgraded? 

#### pods 'gold-nginx' running on controlplane?


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

controlplane ~ ➜   apt-mark unhold kubeadm
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

controlplane ~ ➜  apt-mark hold kubelet kubectl
kubelet set on hold.
kubectl set on hold.


controlplane ~ ➜  systemctl daemon-reload 
