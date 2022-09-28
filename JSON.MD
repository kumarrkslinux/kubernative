``` 
$kubectl get nodes -o=jsonpath='{.items[*].metatdata.name}'
master node01

$kubectl get nodes -o=jsonpath='{.items[*].status.nodeinfo.architecture}'
amd64 amd64

$kubectl get nodes -o=jsonpath='{.items[*].status.capacity.cpu}'
4 4 

$kubectl get nodes -o=jsonpath=''{.items[*].metatdata.name}{.items[*].status.capacity.cpu}'
master node01 4 4 
