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

3. There are 2 pods named 03db-* in in project-c13 namespace. C13 managemnt asked you to scale the pods down to one replica to save resources. record the action 

```
kubectl get pods all -n project-c13 
kubectl edit StatefulSets 03db01 -n procject-c13 -- Change into 1
``
4. There are various pods in all namespaces write a command into /tmm/creation_time.txt which list all pods sorted by their AGE

``` 
kubectl get pod -A --sort-by .metadata.creationTimestamp
kubectl get pod -A --sort-by .metadata.uid
```

5. Create persistenVolume name safari-pv. it should have a capacity of 2Gi, accessMode, ReadWriteOnce, HostPath /volume/Data and staorageClassName defained.. Next create a new PersistntVolumeClaim(safari-pvc) in namespace project-tiger.. Name safari-pvc.. It shoud request 2Gi storage, access mode ReadWriteOnce and should not define a storageClassName. The PVC should bound to the PV correctly. Create a new deployment safari ain namespace project-tiger, Wich mountes volume at /tmp/safari-data. The pods of the deployment  should be useimage httpd:2.4.41-alpine

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: safari-pv
spec:
  capacity:
    storage: 2Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle

---------------------------------------------
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: safari-pvc
  namespace: project-tiger
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 2Gi

----------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: tiger
  name: tiger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tiger
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: tiger
    spec:
      containers:
      - image: httpd:2.4.41-alpine
        name: nginx
        volumeMounts:
      - mountPath: "/tmp/safari-data"
        name: mypd
    volumes:
      - name: mypd
        persistentVolumeClaim:
          claimName: safari-pvc
```

6. The metrix server has not been installed yet in the cluster but its something that should be done soon, You colleage would alreay like to know the kubectl command to 
   
      - show nodes resource usage 
      - Show pods and their container resource usuage 

```

# kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# kubectl -n kube-system get pods

# [root@master ~]# kubectl top nodes
NAME     CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
master   405m         25%    3017Mi          94%       
node1    286m         17%    2271Mi          97%       
[root@master ~]# kubectl top pods
NAME                           CPU(cores)   MEMORY(bytes)   
audit-web-app                  0m           2Mi             
dev-pod-nginx                  0m           2Mi             
my-nginx-pod                   0m           2Mi             
pod-xfyub123                   3m           10Mi            
pod1                           1m           5Mi             
prod-pod-nginx                 0m           2Mi             
staic-pod-01-master            0m           6Mi             
testbod                        0m           0Mi             
we-pod                         0m           2Mi             
we-pod-fsbb9                   0m           2Mi             
we-pod-n2985                   0m           2Mi             
web-hook-84897f6f7c-5v6d8      0m           7Mi             
web-hook-84897f6f7c-7xv84      0m           2Mi             
web-hook-84897f6f7c-js8m7      0m           2Mi             
web-projec01-fb76d5cdd-44xhl   0m           3Mi             
``

7. Check how the master compoenets , kubelet,kube-apiserver,kube-scheduler,kuber-controller-manager and etcd are started/installed on the master ndoe and also find out the name of the DNS application how it started/installed on the master node.  [staticpod/Not installed/process/pod]

kubectl get pods -n kuber-system 


8. Temproarily stop the kube-scheduler this mean in a way that we an start it again afterwards. create a pod named manual-sheduler of image nginx  make sure it is not scheduled.  Manually assign the name in the in the yaml with second node name and make sure its running. 

cd /etc/kubernetes/manifest; mv /etc/kubernetes/manifest/kube-scheduler.yml /root/ 

[root@master ~]# cat manualschedule-pod.yml 
apiVersion: v1
kind: Pod
metadata:
  labels:
  name: manualschedule-pod
spec:
  nodeName: node01
  containers:
  - image: nginx
    name: manualschedule-pod


9. Create a new ServiceAccount processor in Namespace project-hamster. Create a Role and Rolebinding, the both named processor as well. These should allow the new SA to only create Screts and ConfigMaos in that Namespace. 

[root@master manifests]# kubectl create namespace project-hamaster
namespace/project-hamaster created

[root@master manifests]# kubectl create serviceaccount processor
serviceaccount/processor created

[root@master manifests]# kubectl create role processor --verb=create --resource=secrets,configmaps -n project-hamaster 
role.rbac.authorization.k8s.io/processor created

[root@master manifests]# kubectl create rolebinding processor --role=processor --serviceaccount=project-hamaster:processor -n project-hamaster 
rolebinding.rbac.authorization.k8s.io/processor created

[root@master manifests]# kubectl auth can-i create secrets --as system:serviceaccount:project-hamaster:processor  -n project-hamaster
yes
[root@master manifests]# kubectl auth can-i create configmaps --as system:serviceaccount:project-hamaster:processor  -n project-hamaster
yes
[root@master manifests]# kubectl auth can-i list configmaps --as system:serviceaccount:project-hamaster:processor  -n project-hamaster
no
[root@master manifests]# kubectl auth can-i list secrets --as system:serviceaccount:project-hamaster:processor  -n project-hamaster
no

10. Create daemonset names dsimportent with images httpd:2.4-alpine and labels id=ds-important and uid=1842kska-6789-7890-3567-d878798878. The pod it creates should request 10milicore cpu and 10 mebibyte memeory. Pod of the Damonset should run on all the srevers 

[root@master ~]# kubectl get daemonsets.apps -o wide
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE   CONTAINERS     IMAGES             SELECTOR
dsimportent   2         2         2       2            2           <none>          40s   ds-importent   httpd:2.4-alpine   name=ds-importent

[root@master ~]# kubectl get pods -o wide 
NAME                READY   STATUS    RESTARTS   AGE   IP              NODE     NOMINATED NODE   READINESS GATES
dsimportent-bsgx6   1/1     Running   0          50s   10.233.90.246   node1    <none>           <none>
dsimportent-wbrjz   1/1     Running   0          50s   10.233.70.52    master   <none>           <none>


apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: dsimportent
  namespace: 
  labels:
    id: ds-importent
    #    uuid: 
spec:
  selector:
    matchLabels:
      name: ds-importent
  template:
    metadata:
      labels:
        name: ds-importent
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
      containers:
      - name: ds-importent
        image: httpd:2.4-alpine
        resources:
          limits:
            memory: 10Mi
            #  cpu: 10mi
          requests:
            cpu: 10m
            #  memory: 10Mi
11. How can find out what is the servcice CIDR 
    
[root@master ~]# kubectl describe pods kube-controller-manager-master -n kube-system  |grep service-cluster
      --service-cluster-ip-range=10.233.0.0/18
      
[root@master ~]# kubectl get svc -A
NAMESPACE     NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default       audi-web-app-service   NodePort    10.233.61.144   <none>        8080:30002/TCP           3d
default       front-end-svc          NodePort    10.233.9.44     <none>        80:32017/TCP             15d
default       kubernetes             ClusterIP   10.233.0.1      <none>        443/TCP                  19d
default       newsericenode          NodePort    10.233.50.67    <none>        80:30723/TCP             14d
default       web-pod-svc            ClusterIP   10.233.28.212   <none>        80/TCP                   4d23h
kube-system   coredns                ClusterIP   10.233.0.3      <none>        53/UDP,53/TCP,9153/TCP   19d
kube-system   metrics-server         ClusterIP   10.233.13.239   <none>        443/TCP                  14d
