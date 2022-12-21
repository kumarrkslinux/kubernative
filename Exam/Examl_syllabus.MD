

#### Certified Kubernetes Administrator Exam Modules


![ScreenShot](https://github.com/kumarrkslinux/kubernetes/blob/main/Exam/Exam.JPG)


#### There are five modules in the exam:

- Cluster Architecture, Installation, and Configuration
- Workloads and Scheduling
- Services and Networking
- Storage
- Troubleshooting

#### The CKA exam is an open-book exam. You have access to the below resources:

- https://kubernetes.io/docs/
- https://github.com/kubernetes
- https://kubernetes.io/blog/

#### The CKA certification is valid for three years.

-----------------------------------------------------------------------------
- Cluster Architecture, Installation & Configuration	25%


- Storage10%
  - Understand storage classes, persistent volumes
  - Understand volume mode, access modes and reclaim policies for volumes
  - Understand persistent volume claims primitive
  - Know how to configure applications with persistent storage

- Troubleshooting30%
  - Evaluate cluster and node logging
  - Understand how to monitor applications
  - Manage container stdout & stderr logs
  - Troubleshoot application failure
  - Troubleshoot cluster component failure
  - Troubleshoot networking

- Workloads & Scheduling15%
  - Understand deployments and how to perform rolling update and rollbacks
  - Use ConfigMaps and Secrets to configure applications
  - Know how to scale applications
  - Understand the primitives used to create robust, self-healing, application deployments
  - Understand how resource limits can affect Pod scheduling
  - Awareness of manifest management and common templating tools

- Cluster Architecture, Installation & Configuration25%
  - Manage role based access control (RBAC)
  - Use Kubeadm to install a basic cluster
  - Manage a highly-available Kubernetes cluster
  - Provision underlying infrastructure to deploy a Kubernetes cluster
  - Perform a version upgrade on a Kubernetes cluster using Kubeadm
  - Implement etcd backup and restore

- Services & Networking20%
  - Understand host networking configuration on the cluster nodes
  - Understand connectivity between Pods 
  - Understand ClusterIP, NodePort, LoadBalancer service types and endpoints
  - Know how to use Ingress controllers and Ingress resources 
  - Know how to configure and use CoreDNS
  - Choose an appropriate container network interface plugin
    
#### syllabus

Storage10%
  - Understand storage classes, persistent volumes
  - Understand volume mode, access modes and reclaim policies for volumes
  - Understand persistent volume claims primitive
  - Know how to configure applications with persistent storage

Troubleshooting30%
  - Evaluate cluster and node logging
  - Understand how to monitor applications
  - Manage container stdout & stderr logs
  - Troubleshoot application failure
  - Troubleshoot cluster component failure
  - Troubleshoot networking

Workloads & Scheduling15%
  - Understand deployments and how to perform rolling update and rollbacks
  - Use ConfigMaps and Secrets to configure applications 
  - Know how to scale applications
  - Understand the primitives used to create robust, self-healing, application deployments
  - Understand how resource limits can affect Pod scheduling
  - Awareness of manifest management and common templating tools
   
 - Workloads & Scheduling 15%
   - POD
      - Single container and multi container with respective images
      - Architecture of kubernetes components 
   - Labels 
      - Labels are key value pairs that are attected to object, such as pods  (kubectl get nodes --show-labels)
   - Selectors
      - Selectors allows us to filter the objects based on labels (kubectl get pods -l env=prod)
   - Command and arguments 
      - Launch a POD busybox with the image of 1.28 pod should be sleep for 3800 sec and exit
   - Deployments
      - Rolling updates 
      - Rollbacks 
      - scalling 
      - Record instruction 
   - Daemonsets
      -  A Daemonsets can ensures all the nodes run a copy of a pod
      -  As nodes added to cluster, Pods are added to them 
   - Nodeselctor
     - We should be shedule a pod on particular node which has disk=ssd
   - Taints and toleration 
     - Taints are used to repel the pods from a specific nodes
     - In order to enter the tain worker node, we need a pass, this is referred as Toleration(Noshedule/Noexecute)
     - List out number of nodes, which has tained of Noshedule 


Cluster Architecture, Installation & Configuration25%
  - Manage role based access control (RBAC)
  - Use Kubeadm to install a basic cluster
  - Manage a highly-available Kubernetes cluster
  - Provision underlying infrastructure to deploy a Kubernetes cluster
  - Perform a version upgrade on a Kubernetes cluster using Kubeadm
  - Implement etcd backup and restore

Services & Networking20%
  - Understand host networking configuration on the cluster nodes
  - Understand connectivity between Pods
  - Understand ClusterIP, NodePort, LoadBalancer service types and endpoints
  - Know how to use Ingress controllers and Ingress resources
  - Know how to configure and use CoreDNS
  - Choose an appropriate container network interface plugin
  
