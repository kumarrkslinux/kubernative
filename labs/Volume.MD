
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

kubectl get po webapp -o yaml > webapp.yaml 


apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    env:
    - name: LOG_HANDLERS
      value: file
    volumeMounts:
    - mountPath: /log
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: Directory

kubectl replace -f: - It will remove the existing resource and will replace it with the new one from the given manifest file.

root@controlplane ~ kubectl replace -f webapp.yaml --force
pod "webapp" deleted
pod/webapp replaced
