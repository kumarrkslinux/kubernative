
kubectl get pods

kubectl get replicasets.apps

kubectl create -f replicaset-definition-1.yaml 

kubectl create -f replicaset-definition-2.yaml 

kubectl delete replicasets.apps replicaset-1

kubectl describe pod new-replica-set-sqnpm

kubectl edit replicasets.apps new-replica-set (Either delete and recreate the ReplicaSet or Update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.)

