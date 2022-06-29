```sh
Create a Persistent Volume with the given specification.

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

After change the AccessMode to Many: 

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
