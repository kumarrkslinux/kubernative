root@controlplane ~ ➜  kubectl get role
No resources found in default namespace.

root@controlplane ~ ➜  kubectl get role -n kube-system 
NAME                                             CREATED AT
extension-apiserver-authentication-reader        2022-06-21T05:04:46Z
kube-proxy                                       2022-06-21T05:04:49Z
kubeadm:kubelet-config-1.23                      2022-06-21T05:04:47Z
kubeadm:nodes-kubeadm-config                     2022-06-21T05:04:47Z
system::leader-locking-kube-controller-manager   2022-06-21T05:04:46Z
system::leader-locking-kube-scheduler            2022-06-21T05:04:46Z
system:controller:bootstrap-signer               2022-06-21T05:04:46Z
system:controller:cloud-provider                 2022-06-21T05:04:46Z
system:controller:token-cleaner                  2022-06-21T05:04:46Z

root@controlplane ~ ➜  kubectl get role --all-namespaces 
NAMESPACE     NAME                                             CREATED AT
blue          developer                                        2022-06-21T05:07:57Z
kube-public   kubeadm:bootstrap-signer-clusterinfo             2022-06-21T05:04:48Z
kube-public   system:controller:bootstrap-signer               2022-06-21T05:04:46Z
kube-system   extension-apiserver-authentication-reader        2022-06-21T05:04:46Z
kube-system   kube-proxy                                       2022-06-21T05:04:49Z
kube-system   kubeadm:kubelet-config-1.23                      2022-06-21T05:04:47Z
kube-system   kubeadm:nodes-kubeadm-config                     2022-06-21T05:04:47Z
kube-system   system::leader-locking-kube-controller-manager   2022-06-21T05:04:46Z
kube-system   system::leader-locking-kube-scheduler            2022-06-21T05:04:46Z
kube-system   system:controller:bootstrap-signer               2022-06-21T05:04:46Z
kube-system   system:controller:cloud-provider                 2022-06-21T05:04:46Z
kube-system   system:controller:token-cleaner                  2022-06-21T05:04:46Z
