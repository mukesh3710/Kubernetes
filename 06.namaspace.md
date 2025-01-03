# Kubernetes Namespace Explanation and Task Walkthrough

## Kubernetes Namespace Overview

Namespaces in Kubernetes provide a mechanism for isolating groups of resources within a single cluster. They are useful in environments where multiple users or teams operate within the same cluster. Namespaces help in avoiding name collisions and managing resource quotas effectively.

---

## Task Implementation

### 1. Create Namespaces
Create two namespaces named `ns1` and `ns2`.
```bash
kubectl create namespace ns1
kubectl create namespace ns2
```

### 2. Deployments
Create a deployment in each namespace with a single replica using the `nginx` image.
```bash
# Deployment in ns1
kubectl create deployment deploy-ns1 --image=nginx --namespace=ns1

# Deployment in ns2
kubectl create deployment deploy-ns2 --image=nginx --namespace=ns2
```

### 3. Get Pod IPs
Retrieve the IP address of the pods.
```bash
# Get Pod IP in ns1
kubectl get pods -o wide --namespace=ns1

# Get Pod IP in ns2
kubectl get pods -o wide --namespace=ns2
```

### 4. Verify Pod-to-Pod Connectivity
Exec into the pod of `deploy-ns1` and curl the IP address of the pod in `deploy-ns2`.
```bash
# Exec into a pod in ns1
kubectl exec -it <pod-name-in-ns1> --namespace=ns1 -- /bin/bash

# Curl the IP of the pod in ns2
curl <pod-IP-in-ns2>
```
You should receive a successful response.

### 5. Scale Deployments
Scale both deployments to 3 replicas.
```bash
# Scale deployment in ns1
kubectl scale deployment deploy-ns1 --replicas=3 --namespace=ns1

# Scale deployment in ns2
kubectl scale deployment deploy-ns2 --replicas=3 --namespace=ns2
```

### 6. Create Services
Create services to expose the deployments.
```bash
# Service for deploy-ns1
kubectl expose deployment deploy-ns1 --type=ClusterIP --name=svc-ns1 --namespace=ns1

# Service for deploy-ns2
kubectl expose deployment deploy-ns2 --type=ClusterIP --name=svc-ns2 --namespace=ns2
```

### 7. Test Service-to-Service Connectivity
Exec into a pod in `ns1` and curl the service in `ns2`.
```bash
# Exec into a pod in ns1
kubectl exec -it <pod-name-in-ns1> --namespace=ns1 -- /bin/bash

# Curl the service IP of svc-ns2
curl <svc-ns2-IP>
```
This should work successfully.

#### Curl Using Service Name
Try curling the service name instead of the IP address.
```bash
# Curl using service name
curl svc-ns2
```
You will likely encounter a host resolution error.

#### Use Fully Qualified Domain Name (FQDN)
Retry the curl using the FQDN.
```bash
curl svc-ns2.ns2.svc.cluster.local
```
This should resolve successfully.

### 8. Cleanup
Delete both namespaces to remove the resources.
```bash
kubectl delete namespace ns1
kubectl delete namespace ns2
