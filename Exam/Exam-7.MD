#### Create a new service account with the name pvviewer. Grant this Service account access to list all PersistentVolumes in the cluster by creating an appropriate cluster role called pvviewer-role and ClusterRoleBinding called pvviewer-role-binding.
#### Next, create a pod called pvviewer with the image: redis and serviceAccount: pvviewer in the default namespace.

#### ServiceAccount: pvviewer /  ClusterRole: pvviewer-role / ClusterRoleBinding: pvviewer-role-binding /Pod: pvviewer / Pod configured to use ServiceAccount pvviewer ?
