
### Control plane Faliure

``` 
1. 
The cluster is broken. We tried deploying an application but it's not working. Troubleshoot and fix the issue.
Start looking at the deployments.


root@controlplane ~ ➜  kubectl get pods -n kube-system 
NAME                                   READY   STATUS             RESTARTS   AGE
coredns-74ff55c5b-6plz9                1/1     Running            0          39m
coredns-74ff55c5b-f5ppq                1/1     Running            0          39m
etcd-controlplane                      1/1     Running            0          39m
kube-apiserver-controlplane            1/1     Running            0          39m
kube-controller-manager-controlplane   1/1     Running            0          39m
kube-flannel-ds-zv9pr                  1/1     Running            0          39m
kube-proxy-2n468                       1/1     Running            0          39m
kube-scheduler-controlplane            0/1     CrashLoopBackOff   3          79s


root@controlplane /etc/kubernetes/manifests ➜  pwd
/etc/kubernetes/manifests

root@controlplane /etc/kubernetes/manifests ➜  cat kube-scheduler.yaml |grep sche
    component: kube-scheduler
  name: kube-scheduler
    - kube-schedulerrrr   --------------------this need to be corrected 
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    image: k8s.gcr.io/kube-scheduler:v1.20.0
        scheme: HTTPS
    name: kube-scheduler
        scheme: HTTPS
    - mountPath: /etc/kubernetes/scheduler.conf
      path: /etc/kubernetes/scheduler.conf

Run the command: kubectl get pods -n kube-system. Check the kube-scheduler manifest file and fix the issue.
The command run by the scheduler pod is incorrect. Here is a snippet of the YAML file.

spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=true
    - --port=0
Once this is corrected, the scheduler pod will be recreated.

root@controlplane /etc/kubernetes/manifests ➜  kubectl get -n kube-system pods
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-74ff55c5b-6plz9                1/1     Running   0          45m
coredns-74ff55c5b-f5ppq                1/1     Running   0          45m
etcd-controlplane                      1/1     Running   0          46m
kube-apiserver-controlplane            1/1     Running   0          46m
kube-controller-manager-controlplane   1/1     Running   0          46m
kube-flannel-ds-zv9pr                  1/1     Running   0          45m
kube-proxy-2n468                       1/1     Running   0          45m
kube-scheduler-controlplane            1/1     Running   0          22s
``` 

2. 
Scale the deployment app to 2 pods.

```
root@controlplane /etc/kubernetes/manifests ✖ kubectl scale deploy app --replicas=2
deployment.apps/app scaled

``` 

3. Even though the deployment was scaled to 2, the number of PODs does not seem to increase. Investigate and fix the issue.

Inspect the component responsible for managing deployments and replicasets.

```
root@controlplane /etc/kubernetes/manifests ➜  kubectl get pods -A 
NAMESPACE     NAME                                   READY   STATUS             RESTARTS   AGE
default       app-586bddbc54-t28gk                   1/1     Running            0          13m
kube-system   coredns-74ff55c5b-6plz9                1/1     Running            0          50m
kube-system   coredns-74ff55c5b-f5ppq                1/1     Running            0          50m
kube-system   etcd-controlplane                      1/1     Running            0          51m
kube-system   kube-apiserver-controlplane            1/1     Running            0          51m
kube-system   kube-controller-manager-controlplane   0/1     CrashLoopBackOff   5          3m55s
kube-system   kube-flannel-ds-zv9pr                  1/1     Running            0          50m
kube-system   kube-proxy-2n468                       1/1     Running            0          50m
kube-system   kube-scheduler-controlplane            1/1     Running            0          5m25s

root@controlplane /etc/kubernetes/manifests ✖ kubectl logs kube-controller-manager-controlplane -n kube-system 
Flag --port has been deprecated, see --secure-port instead.
I0926 05:05:24.119085       1 serving.go:331] Generated self-signed cert in-memory
stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory

root@controlplane /etc/kubernetes/manifests ✖ cat kube-controller-manager.yaml |grep contr
    component: kube-controller-manager
    tier: control-plane
  name: kube-controller-manager
    - kube-controller-manager
    - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --controllers=*,bootstrapsigner,tokencleaner
    - --kubeconfig=/etc/kubernetes/controller-manager-XXXX.conf   ------ This need to corrected 
    image: k8s.gcr.io/kube-controller-manager:v1.20.0
    name: kube-controller-manager
    - mountPath: /etc/kubernetes/controller-manager.conf
      path: /etc/kubernetes/controller-manager.conf
      
 root@controlplane /etc/kubernetes/manifests ✖ ll /etc/kubernetes/controller-manager.conf
-rw------- 1 root root 5598 Sep 26 04:14 /etc/kubernetes/controller-manager.conf

``` 

4. Something is wrong with scaling again. We just tried scaling the deployment to 3 replicas. But it's not happening.

Investigate and fix the issue.

```
root@controlplane /etc/kubernetes/manifests ➜  kubectl get pods -A 
NAMESPACE     NAME                                   READY   STATUS             RESTARTS   AGE
default       app-586bddbc54-cwwcq                   1/1     Running            0          80s
default       app-586bddbc54-t28gk                   1/1     Running            0          18m
kube-system   coredns-74ff55c5b-6plz9                1/1     Running            0          56m
kube-system   coredns-74ff55c5b-f5ppq                1/1     Running            0          56m
kube-system   etcd-controlplane                      1/1     Running            0          56m
kube-system   kube-apiserver-controlplane            1/1     Running            0          56m
kube-system   kube-controller-manager-controlplane   0/1     CrashLoopBackOff   3          71s
kube-system   kube-flannel-ds-zv9pr                  1/1     Running            0          56m
kube-system   kube-proxy-2n468                       1/1     Running            0          56m
kube-system   kube-scheduler-controlplane            1/1     Running            0          11m

root@controlplane /etc/kubernetes/manifests ➜  kubectl logs kube-controller-manager-controlplane -n kube-system 
Flag --port has been deprecated, see --secure-port instead.
I0926 05:11:32.822458       1 serving.go:331] Generated self-signed cert in-memory
unable to load client CA file "/etc/kubernetes/pki/ca.crt": open /etc/kubernetes/pki/ca.crt: no such file or directory

root@controlplane /etc/kubernetes/manifests ➜  ll /etc/kubernetes/pki/ca.crt 
-rw-r--r-- 1 root root 1066 Sep 26 04:14 /etc/kubernetes/pki/ca.crt

root@controlplane /etc/kubernetes/manifests ➜  kubectl get pods -A 
NAMESPACE     NAME                                   READY   STATUS    RESTARTS   AGE
default       app-586bddbc54-cwwcq                   1/1     Running   0          5m2s
default       app-586bddbc54-t28gk                   1/1     Running   0          22m
kube-system   coredns-74ff55c5b-6plz9                1/1     Running   0          60m
kube-system   coredns-74ff55c5b-f5ppq                1/1     Running   0          60m
kube-system   etcd-controlplane                      1/1     Running   0          60m
kube-system   kube-apiserver-controlplane            1/1     Running   0          60m
kube-system   kube-controller-manager-controlplane   1/1     Running   0          79s
kube-system   kube-flannel-ds-zv9pr                  1/1     Running   0          60m
kube-system   kube-proxy-2n468                       1/1     Running   0          60m
kube-system   kube-scheduler-controlplane            1/1     Running   0          14m
```
### NODE Faliure 

1. Fix the broken cluster

```
root@controlplane ~ ➜  kubectl get nodes
NAME           STATUS     ROLES                  AGE     VERSION
controlplane   Ready      control-plane,master   7m47s   v1.23.0
node01         NotReady   <none>                 7m5s    v1.23.0


root@controlplane ~ ➜  ssh node01
Warning: Permanently added the ECDSA host key for IP address '10.12.149.9' to the list of known hosts.

root@node01 ~ ➜  service kubelet status
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: inactive (dead) since Mon 2022-09-26 05:20:06 UTC; 2min 20s ago
     Docs: https://kubernetes.io/docs/home/
  Process: 1502 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS
 Main PID: 1502 (code=exited, status=0/SUCCESS)

Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552750    1502 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552776    1502 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552803    1502 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552825    1502 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552841    1502 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552882    1502 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:14:34 node01 kubelet[1502]: I0926 05:14:34.552910    1502 reconciler.go:157] "Reconciler: start to s
Sep 26 05:14:35 node01 kubelet[1502]: I0926 05:14:35.728022    1502 request.go:665] Waited for 1.074092078s du
Sep 26 05:14:43 node01 kubelet[1502]: I0926 05:14:43.085593    1502 transport.go:135] "Certificate rotation de


root@node01 ~ ✖ service kubelet start

root@node01 ~ ➜  service kubelet status
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: active (running) since Mon 2022-09-26 05:22:51 UTC; 3s ago
     Docs: https://kubernetes.io/docs/home/
 Main PID: 5376 (kubelet)
    Tasks: 35 (limit: 251382)
   CGroup: /system.slice/kubelet.service
           └─5376 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=

Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.184685    5376 topology_manager.go:200] "Topology Admit H
Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.231823    5376 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.231890    5376 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.231907    5376 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.231930    5376 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.231959    5376 reconciler.go:216] "operationExecutor.Veri
Sep 26 05:22:53 node01 kubelet[5376]: I0926 05:22:53.232018    5376 reconciler.go:216] "operationExecutor.Veri

root@controlplane ~ ✖ kubectl get nodes
NAME           STATUS   ROLES                  AGE     VERSION
controlplane   Ready    control-plane,master   9m31s   v1.23.0
node01         Ready    <none>                 8m49s   v1.23.0

``` 

2. The cluster is broken again. Investigate and fix the issue. 

``` 

root@controlplane ~ ➜  kubectl get nodes
NAME           STATUS     ROLES                  AGE   VERSION
controlplane   Ready      control-plane,master   10m   v1.23.0
node01         NotReady   <none>                 10m   v1.23.0

root@controlplane ~ ➜  ssh node01
Last login: Mon Sep 26 05:22:05 2022 from 10.12.149.6

root@node01 ~ ✖ service  kubelet start 

root@node01 ~ ➜  service kubelet status
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: activating (auto-restart) (Result: exit-code) since Mon 2022-09-26 05:24:54 UTC; 4s ago
     Docs: https://kubernetes.io/docs/home/
  Process: 6396 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS
 Main PID: 6396 (code=exited, status=1/FAILURE)
 
 root@node01 ~ ➜  journalctl -u kubelet.service -f |grep err
Sep 26 05:14:33 node01 kubelet[1502]: I0926 05:14:33.252653    1502 cni.go:240] "Unable to update cni config" err="no networks found in /etc/cni/net.d"
Sep 26 05:14:33 node01 kubelet[1502]: I0926 05:14:33.255914    1502 cni.go:240] "Unable to update cni config" err="no networks found in /etc/cni/net.d"
Sep 26 05:14:33 node01 kubelet[1502]: I0926 05:14:33.256016    1502 cni.go:240] "Unable to update cni config" err="no networks found in /etc/cni/net.d"
Sep 26 05:14:33 node01 kubelet[1502]: E0926 05:14:33.342398    1502 kubelet.go:1351] "Image garbage collection failed once. Stats initialization may not have completed yet" err="failed to get imageFs info: unable to find data in memory cache"
Sep 26 05:14:33 node01 kubelet[1502]: E0926 05:14:33.348408    1502 nodelease.go:49] "Failed to get node when trying to set owner ref to the node lease" err="nodes \"node01\" not found" node="node01"
Sep 26 05:14:33 node01 kubelet[1502]: E0926 05:14:33.446196    1502 kubelet.go:2422] "Error getting node" err="node \"node01\" not found"
Sep 26 05:14:33 node01 kubelet[1502]: E0926 05:14:33.538322    1502 kubelet.go:2001] "Skipping pod synchronization" err="[container runtime status check may not have completed yet, PLEG is not healthy: pleg has yet to be successful]"
Sep 26 05:14:33 node01 kubelet[1502]: E0926 05:14:33.546799    1502 kubelet.go:2422] "Error getting node" err="node \"node01\" not found"
Sep 26 05:14:33 node01 kubelet[1502]: E0926 05:14:33.638739    1502 kubelet.go:2001] "Skipping pod synchronization" err="container runtime status check may not have completed yet"
Sep 26 05:14:33 node01 kubelet[1502]: I0926 05:14:33.727115    1502 manager.go:610] "Failed to read data from checkpoint" checkpoint="kubelet_internal_checkpoint" err="checkpoint is not found"
Sep 26 05:22:52 node01 kubelet[5376]: E0926 05:22:52.225486    5376 kubelet.go:1351] "Image garbage collection failed once. Stats initialization may not have completed yet" err="failed to get imageFs info: unable to find data in memory cache"
Sep 26 05:22:52 node01 kubelet[5376]: E0926 05:22:52.337887    5376 kubelet.go:2001] "Skipping pod synchronization" err="[container runtime status check may not have completed yet, PLEG is not healthy: pleg has yet to be successful]"
Sep 26 05:22:52 node01 kubelet[5376]: E0926 05:22:52.438079    5376 kubelet.go:2001] "Skipping pod synchronization" err="container runtime status check may not have completed yet"
Sep 26 05:22:52 node01 kubelet[5376]: I0926 05:22:52.525850    5376 manager.go:610] "Failed to read data from checkpoint" checkpoint="kubelet_internal_checkpoint" err="checkpoint is not found"
Sep 26 05:23:42 node01 kubelet[6071]: E0926 05:23:42.229877    6071 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:23:52 node01 kubelet[6104]: E0926 05:23:52.528481    6104 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:24:02 node01 kubelet[6137]: E0926 05:24:02.758541    6137 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:24:13 node01 kubelet[6174]: E0926 05:24:13.027222    6174 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:24:23 node01 kubelet[6210]: E0926 05:24:23.257637    6210 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:24:33 node01 kubelet[6245]: E0926 05:24:33.528876    6245 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:24:43 node01 kubelet[6284]: E0926 05:24:43.829328    6284 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:24:54 node01 kubelet[6396]: E0926 05:24:54.007944    6396 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:25:04 node01 kubelet[6445]: E0926 05:25:04.330342    6445 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:25:14 node01 kubelet[6486]: E0926 05:25:14.527752    6486 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:25:24 node01 kubelet[6562]: E0926 05:25:24.757305    6562 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:25:35 node01 kubelet[6600]: E0926 05:25:35.028182    6600 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:25:45 node01 kubelet[6637]: E0926 05:25:45.258878    6637 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:25:55 node01 kubelet[6671]: E0926 05:25:55.526473    6671 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:26:05 node01 kubelet[6705]: E0926 05:26:05.760101    6705 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:26:16 node01 kubelet[6742]: E0926 05:26:16.010802    6742 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:26:26 node01 kubelet[6778]: E0926 05:26:26.255363    6778 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:26:46 node01 kubelet[6851]: E0926 05:26:46.757975    6851 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:27:07 node01 kubelet[6920]: E0926 05:27:07.256720    6920 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:27:17 node01 kubelet[6954]: E0926 05:27:17.530389    6954 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:27:27 node01 kubelet[6997]: E0926 05:27:27.757968    6997 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:27:38 node01 kubelet[7075]: E0926 05:27:38.003218    7075 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:27:48 node01 kubelet[7116]: E0926 05:27:48.256571    7116 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:27:58 node01 kubelet[7147]: E0926 05:27:58.505964    7147 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:28:08 node01 kubelet[7183]: E0926 05:28:08.756217    7183 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:28:19 node01 kubelet[7217]: E0926 05:28:19.004905    7217 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:28:29 node01 kubelet[7309]: E0926 05:28:29.279207    7309 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:28:39 node01 kubelet[7347]: E0926 05:28:39.527310    7347 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:28:49 node01 kubelet[7381]: E0926 05:28:49.828477    7381 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Sep 26 05:29:10 node01 kubelet[7498]: E0926 05:29:10.256543    7498 server.go:279] "Failed to construct kubelet dependencies" err="unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"


kubelet has stopped running on node01 again. Since this is a systemd managed system, we can check the kubelet log by running journalctl. Here is a snippet showing the error with kubelet:

root@node01:~# journalctl -u kubelet 
.
.
Jul 25 07:54:50 node01 kubelet[5681]: F0725 07:54:50.831238    5681 server.go:257] unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory
Jul 25 07:55:01 node01 kubelet[5710]: F0725 07:55:01.339531    5710 server.go:257]
.
.
There appears to be a mistake path used for the CA certificate in the kubelet configuration. This can be corrected by updating the file /var/lib/kubelet/config.yaml.  on worker nodes - /etc/kubernetes/pki/WRONG-CA-FILE.crt
Once this is fixed, restart the kubelet service, (like we did in the previous question) and node01 should return back to a working state.


