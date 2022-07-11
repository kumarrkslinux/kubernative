```
1. Identify the DNS solution implemented in this cluster?

coredns

root@controlplane ~ ➜  kubectl get pods -n kube-system 
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-74ff55c5b-6n7cp                1/1     Running   0          46m
coredns-74ff55c5b-8nnvd                1/1     Running   0          46m
etcd-controlplane                      1/1     Running   0          46m
kube-apiserver-controlplane            1/1     Running   0          46m
kube-controller-manager-controlplane   1/1     Running   0          46m
kube-flannel-ds-4ltzc                  1/1     Running   0          46m
kube-proxy-9ktj5                       1/1     Running   0          46m
kube-scheduler-controlplane            1/1     Running   0          46m

2. What is the name of the service created for accessing CoreDNS?

root@controlplane ~ ➜  kubectl get service -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   49m
  
3.What is the IP of the CoreDNS server that should be configured on PODs to resolve services?
 
root@controlplane ~ ➜  kubectl get service -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   49m
  
4. Where is the configuration file located for configuring the CoreDNS service?
root@controlplane ~ ➜  kubectl get deployments.apps  -n kube-system 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2     2            2           53m

root@controlplane ~ ➜  kubectl describe deployments.apps  coredns -n kube-system 
Name:                   coredns
Namespace:              kube-system
CreationTimestamp:      Mon, 11 Jul 2022 04:41:46 +0000
Labels:                 k8s-app=kube-dns
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               k8s-app=kube-dns
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 25% max surge
Pod Template:
  Labels:           k8s-app=kube-dns
  Service Account:  coredns
  Containers:
   coredns:
    Image:       k8s.gcr.io/coredns:1.7.0
    Ports:       53/UDP, 53/TCP, 9153/TCP
    Host Ports:  0/UDP, 0/TCP, 0/TCP
    Args:
      -conf
      /etc/coredns/Corefile
    Limits:
      memory:  170Mi
    Requests:
      cpu:        100m
      memory:     70Mi
    Liveness:     http-get http://:8080/health delay=60s timeout=5s period=10s #success=1 #failure=5
    Readiness:    http-get http://:8181/ready delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /etc/coredns from config-volume (ro)
  Volumes:
   config-volume:
    Type:               ConfigMap (a volume populated by a ConfigMap)
    Name:               coredns
    Optional:           false
  Priority Class Name:  system-cluster-critical
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   coredns-74ff55c5b (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  53m   deployment-controller  Scaled up replica set coredns-74ff55c5b to 2
  
 5. How is the Corefile passed in to the CoreDNS POD?
  root@controlplane ~ ➜  kubectl get configmaps -n kube-system 
NAME                                 DATA   AGE
coredns                              1      54m
extension-apiserver-authentication   6      55m
kube-flannel-cfg                     2      54m
kube-proxy                           2      54m
kube-root-ca.crt                     1      54m
kubeadm-config                       2      54m
kubelet-config-1.20                  1      54m
  
6. What is the root domain/zone configured for this kubernetes cluster?
root@controlplane ~ ➜  kubectl get configmaps -n kube-system coredns 
NAME      DATA   AGE
coredns   1      56m

root@controlplane ~ ➜  kubectl describe configmaps -n kube-system coredns 
Name:         coredns
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
====
Corefile:
----
.:53 {
    errors
    health {
       lameduck 5s
    }
    ready
    kubernetes cluster.local in-addr.arpa ip6.arpa {
       pods insecure
       fallthrough in-addr.arpa ip6.arpa
       ttl 30
    }
    prometheus :9153
    forward . /etc/resolv.conf {
       max_concurrent 1000
    }
    cache 30
    loop
    reload
    loadbalance
}

Events:  <none>


7. What name can be used to access the hr web server from the test Application?


You can execute a curl command on the test pod to test. Alternatively, the test Application also has a UI. Access it using the tab at the top of your terminal named test-app

web-service

root@controlplane ~ ➜  kubectl get pods
NAME                READY   STATUS    RESTARTS   AGE
hr                  1/1     Running   0          15m
simple-webapp-1     1/1     Running   0          14m
simple-webapp-122   1/1     Running   0          14m
test                1/1     Running   0          15m

root@controlplane ~ ➜  kubectl get svc
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        59m
test-service   NodePort    10.102.80.114   <none>        80:30080/TCP   16m
web-service    ClusterIP   10.99.54.205    <none>        80/TCP         16m


8. Which of the names CANNOT be used to access the HR service from the test pod?

web.service.default.pod

9. Which of the below name can be used to access the payroll service from the test application?
web-service.payroll

10. Which of the below name CANNOT be used to access the payroll service from the test application?
web-service.payroll.cluster


11. We just deployed a web server - webapp - that accesses a database mysql - server. However the web server is failing to connect to the database server. Troubleshoot and fix the issue.

They could be in different namespaces. First locate the applications. The web server interface can be seen by clicking the tab Web Server at the top of your terminal.

CheckCompleteIncomplete
Web Server: webapp

Uses the right DB_Host name


