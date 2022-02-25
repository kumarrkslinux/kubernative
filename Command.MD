## POD:

## Create a Pod

- kubectl run nginx --image=nginx

Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

- kubectl run nginx --image=nginx --dry-run=client -o yaml



## Replicaset: 

- kubectl get pods

- kubectl get replicasets.apps

- kubectl create -f replicaset-definition-1.yaml 

- kubectl create -f replicaset-definition-2.yaml 

- kubectl delete replicasets.apps replicaset-1

- kubectl describe pod new-replica-set-sqnpm

- kubectl edit replicasets.apps new-replica-set (Either delete and recreate the ReplicaSet or Update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.)

- kubectl scale replicaset --replicas=5 new-replica-set


# Deployment
## Create a deployment

- kubectl create deployment --image=nginx nginx

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

- kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)

- kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml

Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

- kubectl create -f nginx-deployment.yaml

OR

In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.

- kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml

