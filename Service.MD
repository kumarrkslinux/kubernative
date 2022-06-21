```sh
What is the type of the default kubernetes service?

controlplane ~ ➜  kubectl get service
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   4m11s

What is the targetPort configured on the kubernetes service?

controlplane ~ ➜  kubectl describe service kubernetes 
Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.43.0.1
IPs:               10.43.0.1
Port:              https  443/TCP
TargetPort:        6443/TCP
Endpoints:         10.8.83.3:6443
Session Affinity:  None
Events:            <none>

How many labels are configured on the kubernetes service?

Two, from above output

How many Endpoints are attached on the kubernetes service?

One, from above output


Create a new service to access the web application using the service-definition-1.yaml file
Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector:
  name: simple-webapp
  
service-definition-1.yaml & kubectl apply -f /root/service-definition-1.yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 8080
      port: 8080
      nodePort: 30080
  selector:
    name: simple-webapp
