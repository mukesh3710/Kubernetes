# Kubernetes Deployment

A Kubernetes Deployment is a higher-level resource that manages a ReplicaSet to ensure the desired number of Pods are running, self-healing in case of failure, and enabling updates with zero downtime. Below, we explain its components and demonstrate end-to-end functionality.

---

## Components of a Deployment

### 1. **Metadata**
Defines the name, namespace, and labels of the Deployment.

### 2. **Spec**
Specifies the desired state of the Deployment, including:
- **Replicas**: Number of Pods to run.
- **Selector**: Labels to identify which Pods belong to the Deployment.
- **Template**: Describes the Pods, including their labels, containers, and configurations.
- **Strategy**: Defines how updates should be applied (e.g., `RollingUpdate` or `Recreate`).

### 3. **Status**
Indicates the current state of the Deployment, such as the number of available and updated replicas.

---

## End-to-End Deployment Functionality

### Step 1: Create a Deployment
```bash
kubectl create deployment nginx --image=nginx:1.23.0 --replicas=3 --dry-run=client -o yaml > nginx-deployment.yaml
```

Edit `nginx-deployment.yaml` to include the labels `tier=backend` for the Deployment and `app=v1` for the Pod template:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    tier: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: v1
  template:
    metadata:
      labels:
        app: v1
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.0
        ports:
        - containerPort: 80
```

Apply the Deployment:
```bash
kubectl apply -f nginx-deployment.yaml
```

Verify the Deployment and running Pods:
```bash
kubectl get deployments
kubectl get pods
```

---

### Step 2: Update the Image
Update the Deployment to use `nginx:1.23.4`:
```bash
kubectl set image deployment/nginx nginx=nginx:1.23.4
```

Check the rollout status:
```bash
kubectl rollout status deployment/nginx
```

Verify the Pods are using the updated image:
```bash
kubectl get pods -o wide
```

---

### Step 3: Assign a Change Cause
Add a change cause for the rollout:
```bash
kubectl annotate deployment nginx kubernetes.io/change-cause="Pick up patch version"
```

Verify the annotation:
```bash
kubectl describe deployment nginx | grep -i "change-cause"
```

---

### Step 4: Scale the Deployment
Scale the Deployment to 5 replicas:
```bash
kubectl scale deployment nginx --replicas=5
```

Verify the new number of Pods:
```bash
kubectl get deployments
kubectl get pods
```

---

### Step 5: View Rollout History
Check the rollout history:
```bash
kubectl rollout history deployment/nginx
```

---

### Step 6: Revert to Revision 1
Rollback the Deployment to revision 1:
```bash
kubectl rollout undo deployment/nginx --to-revision=1
```

Verify the rollback:
```bash
kubectl rollout status deployment/nginx
kubectl describe deployment nginx | grep -i "image"
```

Ensure Pods are using the original image `nginx:1.23.0`:
```bash
kubectl get pods -o wide
```

---

## Summary of Commands

- **Create Deployment**:
  ```bash
  kubectl create deployment
  ```
- **Apply Changes**:
  ```bash
  kubectl apply -f <file>.yaml
  ```
- **Update Image**:
  ```bash
  kubectl set image deployment/<name> <container>=<image>
  ```
- **Scale Deployment**:
  ```bash
  kubectl scale deployment/<name> --replicas=<number>
  ```
- **View Rollout History**:
  ```bash
  kubectl rollout history deployment/<name>
  ```
- **Rollback Deployment**:
  ```bash
  kubectl rollout undo deployment/<name> --to-revision=<revision>
  
