```sh
1. Create a Persistent Volume with the given specification.

Volume Name: pv-log
Storage: 100Mi
Access Modes: ReadWriteMany
Host Path: /pv/log
Reclaim Policy: Retain

# kubectl apply -f pv.yml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
   persistentVolumeReclaimPolicy: Retain
   accessModes:
     - ReadWriteMany
   capacity:
     storage: 100Mi
   hostPath:
     path: /pv/log

root@controlplane ~ ➜  kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Available                                   4m5s

root@controlplane ~ ➜  cat pvc.yml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
   accessModes:
     - ReadWriteOnce
   resources:
     requests: 
       storage: 50Mi

root@controlplane ~ ✖ kubectl apply -f pvc.yml 
persistentvolumeclaim/claim-log-1 created

root@controlplane ~ ➜  kubectl get pv,pvc
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
persistentvolume/pv-log   100Mi      RWX            Retain           Available                                   6m57s

NAME                                STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/claim-log-1   Pending                                                     2m26s

2. After change the AccessMode to Many: 

root@controlplane ~ ➜  cat pvc.yml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
   accessModes:
     - ReadWriteMany
   resources:
     requests: 
       storage: 50Mi
       
root@controlplane ~ ➜  kubectl delete pvc claim-log-1 
persistentvolumeclaim "claim-log-1" deleted

root@controlplane ~ ➜  kubectl apply -f pvc.yml 
persistentvolumeclaim/claim-log-1 created

root@controlplane ~ ➜  kubectl get pv,pvc
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
persistentvolume/pv-log   100Mi      RWX            Retain           Bound    default/claim-log-1                           9m10s

NAME                                STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/claim-log-1   Bound    pv-log   100Mi      RWX                           23s



3. Update the webapp pod to use the persistent volume claim as its storage.

Replace hostPath configured earlier with the newly created PersistentVolumeClaim.

CheckCompleteIncomplete
Name: webapp
Image Name: kodekloud/event-simulator
Volume: PersistentVolumeClaim=claim-log-1
Volume Mount: /log


root@controlplane ~ ➜  cat webapp.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: webapp
  namespace: default
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    name: event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume
  volumes:
  - name: log-volume
    persistentVolumeClaim:
      claimName: claim-log-1
      
root@controlplane ~ ✖ kubectl replace -f webapp.yaml --force 
pod "webapp" deleted
pod/webapp replaced

4. Once PVC is deleted, Pv is available and can not be used 
root@controlplane ~ ➜  kubectl delete pvc claim-log-1 
persistentvolumeclaim "claim-log-1" deleted

root@controlplane ~ ➜  kubectl get pvc
NAME          STATUS        VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Terminating   pv-log   100Mi      RWX                           9m46s

root@controlplane ~ ➜  kubectl delete pod webapp 
pod "webapp" deleted

root@controlplane ~ ➜  kubectl get pvc
No resources found in default namespace.

root@controlplane ~ ➜  kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                 STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Released   default/claim-log-1                           20m
