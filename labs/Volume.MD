
```sh
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
        
Configure a volume to store these logs at /var/log/webapp on the host.  Use the spec provided below.

Name: webapp 
Image Name: 
kodekloud/event-simulator 
Volume HostPath: /var/log/webapp 
Volume Mount: /log
