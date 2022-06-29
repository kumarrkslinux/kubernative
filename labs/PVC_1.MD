Create a new pod called nginx with the image nginx:alpine. The Pod should make use of the PVC local-pvc and mount the volume at the path /var/www/html.

The PV local-pv should in a bound state.

Pod created with the correct Image?
Pod uses PVC called local-pvc?
local-pv bound?
nginx pod running?
Volume mounted at the correct path?

controlplane ~ ➜  kubectl apply -f pod.yml 
pod/nginx created

controlplane ~ ➜  cat pod.yml 
---
apiVersion: v1
kind: Pod
metadata: 
  name: nginx
  labels:
    name: nginx
spec: 
  containers:
  - name: nginx
    image: nginx:alpine
    volumeMounts: 
      - name: local-persisten-storage
        mountPath: /var/www/html
  volumes:
   - name: local-persisten-storage
     persistentVolumeClaim:
       claimName: local-pv
