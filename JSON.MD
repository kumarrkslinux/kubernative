``` 
$kubectl get nodes -o=jsonpath='{.items[*].metatdata.name}'
master node01

$kubectl get nodes -o=jsonpath='{.items[*].status.nodeinfo.architecture}'
amd64 amd64

$kubectl get nodes -o=jsonpath='{.items[*].status.capacity.cpu}'
4 4 

$kubectl get nodes -o=jsonpath='{.items[*].metatdata.name}{.items[*].status.capacity.cpu}'
master node01 4 4 

$kubectl get nodes -o=jsonpath='{.items[*].metatdata.name} {"\n"} {.items[*].status.capacity.cpu}'
master node01 
4        4 

Loop 
$ kubectl get nodes -o=jsonpath='{range.items[*]}{.metatdata.name} {"\t"} {.status.capacity.cpu}{"\n"}{end}'  -->  $ is not required for kubectl 


$kubectl get nodes -o=custom-cloumns=NODE:.metatdata.name,CPU:.status.capacity.cpu
NODE    CPU
master   4
node01   4

$kubectl get nodes --sort-by=.metadata.name


$kubectl get nodes --sort-by=.status.capacity.cpu

How to get more than 40 : 
$[?( @ > 40 )]  -- @ means each iteam in the list / check if ?

$.car.wheels[?(@.location == "rear-wheel")].model
equal to 40
@ == 40 
Not equal to 40
@ != 40 

While card: 
$.*.color 
$.*.prize
$[*].model  -- All item in the list 
$.*.wheels[*].model

List: 
$[0:3]  [START:END]
$[0:8:2] [START:END:STEP]
$[-1:0] Last item in the list
$[-1:]
$[-3:]

practice-test-advanced-kubectl-commands:

#kubectl get nodes  -o json > /opt/outputs/nodes.json

#kubectl get node node01  -o json > /opt/outputs/node01.json

#controlplane ~ ➜  kubectl get nodes  -o jsonpath='{.items[*].metadata.name}'
controlplane node01

#kubectl get nodes  -o jsonpath='{.items[*].metadata.name}' > /opt/outputs/node_names.txt

#controlplane ~ ➜  kubectl get nodes  -o jsonpath='{.items[*].status.nodeInfo.osImage}'
Ubuntu 18.04.6 LTS Ubuntu 18.04.6 LTS

#kubectl get nodes  -o jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os.txt

#controlplane ~ ➜  kubectl config view --kubeconfig=/root/my-kube-config -o jsonpath='{.users[*].name}'
aws-user dev-user test-user

#controlplane ~ ➜  kubectl config view --kubeconfig=/root/my-kube-config -o jsonpath='{.users[*].name}' > /opt/outputs/users.txt

1. A set of Persistent Volumes are available. Sort them based on their capacity and store the result in the file /opt/outputs/storage-capacity-sorted.txt.

# controlplane ~ ➜  kubectl get pv --sort-by=.spec.capacity.storage
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log-4   40Mi       RWX            Retain           Available                                   22m
pv-log-1   100Mi      RWX            Retain           Available                                   22m
pv-log-2   200Mi      RWX            Retain           Available                                   22m
pv-log-3   300Mi      RWX            Retain           Available                                   22m

# kubectl get pv --sort-by=.spec.capacity.storage > /opt/outputs/storage-capacity-sorted.txt


controlplane ~ ➜  kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage 
NAME       CAPACITY
pv-log-4   40Mi
pv-log-1   100Mi
pv-log-2   200Mi
pv-log-3   300Mi

2. That was good, but we don't need all the extra details. Retrieve just the first 2 columns of output and store it in /opt/outputs/pv-and-capacity-sorted.txt.

The columns should be named NAME and CAPACITY. Use the custom-columns option and remember, it should still be sorted as in the previous question

controlplane ~ ➜  kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage > /opt/outputs/pv-and-capacity-sorted.txt

3. Use a JSON PATH query to identify the context configured for the aws-user in the my-kube-config context file and store the result in /opt/outputs/aws-context-name

