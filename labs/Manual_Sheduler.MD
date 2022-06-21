root@controlplane ~ cat nginx.yaml 
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx


root@controlplane ~ ➜  kubectl apply -f nginx.yaml 
pod/nginx created

root@controlplane ~ ➜  cat nginx.yaml 

root@controlplane ~ ✖ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   0/1     Pending   0          20s

root@controlplane ~ ➜  kubectl describe pod nginx 
Name:         nginx
Namespace:    default
Priority:     0
Node:         <none>
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           
IPs:          <none>
Containers:
  nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-w8ljf (ro)
Volumes:
  kube-api-access-w8ljf:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>

root@controlplane ~ ➜  kubectl get pods --namespace kube-system
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-64897985d-2zwjz                1/1     Running   0          4m32s
coredns-64897985d-4f6pk                1/1     Running   0          4m32s
etcd-controlplane                      1/1     Running   0          4m48s
kube-apiserver-controlplane            1/1     Running   0          4m38s
kube-controller-manager-controlplane   1/1     Running   0          4m50s
kube-flannel-ds-fj2pb                  1/1     Running   0          3m44s
kube-flannel-ds-wmhrr                  1/1     Running   0          4m32s
kube-proxy-jppjv                       1/1     Running   0          4m32s
kube-proxy-vjh7r                       1/1     Running   0          3m44s
  
  
root@controlplane ~ ➜  ls
nginx.yaml  sample.yaml

root@controlplane ~ ➜  vim nginx.yaml 

root@controlplane ~ ➜  kubectl get nodes
NAME           STATUS   ROLES                  AGE     VERSION
controlplane   Ready    control-plane,master   8m11s   v1.23.0
node01         Ready    <none>                 7m      v1.23.0

root@controlplane ~ ➜  cat nginx.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx


root@controlplane ~ ➜  kubectl delete pod nginx 
pod "nginx" deleted

root@controlplane ~ ➜  kubectl apply -f nginx.yaml 
pod/nginx created

root@controlplane ~ ➜  kubectl get pods
NAME    READY   STATUS              RESTARTS   AGE
nginx   0/1     ContainerCreating   0          10s


root@controlplane ~ ➜  kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          15s
  
