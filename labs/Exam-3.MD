### Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Next upgrade the deployment to version 1.17 using rolling update.

### Image: nginx:1.16

### Task: Upgrade the version of the deployment to 1:17

Weight: 12 

``` 
controlplane ~/CKA ➜  kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1 
deployment.apps/nginx-deploy created

controlplane ~/CKA ➜  kubectl get deployments.apps 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
gold-nginx     1/1     1            1           58m
nginx-deploy   1/1     1            1           10s

controlplane ~/CKA ➜  kubectl get pods 
NAME                            READY   STATUS    RESTARTS   AGE
gold-nginx-7cf65dbf6d-m8ktt     1/1     Running   0          44m
nginx-deploy-7cfd4975d6-q9b57   1/1     Running   0          19s

controlplane ~/CKA ➜  kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.16.1

controlplane ~ ✖ kubectl rollout status deployment/nginx-deploy 
deployment "nginx-deploy" successfully rolled out
``` 

### A new deployment called alpha-mysql has been deployed in the alpha namespace. However, the pods are not running. Troubleshoot and fix the issue. The deployment should make use of the persistent volume alpha-pv to be mounted at /var/lib/mysql and should use the environment variable MYSQL_ALLOW_EMPTY_PASSWORD=1 to make use of an empty root password.

### Important: Do not alter the persistent volume.

### Troubleshoot and fix the issues

Weight: 20

``` 
controlplane ~ ➜  kubectl get pods -n alpha 
NAME                           READY   STATUS    RESTARTS   AGE
alpha-mysql-56bdcdf8f7-zbp7h   0/1     Pending   0          30m

controlplane ~ ➜  kubectl describe pod alpha-mysql-56bdcdf8f7-zbp7h -n alpha 
Name:           alpha-mysql-56bdcdf8f7-zbp7h
Namespace:      alpha
Priority:       0
Node:           <none>
Labels:         app=alpha-mysql
                pod-template-hash=56bdcdf8f7
Annotations:    <none>
Status:         Pending
IP:             
IPs:            <none>
Controlled By:  ReplicaSet/alpha-mysql-56bdcdf8f7
Containers:
  mysql:
    Image:      mysql:5.6
    Port:       3306/TCP
    Host Port:  0/TCP
    Environment:
      MYSQL_ALLOW_EMPTY_PASSWORD:  1
    Mounts:
      /var/lib/mysql from mysql-data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-q4qwd (ro)
Conditions:
  Type           Status
  PodScheduled   False 
Volumes:
  mysql-data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  mysql-alpha-pvc
    ReadOnly:   false
  kube-api-access-q4qwd:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  27m   default-scheduler  0/2 nodes are available: 2 persistentvolumeclaim "mysql-alpha-pvc" not found. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
  Warning  FailedScheduling  22m   default-scheduler  0/2 nodes are available: 2 persistentvolumeclaim "mysql-alpha-pvc" not found. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
  Warning  FailedScheduling  30m   default-scheduler  0/2 nodes are available: 2 persistentvolumeclaim "mysql-alpha-pvc" not found. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.

controlplane ~ ➜  kubectl get pv -n alpha 
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
alpha-pv   1Gi        RWO            Retain           Available           slow                    31m

controlplane ~ ➜  kubectl get pvc -n alpha 
NAME          STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
alpha-claim   Pending                                      slow-storage   31m
