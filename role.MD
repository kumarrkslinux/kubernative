```sh
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

root@controlplane ~  kubectl describe role kube-proxy -n kube-system 
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]
  
 root@controlplane ~ ➜  kubectl describe rolebindings.rbac.authorization.k8s.io kube-proxy -n kube-system 
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  kube-proxy
Subjects:
  Kind   Name                                             Namespace
  ----   ----                                             ---------
  Group  system:bootstrappers:kubeadm:default-node-token
  
root@controlplane ~ ➜  kubectl get list --as dev-user
Error from server (Forbidden): lists is forbidden: User "dev-user" cannot list resource "lists" in API group "" at the cluster scope
```sh

```sh
To create a Role:- 
kubectl create role developer --namespace=default --verb=list,create,delete --resource=pods
To create a RoleBinding:- 
kubectl create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user
```sh 

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create","delete"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
