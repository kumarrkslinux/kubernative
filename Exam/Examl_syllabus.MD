#### Certified Kubernetes Administrator Exam Modules
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
     - Role-based access control (RBAC)
     - How to install and configure a Kubernetes cluster using kubeadm
     - How to Upgrade Your Kubernetes Cluster Version
     - How to Backup and Restore an ETCD Cluster
- Workloads & Scheduling	15% 
     - How to deploy an application and expose the app using a service
     - How to scale and update the deployments
     - How to configure healthcecks for your application
     - MultiContainer pod/sidecar containers
     - How to configure a pod to use a ConfigMap
     - How to configure a pod to use secrets
- Services & Networking	20% 
     - How to creating networking policies
     - How to create an ingress resource
- Storage	10% 
     - Create an NGINX Pod and mount index.html as PersistentVolume.
     - Create PersistentVolume and PersistentVolume Claim
     - The config pod uses the PersistentVolume Claim
     - PersistentVolume. Some storage classes support resizing the volume
- Troubleshooting	30%
     - One of the worker nodes in cluster is not in a ready state. Troubleshoot the node and bring it online.

#### syllabus

Storage10%
Understand storage classes, persistent volumes
Understand volume mode, access modes and reclaim policies for volumes
Understand persistent volume claims primitive
Know how to configure applications with persistent storage

Troubleshooting30%
Evaluate cluster and node logging
Understand how to monitor applications
Manage container stdout & stderr logs
Troubleshoot application failure
Troubleshoot cluster component failure
Troubleshoot networking

Workloads & Scheduling15%
Understand deployments and how to perform rolling update and rollbacks
Use ConfigMaps and Secrets to configure applications
Know how to scale applications
Understand the primitives used to create robust, self-healing, application deployments
Understand how resource limits can affect Pod scheduling
Awareness of manifest management and common templating tools

Cluster Architecture, Installation & Configuration25%
Manage role based access control (RBAC)
Use Kubeadm to install a basic cluster
Manage a highly-available Kubernetes cluster
Provision underlying infrastructure to deploy a Kubernetes cluster
Perform a version upgrade on a Kubernetes cluster using Kubeadm
Implement etcd backup and restore

Services & Networking20%
Understand host networking configuration on the cluster nodes
Understand connectivity between Pods
Understand ClusterIP, NodePort, LoadBalancer service types and endpoints
Know how to use Ingress controllers and Ingress resources
Know how to configure and use CoreDNS
Choose an appropriate container network interface plugin
