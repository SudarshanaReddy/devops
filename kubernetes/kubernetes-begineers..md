## Pods

- YAML files are used as definition to create kubernetes pods, services, configmaps.
- Important k8s yaml attributes.

    - apiVersion - reresents the version of K8S Api we use.
    - kind - represents the type of object

        - Pod, configmap, replicaset.
    - metadata - data about the object
    - spec - represents the spec about the k8s object we are trying to create. Typically we will specify the image needed in order to create the container.
   
     ```
      apiVersion: V1
      kind: Pod
      metadata:
        name: myapp-pod
        labels: 
          app: myapp
          type: front-end
      
      spec:
        containers: 
          - name: nginx-container
            image: nginx
               
     ```
- To create the kubernetes pod. -f stands for file name.
    ```
      kubectl create -f pod-definition.yaml
    ```
- To list pods create 
   ```
     kubectl get pods
   ```
- To describe pod to have all the information
  ```
   kubectl descibe pod
  ```

### Replication controller and ReplicaSets
- Replication Controller helps in running multiple pods at the same time, to have high availability.
- Load Balancing and Scaling can be done with help of RC across nodes.
- Replica Set if the new way of setting up the replication.
- Replica Set is a process that monitors the pods and deploys a new pod in case if a particular pod fails.

### Labels and Selectors

- Labels will help the replica set to identify which pods to monitor and deploy a new one in case of failure.

### Scaling

- We can update the replicas in the definition file and run the below command
  ```
    kubectl create -f replicaset.yaml
  ```
  ```
    kubectl scale --replicas=6 -f rc-definition.yaml
  ```
  
  ```
   kubectl scale --replicas=6 replicaset myapp-replicaset
  ```

- Replicaset definition
 
  ```
  apiVersion: apps/v1
  kind: ReplicaSet
  metadata:
     name: myapp-replicaset
     labels:
       app: myapp
  spec:
    replicas: 3
    selector:
      matchLabels:
        env: production
  template:
    metadata:
      name: nginx-2
      labels:
         env: production
    spec:
      containers:
         - name: nginx
           image: nginx
  ```

### Deployments

- Deployments -> Replica Set -> Pods

- ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
     name: myapp-replicaset
     labels:
       app: myapp
  spec:
    replicas: 3
    selector:
      matchLabels:
        env: production
  template:
    metadata:
      name: nginx-2
      labels:
         env: production
    spec:
      containers:
         - name: nginx
           image: nginx
  ```

### Rollouts and updates

- when a deployment is created - a new rollout will be created and this will create the revision.
  
  ```
    Deployment -> Rollout -> Revision
  ```

  ```
    kubectl rollout status deployment/deployment-name
  ```

### Deployment Strategy

- Recreate
  - In this all the deployment instances are destroyed and new deployment is created in which case Appliocation will be down for certaini duration.
- Rolling Update
  - This is the default deployment strategy.
  - In this older version is taken down and newer version will be bought up one by one so that application will not be down.

### Update the deployment.

- We can update the deployment file and use the apply dommand to update the deployment.

  ```
   kubectl apply -f deployment.yaml
  ```

### Rollback

 ```
   kubectl rollout undo deployment/deployment-name
 ```

### Networking in Kubernetes

- Each pod in kubernetes will get a dynamic ip address in the range of 10.244.0.*
- Cluster Networking
  - If there are multiple nodes in a single cluster, there is a problem of ip address conflicts.

### Services

- Services enable connection between groups of pods.
- ***Node Port Service***
  - This is a service which listens on a particular port on the node in the cluster and forwards the request to a pod on the node.  
- ***Service Types***
  - NodePort - 
  - Cluster IP
  - Load Balancer

- ***Service definition yaml***
  ```
    apiVersion: v1
    kind: Service
    metadata:
       name: myapp-service
    spec:
       type: NodePort
       ports:
         - port: 80 # port on the pod
           targetPort: 80 # port on the service
           nodePort: 30004 # port on the node - minikube
       selector:
           app: myapp
  ```

- ***Cluster IP Service***
- ***Load Balancer Service***
