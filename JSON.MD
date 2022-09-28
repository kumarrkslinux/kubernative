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
$ kubectl get nodes -o=jsonpath='{range.items[*]}{.metatdata.name} {"\t"} {.status.capacity.cpu}{"\n"}{end}'


$kubectl get nodes -o=custom-cloumns=NODE:.metatdata.name,CPU:.status.capacity.cpu
NODE    CPU
master   4
node01   4

$kubectl get nodes --sort-by=.metadata.name


$kubectl get nodes --sort-by=.status.capacity.cpu
