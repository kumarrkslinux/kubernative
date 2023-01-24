1. Get the current context and save into file and take all the context detail and save into file with kubectl.     

 ```
[root@master ~]# kubectl config current-context 
kubernetes-admin@cluster.local
[root@master ~]# kubectl config get-contexts 
CURRENT   NAME                             CLUSTER         AUTHINFO           NAMESPACE
*         kubernetes-admin@cluster.local   cluster.local   kubernetes-admin   
```
2. Create a single pod of image httpd:2.4.41-alpine in Namespace default. The Pod should be named pod1 and the container should be named pod1-conatners. 
   The pod should be scheduled on a master node, do not add new lables any nodes 
   Shorlt write why PODS are by default not sheduled on master nodes into /tmp/pod/reason.txt 

```
[root@master ~]# kubectl run pod1 --image=httpd:2.4.41-alpine --dry-run=client -o yaml > 2.yaml

[root@master ~]# cat 2.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod1
  name: pod1
spec:
  nodeName: master
  containers:
  - image: httpd:2.4.41-alpine
    name: pod1-container
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
[root@master ~]# kubectl apply -f 2.yaml 
pod/pod1 created
# kubectl get pods



