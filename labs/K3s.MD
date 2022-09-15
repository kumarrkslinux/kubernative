### k3s installtion:

### Master 
``` 
curl -sfL https://get.k3s.io | sh -
```
```
### client 
curl -sfL https://get.k3s.io |K3S_URL=https://192.168.1.10:6443 K3S_TOKEN=K10817f8bf7c8f2f1bec2e81dccf81b6dd61a812d96e4af2c194140cbf06217e3ad::server:5bfba1c3694b64b8794d50df0d79cb90 sh -
```
[root@k8smaster ~]# kubectl get nodes
NAME                       STATUS   ROLES                  AGE   VERSION
worknode.k8s.com           Ready    <none>                 26m   v1.24.4+k3s1
k8smaster.rockylinux.com   Ready    control-plane,master   44h   v1.24.4+k3s1

[root@k8smaster ~]# kubectl create deployment nginx --image=nginx
deployment.apps/nginx created

[root@k8smaster ~]# kubectl get pod -l app=nginx
NAME                    READY   STATUS    RESTARTS   AGE
nginx-8f458dc5b-6jbpj   1/1     Running   0          5m41s

# Retrieve the full name of the nginx pod:
[root@k8smaster ~]# POD_NAME=$(kubectl get pod -l app=nginx -o jsonpath="{.items[0].metadata.name}")

[root@k8smaster ~]# echo $POD_NAME
nginx-8f458dc5b-6jbpj

[root@k8smaster ~]# kubectl port-forward $POD_NAME 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80

[root@k8smaster ~]# bg
[1]+ kubectl port-forward $POD_NAME 8080:80 &

[root@k8smaster ~]# curl --head http://127.0.0.1:8080
Handling connection for 8080
HTTP/1.1 200 OK
Server: nginx/1.23.1
Date: Thu, 15 Sep 2022 07:03:51 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Tue, 19 Jul 2022 14:05:27 GMT
Connection: keep-alive
ETag: "62d6ba27-267"
Accept-Ranges: bytes

[root@k8smaster ~]# kubectl logs $POD_NAME
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/09/15 06:30:27 [notice] 1#1: using the "epoll" event method
2022/09/15 06:30:27 [notice] 1#1: nginx/1.23.1
2022/09/15 06:30:27 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2022/09/15 06:30:27 [notice] 1#1: OS: Linux 4.18.0-305.3.1.el8_4.x86_64
2022/09/15 06:30:27 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/09/15 06:30:27 [notice] 1#1: start worker processes
2022/09/15 06:30:27 [notice] 1#1: start worker process 31
127.0.0.1 - - [15/Sep/2022:07:03:51 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.61.1" "-"

[root@k8smaster ~]# kubectl exec -it $POD_NAME -- nginx -v
nginx version: nginx/1.23.1

# Retrieve the node port assigned to the nginx service:
[root@k8smaster ~]# NODE_PORT=$(kubectl get svc nginx --output=jsonpath="{.spec.ports[0].nodePort}")
[root@k8smaster ~]# echo $NODE_PORT
31315
