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



