# Kubernetes Labels, Selectors, Namespaces, Annotations, and Static Pods

## Labels and Selectors

### Labels
Labels are key-value pairs attached to Kubernetes objects like pods, nodes, and services. They are used to organize and select subsets of objects based on their labels.

#### Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: labeled-pod
  labels:
    app: myapp
    env: test
spec:
  containers:
    - name: nginx
      image: nginx
```

### Selectors
Selectors allow you to query Kubernetes objects based on their labels. Two types of selectors are supported:
- **Equality-based**: Matches objects with specific key-value pairs.
- **Set-based**: Matches objects whose label values match a set of conditions.

#### Example
```bash
# Get all pods with the label env=test
kubectl get pods -l env=test

# Get all pods with labels app=myapp and env=test
kubectl get pods -l app=myapp,env=test
```

---

## Labels vs. Namespaces

| Feature       | Labels                          | Namespaces                     |
|---------------|---------------------------------|---------------------------------|
| **Purpose**   | Categorize objects              | Isolate groups of objects      |
| **Scope**     | Cluster-wide                    | Namespace-specific             |
| **Flexibility**| Dynamic, multiple labels per object | Static, one namespace per object |

---

## Annotations

Annotations are used to store non-identifying metadata about Kubernetes objects. They can be used for external tools and integrations.

#### Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: annotated-pod
  annotations:
    description: "This is an example pod."
    maintainer: "team@example.com"
spec:
  containers:
    - name: nginx
      image: nginx
```

---

## Static Pods

### Definition
Static pods are managed directly by the kubelet on each node, without using the Kubernetes API server. They are defined as manifest files in a specific directory on the node.

### Default Static Pod Manifest Directory
On the control plane node, static pod manifests are typically located in:
```bash
/etc/kubernetes/manifests
```

#### Example: Restarting Control Plane Components
1. SSH into the control plane node.
2. Navigate to the static pod manifest directory:
   ```bash
   cd /etc/kubernetes/manifests
   ```
3. Modify or delete the manifest files to restart components like `kube-apiserver` or `kube-scheduler`.

---

## Manual Pod Scheduling

### Scheduling a Pod Without a Scheduler
Create a pod and assign it to a node manually by specifying the `nodeName` in the pod spec.

#### Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: manual-scheduling-pod
spec:
  containers:
    - name: nginx
      image: nginx
  nodeName: <node-name>
```

Apply the manifest:
```bash
kubectl apply -f manual-scheduling-pod.yaml
```

---

## Labels and Filtering Pods

### Task: Creating and Filtering Pods

1. Create three pods with specific labels:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod1
  labels:
    env: test
spec:
  containers:
    - name: nginx
      image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: pod2
  labels:
    env: dev
spec:
  containers:
    - name: nginx
      image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: pod3
  labels:
    env: prod
spec:
  containers:
    - name: nginx
      image: nginx
```

Apply the manifests:
```bash
kubectl apply -f pods-with-labels.yaml
```

2. Filter pods using labels:
```bash
# Get pods with label env=dev
kubectl get pods -l env=dev

# Get pods with label env=prod
kubectl get pods -l env=prod
```
