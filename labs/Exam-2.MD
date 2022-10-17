#### Print the names of all deployments in the admin2406 namespace in the following format:
#### DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE
#### <deployment name> <container image used> <ready replica count> <Namespace>
#### The data should be sorted by the increasing order of the deployment name

Weight: 15
  
#### Example:
####  DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE
#### deploy0 nginx:alpine 1 admin2406
#### Write the result to the file /opt/admin2406_data.
  
```   
controlplane ~ ✖ kubectl get deployments.apps -o jsonpath='{.items[*].metadata.name}' -n admin2406
deploy1 deploy2 deploy3 deploy4 deploy5

controlplane ~ ➜  kubectl get deployments.apps -o jsonpath='{.items[*].spec.template.spec.containers[0].image}' -n admin2406
nginx nginx:alpine nginx:1.16 nginx:1.17 nginx:latest

kubectl get deployments.apps -o jsonpath='{.items[*].status.readyReplicas}' -n admin2406

kubectl get deployments.apps -o jsonpath='{.items[*].metadata.namespace}' -n admin2406

DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE


kubectl get deployments.apps -o=custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[0].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace -n admin2406 > /opt/admin2406_data
