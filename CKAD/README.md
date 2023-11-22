# Certified Kubernetes Application Developer

#### Kubernetes Architecture


- Nodes (Minions)
- Cluster - set of nodes grouped together
- Master - another node in the cluster which monitors the worker nodes with in the cluster.
- ***Components***
  - API Server - front end for kubernetes. 
  - etcd - distributed, reliable key/value store. Implements logging. 
  - Scheduler - Distributing load 
  - Controllers - When containers goes down, controllers will help in bringing them up.
  - Container Runtime - this is needed to run the containers.
  - kubelet - agent it is responsible for running containers as expected.

- ***Master vs Worker Nodes***
  - Master - will have kube api server, etcd, controller manager, scheduler.
  - Worker Node - will have kubelet, container runtime.

#### Docker vs ContainerD

- CRI - container runtime interface -

## Configuration

#### Define, build and modify container images


#### Commands, Arguments and Arguments in Docker

ARG - we can use arguments to dynamically send the values to CMD pattern.
- CMD ["sleep", "5"]
- ENTRYPOINT - defines what command to be run as soon as the container starts.
  - ENTRYPOINT ["sleep"]
  - CMD ["5"]

#### Commands and Arguments in Kubernetes

- We can override the ENTRYPOINT and CMD fields in the Dockerfile when a pod definition file is created.

- ```
   apiversion: v1
   kind: Pod
   metadata:
     name: ubuntu-sleeper-pod
   spec:
    containers:
      name: ubuntu-sleeper
      image: ubuntu
      command: ["sleep"] # this will override the ENTRYPOINT in docker file
      args: ["20"] # This will override the CMD arguments.
  ```

#### Environmental Variable

- env: property array can be used to set the environment variables.
  ```
  env:
    - name: 'APP'
      color: 'PINK'
  ```

#### Configmaps

- config maps are key/value pairs. 
- These are create separately to store the data related to application and injected into the pod, which are in turn used by container that runs inside the pod.
- Imperative way
  ```
    kubectl create configmap \
      app-config --from-literal=<key>=<value>
  ```
  ```
   kubectl create configmap \
     app-config --from-file=app-config.yaml
  ```

- ***Declarative Approach***

  ```
   apiVersion: v1
   kind: configmap
   metadata:
     name: app-config
   data:
     APP-COLOR: BLUE
     BACKGROUND-COLOR: GREEN
  ```
  ``` 
   kubectl create -f configmap.yaml 
  ```
- ***To list and describe configmap***
  ```
   kubectl get configmap
   kubectl describe configmaps
  ```

- ***Inject configmap into pod***
  ```
    envFrom:
      - configmapRef: 
         name: app-config
  ```

#### Secrets

- ***Create Secret***
  ```
    kubectl create secret generic --from-literal=DB-PASSWORD=<value>
  
    kubectl create secrect generix --from-file=filepath
  ```

- ***Inorder to get the encrypted version of secret***
  ```
    echo -n 'password' | base64
  ```
  
- *** List and describe secret***
  ``` 
    kubectl get secrets 
    kubectl describer secret <secret-name>
  ```
  
- *** Inject secrets into pod***
  ```
    envFrom: 
      - secretRef: 
           name: app-secret
  ```

#### Encrypting Secret data at rest

- Explore later

#### Security in Docker

- Options to run
  - ```
      --cap-previligied
      
    ```

#### Container Security

- securityContext tag is used to specify the security on the pod.

- ```
    securityContext:
  ```

#### Resource Requirements

- kubescheduler is responsible to place a pod on a particular node.
- If nodes does not have a resources, scheduler will place pod on a separate node.
- If all nodes are full, then the scheduler will scheduler will not schedule the deployment and deployment will be in the pending state.
- An amount of CPU, MEM can be defined when creating pod - called as resource requirement.

  ```
    resources:
     requests:
       memory: "1Ki",
       cpu: 1m
     limits:
      memory: "2Ki"
      cpu: 2m
  ```

  - ***No Requests and Limits***

    - requests = limits
  - ***Limit Range***
    
    - ```
         apiVersion: v1
         kind: LimitRange
         metdata:
           name: cpu-resource-constarint
         spec:
           limits:
           default: # Limit
            cpu: 500m
           defaultRequest: # FRequest
             cpu: 500m
           max: # limit
             cpu: 1
           min: # request
             cpu: 100m
           type: container
      ```
    - When a pod is created there limits are assigned to a newly created container.
    
  - ***Resource Quota***
    - This can be used to set the overall cpu and memory limit on a particular namespace.
    - ``` 
       apiVersion: v1
       kind: ResourceQuota
       metadata:
         name: resource-quota
       spec:
         hard:
           requests.cpu: 4
           requsts.memory: 4Gi
           limits.cpu: 10
           limits.memory: 10Gi 
      ```
#### Service Accounts

- Service accounts are used by machines
   ```
     kubectl create serviceaccout <accout-name>
     kubectl get serviceaccount
    ```
- As service account is created, a token will be generated and will be mounted as a secret object.
- If any third party api needs to access k8s cluster then the token needs to be used, in order to pass authentication.
  ```
   kubectl describe serviceaccount <service-account-name>
  ```

#### Taints and Tolerations

- Pod to Node relationship
- When a Taint is applied on a Node, only the pods that are tolerant to the taint will be allowed to be deployed on that Node
- This is useful when the pod has a special requirements.

  ```
   kubectl taint nodes node-name key=value:taint-effect
  
   kubectl taint nodes node1 app=blue:NoSchedule
  ```

- ***Possible taint-effect values***
  
  - NoSchedule
  - PreferNoSchedule
  - NoExecute

-  ***In pod definition file***
   ``` 
    tolerations:
      key: "app"
      operator: "Equal"
      value: "blue"
      taint-effect: "NoSchedule"
   ```
- ***To remove taint*** 
- Here catch is that mentioning - towards the end.
  ```
  kubectl taint nodes node key=value:effect-
  ```

#### Node Selector Logging

- This is used to place a pod on a particular node.
- ***In Pod definition file***
  ```
  spec:
    nodeSelectors:
       size: Large
  ```
- ***To Create label on node***
  ```
    kubectl label nodes node-name key=value
    i.e
    kubectl label nodes node-1 size=Large
  ```

#### Node Affinity

- In order to handle complex cases to define where to place a particular pod, node affinity is used.
  
  ```
   affinity:
     requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
       - matchExpressions:
           - key: size
             operator: in
             values:
               - Large
               - Medium
  ```

- ***Node Affinity Types***

  - When a node is not labelled, the description mentioned under nodeAffinity helps the scheduler to decide what to do with the pod.
  - required***DuringScheduling***IgnoreDuringExecution.
  - preferred***DuringScheduling***ignoredDuringExecution.

### Taints and Tolerants vs Node Affinity

- 
