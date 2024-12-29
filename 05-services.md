# Kubernetes Services

Kubernetes Services enable communication between different components within the cluster or external access to workloads. They abstract Pod networking, allowing Pods to be exposed without directly interacting with their IP addresses.

---

## Types of Kubernetes Services

### 1. ClusterIP
- **Default service type**.
- Exposes the service on an internal IP within the cluster.
- Accessible only within the cluster.

**Use Case**: Internal communication between microservices.

### 2. NodePort
- Exposes the service on each node’s IP at a static port (30000-32767).
- Accessible from outside the cluster using `<NodeIP>:<NodePort>`.

**Use Case**: Development and testing environments for simple external access.

### 3. LoadBalancer
- Exposes the service externally using a cloud provider’s load balancer.
- Maps traffic to NodePorts and then the service.

**Use Case**: Production applications requiring managed external access.

### 4. ExternalName
- Maps the service to an external DNS name.
- No proxying; only works for DNS resolution.

**Use Case**: Accessing external services (e.g., databases or APIs).

---

## Architecture of Services
- **Selectors**: Match Pods to the service using labels.
- **Endpoints**: Define the set of IP addresses for the Pods backing the service.
- **Service Proxy**: Forwards traffic from the service to the Pod's IP.
- **Cluster DNS**: Allows services to be accessed by name.

---

## Tasks and Examples

### 1. Create a Service (ClusterIP)
```bash
kubectl create service clusterip myapp --tcp=80:80
```

### 2. Create a Deployment and Expose a Pod
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.4-alpine
        ports:
        - containerPort: 80
```
```bash
kubectl apply -f myapp-deployment.yaml
kubectl expose deployment myapp --type=ClusterIP --port=80 --target-port=80
```

### 3. Scale the Deployment to 2 replicas
```bash
kubectl scale deployment myapp --replicas=2
```

### 4. Temporary Pod and Internal `wget` Command
```bash
kubectl run busybox --image=busybox --rm -it -- /bin/sh
wget <ClusterIP-of-myapp-service>
```

### 5. External `wget` Command
- Initially, this fails because `ClusterIP` is internal-only.

### 6. Change Service Type to NodePort
```bash
kubectl patch service myapp -p '{"spec": {"type": "NodePort"}}'
```
- Obtain the `NodePort`:
```bash
kubectl get svc myapp
wget <NodeIP>:<NodePort>
```

---

## Discussion

### Expose Pods Without a Deployment
Yes, you can expose individual Pods using a service by directly setting their `selector` to match the Pod’s labels. However, this is not recommended for production as Pods lack resiliency.

### When to Use Service Types
1. **ClusterIP**: Internal service communication, microservices.
2. **NodePort**: Simple external access, dev/test environments.
3. **LoadBalancer**: Production-grade external access.
4. **ExternalName**: Redirect traffic to external services.

### Real-World Usage
In production, **LoadBalancer** is commonly used, often fronted by Ingress for advanced routing and SSL termination.

---

## Production-Ready Service Deployment

### YAML Example
```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  namespace: production
spec:
  selector:
    app: webapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp-container
        image: webapp:1.0.0
        ports:
        - containerPort: 8080
```

### Explanation
- **Service**:
  - Type `LoadBalancer` enables external access.
  - Maps port `80` to container port `8080`.
- **Deployment**:
  - Runs 3 replicas for high availability.
  - Selectors ensure Pods are targeted by the service.

### Deployment Steps
1. Apply the YAML:
   ```bash
   kubectl apply -f production-service.yaml
   ```
2. Verify the Deployment and Service:
   ```bash
   kubectl get deployments -n production
   kubectl get svc -n production
   ```
3. Test external access using the LoadBalancer's external IP.
