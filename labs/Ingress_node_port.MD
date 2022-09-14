
```
~/k8s$ cat deploy.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80

~/k8s$ cat Node_port_service.yml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
  - name: http
    port: 80
    targetPort: 80
    nodePort: 30036
    protocol: TCP
      
~/k8s$ cat Ingress.yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  name: kumartestdemo
spec:
  rules:
  - host: kumartest.apps.nsk.kumar.net
    http:
      paths:
      - backend:
          serviceName: my-nodeport-service
          servicePort: 443
        path: /
```

``` 
~/k8s$ kubectl get deployments -o wide
NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES   SELECTOR
kumartest          1/1     1            1           41d   nginx        nginx    app=kumartest
nginx-deployment   3/3     3            3           22h   nginx        nginx    app=nginx
webapp             1/1     1            1           18d   nginx        nginx    app=webapp

~/k8s$ kubectl get nodes
NAME                                   STATUS                     ROLES    AGE   VERSION
2a8a2661-4c52-4157-bc2f-f20aed7df3a3   Ready,SchedulingDisabled   <none>   42d   v1.19.9
44835d1b-c202-402a-a6e4-4ddf0ede73e0   Ready                      <none>   42d   v1.19.9
6f39c451-20b3-4c72-8b8e-8faa0886fe8e   Ready                      <none>   42d   v1.19.9

~/k8s$ kubectl get po -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP             NODE                                   NOMINATED NODE   READINESS GATES
kumartest-8658c58f96-zm792          1/1     Running   0          41d   10.100.37.18   44835d1b-c202-402a-a6e4-4ddf0ede73e0   <none>           <none>
nginx-deployment-7848d4b86f-kf45p   1/1     Running   0          22h   10.100.7.25    6f39c451-20b3-4c72-8b8e-8faa0886fe8e   <none>           <none>
nginx-deployment-7848d4b86f-rnfmm   1/1     Running   0          22h   10.100.37.40   44835d1b-c202-402a-a6e4-4ddf0ede73e0   <none>           <none>
nginx-deployment-7848d4b86f-wtqdf   1/1     Running   0          22h   10.100.37.39   44835d1b-c202-402a-a6e4-4ddf0ede73e0   <none>           <none>
webapp-5654c984c-57zv6              1/1     Running   0          18d   10.100.37.29   44835d1b-c202-402a-a6e4-4ddf0ede73e0   <none>           <none>

~/k8s$ kubectl get svc
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
my-nodeport-service   NodePort    10.110.2.168   <none>        80:30036/TCP   21h
nginx                 NodePort    10.110.2.72    <none>        80:32425/TCP   18d
web-svc               ClusterIP   10.110.2.251   <none>        80/TCP         18d

~/k8s$ kubectl get ing -o wide
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
NAME            CLASS    HOSTS                            ADDRESS         PORTS   AGE
kumartestdemo   <none>   kumartest.apps.nsk.kumar.net   10.1.1.1   80      20h

curl http://10.100.37.39:30036
curl http://kumartest.apps.nsk.kumar.net:30036

``` 
