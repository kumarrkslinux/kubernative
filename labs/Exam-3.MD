### Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Next upgrade the deployment to version 1.17 using rolling update.

### Image: nginx:1.16

### Task: Upgrade the version of the deployment to 1:17

Weight: 12 

``` 
controlplane ~/CKA ➜  kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1 
deployment.apps/nginx-deploy created

controlplane ~/CKA ➜  kubectl get deployments.apps 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
gold-nginx     1/1     1            1           58m
nginx-deploy   1/1     1            1           10s

controlplane ~/CKA ➜  kubectl get pods 
NAME                            READY   STATUS    RESTARTS   AGE
gold-nginx-7cf65dbf6d-m8ktt     1/1     Running   0          44m
nginx-deploy-7cfd4975d6-q9b57   1/1     Running   0          19s

controlplane ~/CKA ➜  kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.16.1

controlplane ~ ✖ kubectl rollout status deployment/nginx-deploy 
deployment "nginx-deploy" successfully rolled out
``` 

### A new deployment called alpha-mysql has been deployed in the alpha namespace. However, the pods are not running. Troubleshoot and fix the issue. The deployment should make use of the persistent volume alpha-pv to be mounted at /var/lib/mysql and should use the environment variable MYSQL_ALLOW_EMPTY_PASSWORD=1 to make use of an empty root password.

### Important: Do not alter the persistent volume.

### Troubleshoot and fix the issues

Weight: 20

``` 
