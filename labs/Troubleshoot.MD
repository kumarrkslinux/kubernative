
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
