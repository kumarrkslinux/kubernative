What is the network interface configured for cluster connectivity on the controlplane node?

root@controlplane ~ ✖ kubectl get nodes controlplane -o wide
NAME           STATUS   ROLES                  AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane,master   10m   v1.23.0   10.6.57.9     <none>        Ubuntu 18.04.6 LTS   5.4.0-1080-gcp   docker://19.3.0

root@controlplane ~ ➜  ip a|grep 10.6.57.9
    inet 10.6.57.9/24 brd 10.6.57.255 scope global eth0

root@controlplane ~ ➜  ifconfig eth0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1450
        inet 10.6.57.9  netmask 255.255.255.0  broadcast 10.6.57.255
        ether 02:42:0a:06:39:09  txqueuelen 0  (Ethernet)
        RX packets 2940  bytes 367761 (367.7 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2203  bytes 1413874 (1.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

How to find the interface name of node01?
root@controlplane ~ ✖ kubectl get nodes node01 -o wide
NAME     STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
node01   Ready    <none>   13m   v1.23.0   10.6.57.12    <none>        Ubuntu 18.04.6 LTS   5.4.0-1080-gcp   docker://19.3.0

  
root@controlplane ~ ➜  ip a |grep 10.6.57.12

How to find the mac address of node01?

root@controlplane ~ ➜  arp node01
Address                  HWtype  HWaddress           Flags Mask            Iface
10.6.57.11               ether   02:42:0a:06:39:0a   C                     eth0

 what is the bridge used on these servers ?
  
root@controlplane ~ ➜  ip link 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default 
    link/ether 02:42:6a:f5:e3:55 brd ff:ff:ff:ff:ff:ff
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue state UNKNOWN mode DEFAULT group default 
    link/ether de:f5:9c:a0:1f:0f brd ff:ff:ff:ff:ff:ff
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 6a:77:d8:02:52:f1 brd ff:ff:ff:ff:ff:ff
5: veth35e808d4@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue master cni0 state UP mode DEFAULT group default 
    link/ether 4a:6a:42:5d:f6:5e brd ff:ff:ff:ff:ff:ff link-netnsid 2
6: vethe558a0f1@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue master cni0 state UP mode DEFAULT group default 
    link/ether 4a:49:c0:51:d9:bc brd ff:ff:ff:ff:ff:ff link-netnsid 3
20260: eth0@if20261: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 02:42:0a:06:39:09 brd ff:ff:ff:ff:ff:ff link-netnsid 0
20262: eth1@if20263: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 02:42:ac:19:00:45 brd ff:ff:ff:ff:ff:ff link-netnsid 1

root@controlplane ~ ➜  ifconfig docker0
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.12.0.1  netmask 255.255.255.0  broadcast 172.12.0.255
        ether 02:42:6a:f5:e3:55  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


root@controlplane ~ ➜  ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:6a:f5:e3:55 brd ff:ff:ff:ff:ff:ff
    inet 172.12.0.1/24 brd 172.12.0.255 scope global docker0
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue state UNKNOWN group default 
    link/ether de:f5:9c:a0:1f:0f brd ff:ff:ff:ff:ff:ff
    inet 10.244.0.0/32 brd 10.244.0.0 scope global flannel.1
       valid_lft forever preferred_lft forever
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue state UP group default qlen 1000
    link/ether 6a:77:d8:02:52:f1 brd ff:ff:ff:ff:ff:ff
    inet 10.244.0.1/24 brd 10.244.0.255 scope global cni0
       valid_lft forever preferred_lft forever
5: veth35e808d4@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue master cni0 state UP group default 
    link/ether 4a:6a:42:5d:f6:5e brd ff:ff:ff:ff:ff:ff link-netnsid 2
6: vethe558a0f1@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue master cni0 state UP group default 
    link/ether 4a:49:c0:51:d9:bc brd ff:ff:ff:ff:ff:ff link-netnsid 3
20260: eth0@if20261: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether 02:42:0a:06:39:09 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.6.57.9/24 brd 10.6.57.255 scope global eth0
       valid_lft forever preferred_lft forever
20262: eth1@if20263: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:19:00:45 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet 172.25.0.69/24 brd 172.25.0.255 scope global eth1
       valid_lft forever preferred_lft forever

root@controlplane ~ ➜  ip a |grep docker0
2: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    inet 172.12.0.1/24 brd 172.12.0.255 scope global docker0

How can find the default gateway for this system ?
root@controlplane ~ ➜  route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.25.0.1      0.0.0.0         UG    0      0        0 eth1
10.6.57.0       0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.244.0.0      0.0.0.0         255.255.255.0   U     0      0        0 cni0
10.244.1.0      10.244.1.0      255.255.255.0   UG    0      0        0 flannel.1
172.12.0.0      0.0.0.0         255.255.255.0   U     0      0        0 docker0
172.25.0.0      0.0.0.0         255.255.255.0   U     0      0        0 eth1

root@controlplane ~ ➜  kubectl get pods -n kube-system 
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-64897985d-4kgvh                1/1     Running   0          19m
coredns-64897985d-lllvn                1/1     Running   0          19m
etcd-controlplane                      1/1     Running   0          19m
kube-apiserver-controlplane            1/1     Running   0          20m
kube-controller-manager-controlplane   1/1     Running   0          20m
kube-flannel-ds-dppkq                  1/1     Running   0          19m
kube-flannel-ds-r9rdd                  1/1     Running   0          19m
kube-proxy-qpm9r                       1/1     Running   0          19m
kube-proxy-zrdgz                       1/1     Running   0          19m
kube-scheduler-controlplane            1/1     Running   0          19m

root@controlplane ~ ✖ kubectl get pod kube-scheduler-controlplane -n kube-system 
NAME                          READY   STATUS    RESTARTS   AGE
kube-scheduler-controlplane   1/1     Running   0          20m

root@controlplane ~ ➜  kubectl get pod kube-scheduler-controlplane -n kube-system -o wide
NAME                          READY   STATUS    RESTARTS   AGE   IP          NODE           NOMINATED NODE   READINESS GATES
kube-scheduler-controlplane   1/1     Running   0          20m   10.6.57.9   controlplane   <none>           <none>

what is the port used by scheduler ?
root@controlplane ~ ➜  netstat -nplt|grep scheduler
tcp        0      0 127.0.0.1:10259         0.0.0.0:*               LISTEN      3463/kube-scheduler 

  
root@controlplane ~ ➜  netstat -nplt|grep etcd
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN      3712/etcd           
tcp        0      0 10.6.57.9:2379          0.0.0.0:*               LISTEN      3712/etcd           
tcp        0      0 10.6.57.9:2380          0.0.0.0:*               LISTEN      3712/etcd           
tcp        0      0 127.0.0.1:2381          0.0.0.0:*               LISTEN      3712/etcd       
