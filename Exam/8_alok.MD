1. create a new pod called admin-pod with bisybox all the pod to able to set system_time
   The container should sleep for 3200 seconds
   
2. A kubeconfig file called test.kubeconfig has been created under /root/CKA/ 
   There is something wrong in the configuration need to troubleshoot and findout the issue. 
    
   - Use the command kubectl config view check current config and compare with the file. 
    
3. Create a deployment called webproject with image busybox:1.16 and replica 1. Next upgrade the deployment to version 1.17 using rolling update. 
   Make sure that upgrade is recorded in the resource annotation 
   
4. Create deployment called web-hook and scale up to deplyment to 3.. Make sure that desire number of POD is running. 
   Mostly issue on contoller-manager(kubectl describe) in kube-system namespace and thats static pod in cluster 
   
5 . Upgrade the cluster(mater and worker) from 1.22.6 to 1.23.0 Make sure first drain the first master node and second node.
    -  kubeadm upgrade apply v1.25.0 --etcd-upgrade=false 
    -  kubectl drain masternode --ignore-daemonsets --delete-local-data --force or --deleted-empty-dir-data=true

6. create web-load-1234 pod using nginx:1.17 image with label set to tier=web 

7. Create static pod called static-pod01 on node01 with image nginx and we have to make sure that it recreate/restarted automatically in case of any faliure  happen. 
You have to run the command to identify where we need keep the staic pod in node01
   - ps -ef |grep kubelet
   - get the out from previous command --config=/var/lib/kubelet/config.yml  
   - cat /var/lib/kubelet/config.yml |grep static
8.  Create pod called multpod with two container describtion mention below 
    - container 1: name: conatainer1, image: nginx 
    - container 2: name: conatainer2, image: busybox and sleep 4800 

9. Create pod called delta-pod in defence name space belongs to development environment (env=dev) and front tier(tier=front) and image=nginx:1.17

10. Get node01 in json format store it in a file at /opt/output/node0112345.txt

11. Take backup of ETCD database backup and save it in the /opt/output/etcdbackp.db

    - If etcd as service kubectl -n kube-system get pods etcd-pod-kjhdf92 ; kubectl -n kube-system describe pod etcd-pod-kjhdf92  
12. A new applciation has finance pod is deployed in finance name space and there is some issue in the container. 
    
      - Sleep name wrong in the container need to take as -o yaml file and replace with kubectl command. 

13. Create a pod called web-pod using nginx image, expose it internally with service name called web-pod-svc, Check are you able to look up the service and pod from within cluster
      
       - user the image busynox:1.28 for dns lookup 
       - save the result service dns lookup in /root/web-po-svc.txt and save pod lookup in webpod.txt

Solution: 
    
     - kubectl exec -it testpod -- nslookup webpod-svc.default.pod.cluster.local 
     - kubectl exec -it testpod -- nslookup 192-168-1-22.default.pod.cluster.local 
 14. Create persisten Volume with given specification 
 
     - Volume name: pv-data / Storage Size: 100Mi / Access mode : ReadWriteMany /Host Path: /host/pv-data

15. Expose the audit-pod as a service audit-web-service application on port 300002 on the node on the cluster Note: The application listen on port 8080  

16. Taint the worker node  node01 with detail provided here.  

     - Create the pod called   dev-pod-nginx using image as Nginx, make sure that workloads are not shedule to this Workernode Node01. 
     - Create another pod called prod-pod-nginx using using nginx with toleration toleration to be shedule on node01. 
     - Detail:
        - Key: env_type, Vaule: production, operator: equal and Effect: NoShedule
 17. Create pod called Pod-xnhj123 detail given below
   
        - SecuirtyContext: runAsUser: 1000, fsGroup: : 2000 
        - Image : redis/alpine   

    - Validation: kubectl exec pod-xhbx123 -it -- whoamit
      you will get whoami: unknown uid 1000 
      command terminator with exit code 1 and the pod can be communicate as above configuration
          
 18. Workker node node01 is not reponding Have a quick fix.   Kubelet service on wroker need to be validated on node01

 19. List the InternallIIP  of the all the nodes of the cluster and save the result to a file /root/InternallIPList.txt
 
 20. One static pod is riunning on controlpanel node and move the static pod to run on node01.. Dont need to do any other changes 

 21. A new user named guru need to be created, Grant him access to access the cluster. User guru to have access to create,list,get,update and delete the pod in the space in the namespace.. Private key exsits in the location ... /root/guru.key and /root/guru.csr

22. Create pv,pvc and pid with below specification 
    
    - Pv:                         - Pvc                            POD
      - Volume name: my vaolume     - Volume name: pvc-claim-log.    - Name: My-nginx-pod
      - storage: 100Mi              - Storage: 50Mi                  - Image Name: nginx
      - access mode: ReadWriteMany  - Access mode: ReadWriteMany.    - Volume: PersistenVolumeClaim=pvc-claim-log
      - Host Path: /pv/log                                           - VolumeMount: /log
      - Reclaim policy: Retain  
23. Workker node node01 is not reponding Have a quick fix.  From The static kubelet configration and found that there is certificate file is missing with WRONG NAME, we need to neviagte to /var/lib/kubelet/config.yaml and change actual certname in the file.  systemctl daemon-reload and restarted the kubelet

24. A pod is on custom namespace is not running. Find the problem and fix it and make it running. 
    Definition file mention in the root. 
    
    - kubectl describe pod my-nginx-pod and found the pvc claim name is missing. 
    - Find its running right namespace and pvc created on that and claim that.. Surely check on the space we should have pv already created. 
25. Create multi container pod in development namespace and use the nginx and redis image  
26. A pod(nginx-pod) is on default namespace is not running. Find the problem and fix it and make it running.

   - kubectl describe pod nginx-pod 
   - Seems like taint on the node01 while is not allowing to sdhedule 
   - add the toleration on the pod and replace command and recreate it. 

27. Create the deployment called nginx-deploy with image nginx:1.16 and 2 replicas. There are 5 worker node in the cluster. Please make sure that no pod is deployed on below worker node. 
        - woker01 and worker02
        - kubectl cordon worker01 and kubectl cordon worker02 which help to not reploy any deployment on this node. 
        - kubectl create deployment nginx-reploy --image=nginx --replicas=5

28. Create a replicaset (Name: web-pod, Image: nginx:1.17, replicas: 3. Pod is already running on the cluster. 

if you are using exsits pod the we can use same lable and selector while creating replication 

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  labels:
    app: kumar
  name: we-pod
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kumar
  template:
    metadata:
      labels:
        app: kumar
    spec:
      containers:
      - image: nginx
        name: nginx
```
29. There are 3 node in the cluster, create DaemonSet(Name: my-pod, Image: nginx) on each node except one(Worker03)    
        
     - kubectl taint node worker03 env=qa:NoSchedule (then on this node daemon set will not be deployed)
     - After reployment check, kubectl get daemonsets -o wide 
30. Create a pod "front-end-helper" that write "binaries downloaded successfully" into file "front-end-heleper-log.txt" abd then exists. Check, Pod"front-end-helper" should be deleyed automatically when its completed" 
```
# kubectl run front-end-helper --image=busybox -it --rm --restart=Never -- /bin/sh -c 'echo binaries downloaded successfully' > /tmp/front-end-pod.txt
# cat /tmp/front-end-pod.txt 
binaries downloaded successfully
pod "front-end-helper" deleted
```
   - 
30. Check How many nodes are in ready state and write the information about nodes tained with "NoSchedule" into file CKA0006.txt. At least one node's taint information should be logged in a file with below format. 

   {Name""<<Node_value>>", "taints:<<Value>>}
   Name""<<Node_value>>", "taints:<<Value>>}
```
   - Kubectl taint node master cpu=high:NoSchedule 
   - kubectl get nodes -o custom-columns=NAME:.metadata.name,taints:.spec.taints - I dont but actually
   - [root@master ~]# kubectl get nodes -o json |jq ".items[]| {name: .metadata.name, taints:.spec.taints}"
{
  "name": "master",
  "taints": [
    {
      "effect": "NoSchedule",
      "key": "node-role.kubernetes.io/master"
    }
  ]
}
{
  "name": "node1",
  "taints": null
}
```
