1. ##### create a new pod called admin-pod with bisybox all the pod to able to set system_time
   The container should sleep for 3200 seconds
   
2. A kubeconfig file called test.kubeconfig has been created under /root/CKA/ 
   There is something wrong in the configuration need to troubleshoot and findout the issue. 
    Use the command kubectl config view check current config and compare with the file. 
    
3. Create a deployment called webproject with image busybox:1.16 and replica 1. Next upgrade the deployment to version 1.17 using rolling update. 
   Make sure that upgrade is recorded in the resource annotation 
