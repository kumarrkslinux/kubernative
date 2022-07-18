```html

https://kubernetes.github.io/ingress-nginx/ - example and troubleshoot

1. Which namespace is the Ingress Controller deployed in? ingress-nginx

root@controlplane ~ ➜  kubectl get all -A
NAMESPACE       NAME                                            READY   STATUS      RESTARTS   AGE
app-space       pod/default-backend-7f87f4c484-jclm2            1/1     Running     0          3m47s
app-space       pod/webapp-video-d54b764b6-tz5xs                1/1     Running     0          3m48s
app-space       pod/webapp-wear-5bb44cf565-vd5sv                1/1     Running     0          3m48s
ingress-nginx   pod/ingress-nginx-admission-create-hdlp2        0/1     Completed   0          3m45s
ingress-nginx   pod/ingress-nginx-admission-patch-sgr82         0/1     Completed   0          3m45s
ingress-nginx   pod/ingress-nginx-controller-6b79d8694c-swc2m   1/1     Running     0          3m45s
kube-system     pod/coredns-64897985d-k2wpj                     1/1     Running     0          16m
kube-system     pod/coredns-64897985d-qtlbg                     1/1     Running     0          16m
kube-system     pod/etcd-controlplane                           1/1     Running     0          16m
kube-system     pod/kube-apiserver-controlplane                 1/1     Running     0          16m
kube-system     pod/kube-controller-manager-controlplane        1/1     Running     0          16m
kube-system     pod/kube-flannel-ds-zz866                       1/1     Running     0          16m
kube-system     pod/kube-proxy-hfh9v                            1/1     Running     0          16m
kube-system     pod/kube-scheduler-controlplane                 1/1     Running     0          16m

NAMESPACE       NAME                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
app-space       service/default-backend-service              ClusterIP   10.102.91.209    <none>        80/TCP                       3m47s
app-space       service/video-service                        ClusterIP   10.107.21.121    <none>        8080/TCP                     3m48s
app-space       service/wear-service                         ClusterIP   10.101.165.234   <none>        8080/TCP                     3m48s
default         service/kubernetes                           ClusterIP   10.96.0.1        <none>        443/TCP                      16m
ingress-nginx   service/ingress-nginx-controller             NodePort    10.110.227.86    <none>        80:30080/TCP,443:32103/TCP   3m45s
ingress-nginx   service/ingress-nginx-controller-admission   ClusterIP   10.103.116.131   <none>        443/TCP                      3m45s
kube-system     service/kube-dns                             ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP       16m

NAMESPACE     NAME                             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/kube-flannel-ds   1         1         1       1            1           <none>                   16m
kube-system   daemonset.apps/kube-proxy        1         1         1       1            1           kubernetes.io/os=linux   16m

NAMESPACE       NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
app-space       deployment.apps/default-backend            1/1     1            1           3m48s
app-space       deployment.apps/webapp-video               1/1     1            1           3m48s
app-space       deployment.apps/webapp-wear                1/1     1            1           3m48s
ingress-nginx   deployment.apps/ingress-nginx-controller   1/1     1            1           3m45s
kube-system     deployment.apps/coredns                    2/2     2            2           16m

NAMESPACE       NAME                                                  DESIRED   CURRENT   READY   AGE
app-space       replicaset.apps/default-backend-7f87f4c484            1         1         1       3m47s
app-space       replicaset.apps/webapp-video-d54b764b6                1         1         1       3m48s
app-space       replicaset.apps/webapp-wear-5bb44cf565                1         1         1       3m48s
ingress-nginx   replicaset.apps/ingress-nginx-controller-6b79d8694c   1         1         1       3m45s
kube-system     replicaset.apps/coredns-64897985d                     2         2         2       16m

NAMESPACE       NAME                                       COMPLETIONS   DURATION   AGE
ingress-nginx   job.batch/ingress-nginx-admission-create   1/1           20s        3m45s
ingress-nginx   job.batch/ingress-nginx-admission-patch    1/1           21s        3m45s
  
  
2. What is the name of the Ingress Controller Deployment?
  ingress-nginx-controller
  
root@controlplane ~ ➜  kubectl get deploy -A
NAMESPACE       NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
app-space       default-backend            1/1     1            1           6m49s
app-space       webapp-video               1/1     1            1           6m49s
app-space       webapp-wear                1/1     1            1           6m49s
ingress-nginx   ingress-nginx-controller   1/1     1            1           6m46s
kube-system     coredns                    2/2     2            2           19m
  
3. Which namespace is the APP deployed in?
  
  root@controlplane ~ ➜  kubectl get po -A
NAMESPACE       NAME                                        READY   STATUS      RESTARTS   AGE
app-space       default-backend-7f87f4c484-jclm2            1/1     Running     0          7m58s
app-space       webapp-video-d54b764b6-tz5xs                1/1     Running     0          7m59s
app-space       webapp-wear-5bb44cf565-vd5sv                1/1     Running     0          7m59s
ingress-nginx   ingress-nginx-admission-create-hdlp2        0/1     Completed   0          7m56s
ingress-nginx   ingress-nginx-admission-patch-sgr82         0/1     Completed   0          7m56s
ingress-nginx   ingress-nginx-controller-6b79d8694c-swc2m   1/1     Running     0          7m56s
kube-system     coredns-64897985d-k2wpj                     1/1     Running     0          20m
kube-system     coredns-64897985d-qtlbg                     1/1     Running     0          20m
kube-system     etcd-controlplane                           1/1     Running     0          20m
kube-system     kube-apiserver-controlplane                 1/1     Running     0          20m
kube-system     kube-controller-manager-controlplane        1/1     Running     0          20m
kube-system     kube-flannel-ds-zz866                       1/1     Running     0          20m
kube-system     kube-proxy-hfh9v                            1/1     Running     0          20m
kube-system     kube-scheduler-controlplane                 1/1     Running     0          20m
  
 4. How many applications are deployed in the app-space namespace?
Count the number of deployments in this namespace. 3 
  
 root@controlplane ~ ➜  kubectl get deploy --namespace app-space
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
default-backend   1/1     1            1           9m11s
webapp-video      1/1     1            1           9m11s
webapp-wear       1/1     1            1           9m11s

  5. Which namespace is the Ingress Resource deployed in?
  
root@controlplane ~ ➜  kubectl get ingress --all-namespaces
NAMESPACE   NAME                 CLASS    HOSTS   ADDRESS         PORTS   AGE
app-space   ingress-wear-watch   <none>   *       10.110.227.86   80      11m
  
  6. What is the Host configured on the Ingress Resource?
The host entry defines the domain name that users use to reach the application like www.google.com
  
  root@controlplane ~ ➜  kubectl describe ingress -n app-space ingress-wear-watch 
Name:             ingress-wear-watch
Labels:           <none>
Namespace:        app-space
Address:          10.110.227.86
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /wear    wear-service:8080 (10.244.0.4:8080)
              /watch   video-service:8080 (10.244.0.6:8080)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
              nginx.ingress.kubernetes.io/ssl-redirect: false
Events:
  Type    Reason  Age                From                      Message
  ----    ------  ----               ----                      -------
  Normal  Sync    12m (x2 over 12m)  nginx-ingress-controller  Scheduled for sync
  
  7. If the requirement does not match any of the configured paths what service are the requests forwarded to?
      From the above output default-backend-service
  
 8.  You are requested to change the URLs at which the applications are made available.

      Make the video application available at /stream.

      1.Ingress: ingress-wear-watch

      2. Path: /stream

      3. Backend Service: video-service

      4. Backend Service Port: 8080
  
  Solution manifest file to change the path to the video streaming application to /stream as follows:
  kubectl edit ingress -n app-space and change the value
  Or 
  apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
  name: ingress-wear-watch
  namespace: app-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port: 
              number: 8080
        path: /wear
        pathType: Prefix
      - backend:
          service:
            name: video-service
            port: 
              number: 8080
        path: /stream
        pathType: Prefix

  9. You are requested to add a new path to your ingress to make the food delivery application available to your customers.

    Make the new application available at /eat.

    1. Ingress: ingress-wear-watch

    2.Path: /eat

    3.Backend Service: food-service

    4. Backend Service Port: 8080
  
  kubectl edit ingress -n app-space and change the value
  - backend:
            service:
              name: food-service
              port:
                number: 8080
          path: /eat
          pathType: Prefix
  
  or 
  
  Solution manifest file to add a new path to our ingress service to make the application available at /eat as follows:
  apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
  name: ingress-wear-watch
  namespace: app-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port: 
              number: 8080
        path: /wear
        pathType: Prefix
      - backend:
          service:
            name: video-service
            port: 
              number: 8080
        path: /stream
        pathType: Prefix
      - backend:
          service:
            name: food-service
            port: 
              number: 8080
        path: /eat
        pathType: Prefix

  10. A new payment service has been introduced. Since it is critical, the new application is deployed in its own namespace.
  Identify the namespace in which the new application is deployed. ? critical-space   
  root@controlplane ~ ✖ kubectl get deployments.apps -A
NAMESPACE        NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
app-space        default-backend            1/1     1            1           30m
app-space        webapp-food                1/1     1            1           3m47s
app-space        webapp-video               1/1     1            1           30m
app-space        webapp-wear                1/1     1            1           30m
critical-space   webapp-pay                 1/1     1            1           27s
ingress-nginx    ingress-nginx-controller   1/1     1            1           30m
kube-system      coredns                    2/2     2            2           42m
  
11. You are requested to make the new application available at /pay.
Create a new Ingress for the new pay application in the critical-space namespace.
Use the command kubectl get svc -n critical-space to know the service and port details.

  kubectl create ingress ingress-pay -n critical-space --rules="/pay=pay-service:8282"
  and add annotations 
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  or 
  
  root@controlplane ~ ✖ kubectl get svc -n critical-space
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
pay-service   ClusterIP   10.105.98.228   <none>        8282/TCP   9m35s
    
Identify and implement the best approach to making this application available on the ingress controller and test to make sure its working. Look into annotations: rewrite-target as well.

   1. Ingress Created

   2. Path: /pay

   3. Configure correct backend service

   4.Configure correct backend port
  
 root@controlplane ~ ➜  cat ingr.yml
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
  name: newapplication
  namespace: critical-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: pay-service
            port:
              number: 8282
        path: /pay
        pathType: Prefix
  
  you can try this way 
  # echo ' --------
  #------' | kubectl create -f -
root@controlplane ~ ➜  kubectl  apply -f  ingr.yml 
ingress.networking.k8s.io/newapplication created
  
  
  created namespace,configobject,service,account and Roles and RoleBindings for the ServiceAccount done by trainer. 
  
  root@controlplane ~ ➜  kubectl get deployments.apps -A
NAMESPACE     NAME              READY   UP-TO-DATE   AVAILABLE   AGE
app-space     default-backend   1/1     1            1           2m25s
app-space     webapp-video      1/1     1            1           2m25s
app-space     webapp-wear       1/1     1            1           2m25s
kube-system   coredns           2/2     2            2           31m

root@controlplane ~ ➜  kubectl create namespace ingress-space
namespace/ingress-space created

root@controlplane ~ ➜  kubectl create configmap nginx-configuration -n ingress-space 
configmap/nginx-configuration created

root@controlplane ~ ➜  kubectl create serviceaccount ingress-serviceaccount -n ingress-space 
serviceaccount/ingress-serviceaccount created

root@controlplane ~ ➜  kubectl get roles -n ingress-space 
NAME           CREATED AT
ingress-role   2022-07-18T05:27:49Z

root@controlplane ~ ➜  kubectl describe roles ingress-role -n ingress-space 
Name:         ingress-role
Labels:       app.kubernetes.io/name=ingress-nginx
              app.kubernetes.io/part-of=ingress-nginx
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names                     Verbs
  ---------   -----------------  --------------                     -----
  configmaps  []                 []                                 [get create]
  configmaps  []                 [ingress-controller-leader-nginx]  [get update]
  endpoints   []                 []                                 [get]
  namespaces  []                 []                                 [get]
  pods        []                 []                                 [get]
  secrets     []                 []                                 [get]     

root@controlplane ~ ➜  kubectl describe rolebindings.rbac.authorization.k8s.io -n ingress-space 
Name:         ingress-role-binding
Labels:       app.kubernetes.io/name=ingress-nginx
              app.kubernetes.io/part-of=ingress-nginx
Annotations:  <none>
Role:
  Kind:  Role
  Name:  ingress-role
Subjects:
  Kind            Name                    Namespace
  ----            ----                    ---------
  ServiceAccount  ingress-serviceaccount  
