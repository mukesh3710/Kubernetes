# Kubernetes ReplicaSet: Overview and Operations

A **ReplicaSet** is a Kubernetes resource that ensures a specified number of identical pod replicas are running at any given time. It is primarily used to maintain a stable set of replica pods running at any time, ensuring that the defined number of pods are available for your application.

## Components of a ReplicaSet

A ReplicaSet consists of the following main components:

- **Pods**: The actual containers running your application.
- **Replicas**: The desired number of identical pods that should be running.
- **Selector**: A label selector that identifies the set of pods managed by the ReplicaSet.
- **Template**: A pod template that describes the pod specification (container image, labels, ports, etc.).
- **Labels**: A set of key-value pairs that help identify and select resources.

## End-to-End Functionality

A ReplicaSet ensures that the desired number of replicas of a pod are running. If a pod crashes, the ReplicaSet controller will automatically create a new one to replace it. If there are too many pods, the ReplicaSet will scale down the excess.

### Example Workflow for Creating and Managing a ReplicaSet

Let's walk through a series of tasks involving creating, updating, and scaling a ReplicaSet in Kubernetes using YAML and command line instructions.

---

## 1. **Create a New ReplicaSet Based on the NGINX Image with 3 Replicas**

### YAML Definition for ReplicaSet

Below is an example of a YAML file to create a ReplicaSet that runs 3 replicas of the `nginx` container.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
  ```
## Steps:
Save the above YAML content to a file, e.g., nginx-replicaset.yaml.
Apply the YAML to create the ReplicaSet:
kubectl apply -f nginx-replicaset.yaml
Verify the ReplicaSet and Pods:
kubectl get replicasets
kubectl get pods
You should see that 3 pods are running with the nginx image.

2. Update the Replicas to 4 from the YAML

To update the number of replicas, modify the replicas field in the YAML file.

Updated YAML File:
