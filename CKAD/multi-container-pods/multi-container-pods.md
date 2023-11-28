## Multi Container Pods

- Design Patterns for multi container pods.

  - Side car
  - Adapater
  - Ambassador

- ***Init Container***

  - It is a container specified with in a pod, which is task whick will run only once, when the pod is created.
  - If init container fails to start, pod will be restarted until it succedes.
