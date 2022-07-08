1. Inspect the kubelet service and identify the network plugin configured for Kubernetes.
2. What network range are the nodes in the cluster part of?

Run the command: ip addr and look at the IP address assigned to the eth0 interfaces. Derive network range from that.

root@controlplane ~ ➜  ip a |grep eth0
21402: eth0@if21403: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    inet 10.68.205.3/24 brd 10.68.205.255 scope global eth0
    
root@controlplane ~ ➜  ipcalc -b 10.68.205.3/24
Address:   10.68.205.3          
Netmask:   255.255.255.0 = 24   
Wildcard:  0.0.0.255            
=>
Network:   10.68.205.0/24       
HostMin:   10.68.205.1          
HostMax:   10.68.205.254        
Broadcast: 10.68.205.255        
Hosts/Net: 254                   Class A, Private Internet


2. What is the range of IP addresses configured for PODs on this cluster?

root@controlplane ~ ➜  kubectl get pods -n kube-system 
NAME                                   READY   STATUS    RESTARTS      AGE
coredns-64897985d-tfpbv                1/1     Running   0             42m
coredns-64897985d-z52tb                1/1     Running   0             42m
etcd-controlplane                      1/1     Running   0             42m
kube-apiserver-controlplane            1/1     Running   0             42m
kube-controller-manager-controlplane   1/1     Running   0             42m
kube-proxy-ftqpj                       1/1     Running   0             42m
kube-proxy-ln5n7                       1/1     Running   0             41m
kube-scheduler-controlplane            1/1     Running   0             42m
weave-net-2wnhc                        2/2     Running   1 (42m ago)   42m
weave-net-82w4v                        2/2     Running   0             41m

The network is configured with weave. Check the weave pods logs using command kubectl logs <weave-pod-name> weave -n kube-system and look for ipalloc-range
ipalloc-range:10.50.0.0/16

root@controlplane ~ ➜  kubectl logs weave-net-2wnhc -n kube-system weave
weave       weave-init  weave-npc   

root@controlplane ~ ➜  kubectl logs weave-net-2wnhc -n kube-system weave
DEBU: 2022/07/08 04:30:14.088327 [kube-peers] Checking peer "c2:1e:48:2b:77:5a" against list &{[]}
Peer not in list; removing persisted data
INFO: 2022/07/08 04:30:14.267201 Command line options: map[conn-limit:200 datapath:datapath db-prefix:/weavedb/weave-net docker-api: expect-npc:true http-addr:127.0.0.1:6784 ipalloc-init:consensus=0 ipalloc-range:10.50.0.0/16 metrics-addr:0.0.0.0:6782 name:c2:1e:48:2b:77:5a nickname:controlplane no-dns:true no-masq-local:true port:6783]
INFO: 2022/07/08 04:30:14.267301 weave  2.8.1
INFO: 2022/07/08 04:30:15.339257 Bridge type is bridged_fastdp
INFO: 2022/07/08 04:30:15.339292 Communication between peers is unencrypted.
INFO: 2022/07/08 04:30:15.345325 Our name is c2:1e:48:2b:77:5a(controlplane)
INFO: 2022/07/08 04:30:15.345382 Launch detected - using supplied peer list: []
INFO: 2022/07/08 04:30:15.345423 Using "no-masq-local" LocalRangeTracker
INFO: 2022/07/08 04:30:15.345428 Checking for pre-existing addresses on weave bridge
INFO: 2022/07/08 04:30:15.346387 [allocator c2:1e:48:2b:77:5a] No valid persisted data
INFO: 2022/07/08 04:30:15.351662 [allocator c2:1e:48:2b:77:5a] Initialising via deferred consensus
INFO: 2022/07/08 04:30:15.351752 Sniffing traffic on datapath (via ODP)
INFO: 2022/07/08 04:30:15.355238 Listening for HTTP control messages on 127.0.0.1:6784
INFO: 2022/07/08 04:30:15.355273 Listening for metrics requests on 0.0.0.0:6782
INFO: 2022/07/08 04:30:15.519216 Error checking version: Get "https://checkpoint-api.weave.works/v1/check/weave-net?arch=amd64&flag_docker-version=none&flag_kernel-version=5.4.0-1080-gcp&os=linux&signature=HFO68S%2BuA9L3%2BjMaZVeYbPIqQTtzb4%2BWfOj1iF4vqyc%3D&version=2.8.1": dial tcp: lookup checkpoint-api.weave.works on 10.96.0.10:53: write udp 172.25.0.70:45430->10.96.0.10:53: write: operation not permitted
INFO: 2022/07/08 04:30:16.280153 [kube-peers] Added myself to peer list &{[{c2:1e:48:2b:77:5a controlplane}]}
DEBU: 2022/07/08 04:30:16.289233 [kube-peers] Nodes that have disappeared: map[]
INFO: 2022/07/08 04:30:16.301845 Assuming quorum size of 1
INFO: 2022/07/08 04:30:16.301933 adding entry 10.50.0.0/16 to weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:16.301946 added entry 10.50.0.0/16 to weaver-no-masq-local of 0
10.50.0.1
DEBU: 2022/07/08 04:30:16.455446 registering for updates for node delete events
INFO: 2022/07/08 04:30:32.180591 ->[10.68.205.6:34343] connection accepted
INFO: 2022/07/08 04:30:32.181499 ->[10.68.205.6:34343|ae:95:35:63:ab:bf(node01)]: connection ready; using protocol version 2
INFO: 2022/07/08 04:30:32.181699 overlay_switch ->[ae:95:35:63:ab:bf(node01)] using fastdp
INFO: 2022/07/08 04:30:32.181755 ->[10.68.205.6:34343|ae:95:35:63:ab:bf(node01)]: connection added (new peer)
INFO: 2022/07/08 04:30:32.282526 ->[10.68.205.6:34343|ae:95:35:63:ab:bf(node01)]: connection fully established
INFO: 2022/07/08 04:30:32.283819 sleeve ->[10.68.205.6:6783|ae:95:35:63:ab:bf(node01)]: Effective MTU verified at 1388
INFO: 2022/07/08 04:30:32.300245 adding entry 10.50.0.0/17 to weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:32.300285 added entry 10.50.0.0/17 to weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:32.301412 adding entry 10.50.128.0/18 to weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:32.301453 added entry 10.50.128.0/18 to weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:32.302224 deleting entry 10.50.0.0/16 from weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:32.302250 deleted entry 10.50.0.0/16 from weaver-no-masq-local of 0
INFO: 2022/07/08 04:30:37.290918 Discovered remote MAC ae:95:35:63:ab:bf at ae:95:35:63:ab:bf(node01)
  
3. What is the IP Range configured for the services within the cluster?
  Inspect the setting on kube-api server by running on command cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range
  
  root@controlplane ~ ✖ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range
    - --service-cluster-ip-range=10.96.0.0/12

root@controlplane ~ ➜  ps -ef |grep service-cluster-ip-range
root      3355  3192  0 04:29 ?        00:02:18 kube-apiserver --advertise-address=10.68.205.3 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
root      3526  3362  0 04:29 ?        00:00:48 kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf --bind-address=127.0.0.1 --client-ca-file=/etc/kubernetes/pki/ca.crt --cluster-cidr=10.244.0.0/16 --cluster-name=kubernetes --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt --cluster-signing-key-file=/etc/kubernetes/pki/ca.key --controllers=*,bootstrapsigner,tokencleaner --kubeconfig=/etc/kubernetes/controller-manager.conf --leader-elect=true --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --root-ca-file=/etc/kubernetes/pki/ca.crt --service-account-private-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --use-service-account-credentials=true
root     24278 18115  0 05:20 pts/0    00:00:00 grep --color=auto service-cluster-ip-range
  
