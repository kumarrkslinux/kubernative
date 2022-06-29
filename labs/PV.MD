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
