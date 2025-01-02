# Kubernetes Resource Requests and Limits

## What Are Resource Requests and Limits?
Resource requests and limits are mechanisms in Kubernetes that help manage CPU and memory allocation for pods. They ensure fair resource distribution and prevent one pod from consuming all resources on a node.

### Resource Requests
- **Definition**: The minimum amount of CPU or memory that a container requires to run.
- **Impact**: Used by the scheduler to determine where a pod can be placed. Ensures the node has sufficient resources to meet the pod's needs.

### Resource Limits
- **Definition**: The maximum amount of CPU or memory that a container can use.
- **Impact**: Prevents a single container from consuming excessive resources, protecting other containers on the same node.

---

## Example: Setting Resource Requests and Limits

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo-pod
spec:
  containers:
    - name: nginx
      image: nginx
      resources:
        requests:
          memory: "128Mi"
          cpu: "500m"
        limits:
          memory: "256Mi"
          cpu: "1"
```
- **Requests**:
  - `memory: 128Mi`: The pod requests 128 MiB of memory.
  - `cpu: 500m`: The pod requests 500 millicores (0.5 cores) of CPU.
- **Limits**:
  - `memory: 256Mi`: The pod can use up to 256 MiB of memory.
  - `cpu: 1`: The pod can use up to 1 core of CPU.

Apply the manifest:
```bash
kubectl apply -f resource-demo-pod.yaml
```

---

## Resource Management in Real-World Projects

### Ensuring Performance
Resource requests and limits ensure that critical applications receive the necessary resources to perform efficiently.
- For example, a database pod might request high memory and CPU resources to handle large queries efficiently.

### Avoiding Overcommitment
By setting limits, Kubernetes prevents any single pod from consuming all the resources on a node, ensuring other applications remain unaffected.

### Optimizing Costs
Resource requests help allocate just enough resources for workloads, avoiding over-provisioning and reducing costs in cloud environments.

---

## Monitoring Resource Usage
To monitor how much CPU and memory a pod is using:
```bash
kubectl top pods
```

To check the resource requests and limits of a pod:
```bash
kubectl describe pod <pod-name>
```

---

## Advanced Example: Multiple Containers with Different Resource Needs

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
    - name: frontend
      image: nginx
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
    - name: backend
      image: busybox
      command: ["sh", "-c", "while true; do echo hello; sleep 10; done"]
      resources:
        requests:
          memory: "256Mi"
          cpu: "1"
        limits:
          memory: "512Mi"
          cpu: "2"
```

- The frontend container has lower resource requirements compared to the backend.
- Ensures both containers can coexist on the same node without resource contention.

Apply the manifest:
```bash
kubectl apply -f multi-container-pod.yaml
```

---

Resource requests and limits are essential tools in Kubernetes for optimizing resource allocation, improving cluster stability, and managing costs effectively. Let me know if you need further details or examples!
