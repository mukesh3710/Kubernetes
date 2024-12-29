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
  
---
---

# Production-Ready Kubernetes Deployment

This document demonstrates a production-ready `Deployment` configuration and explains each parameter to ensure reliability, scalability, and maintainability in real-world scenarios.

---

## Deployment YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
  namespace: production
  labels:
    app: webapp
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
        - containerPort: 80
        resources:
          requests:
            memory: "128Mi"
            cpu: "500m"
          limits:
            memory: "256Mi"
            cpu: "1"
        readinessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 20
        env:
        - name: ENV
          value: "production"
        volumeMounts:
        - name: config-volume
          mountPath: /etc/config
      volumes:
      - name: config-volume
        configMap:
          name: webapp-config
      nodeSelector:
        disktype: ssd
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - webapp
            topologyKey: kubernetes.io/hostname
      tolerations:
      - key: "key"
        operator: "Equal"
        value: "value"
        effect: "NoSchedule"
```

---

## Explanation of Parameters

### `apiVersion`
Specifies the API version. Use `apps/v1` for Deployments.

### `kind`
Indicates the resource type, which is `Deployment`.

### `metadata`
- **`name`**: Unique name for the Deployment.
- **`namespace`**: Defines the namespace in which the Deployment exists. Use `production` for production environments.
- **`labels`**: Key-value pairs to categorize and identify the resource.

### `spec`
Describes the desired state of the Deployment.

#### `replicas`
Specifies the number of Pods to maintain. Here, 3 replicas ensure high availability.

#### `selector`
Defines how to identify Pods managed by the Deployment. Pods with the label `app: webapp` are selected.

#### `template`
Defines the Pod template to create.

##### `template.metadata.labels`
Labels assigned to the Pods for identification and service discovery.

##### `template.spec`
Specifies the containers and other runtime configurations for the Pod.

- **`containers`**: Defines the containers.
  - **`name`**: Name of the container.
  - **`image`**: Container image with a specific version tag for consistency.
  - **`ports`**: Specifies container ports to expose.
  - **`resources`**: Ensures resource requests and limits for efficient resource allocation.
    - **`requests`**: Minimum resources guaranteed.
    - **`limits`**: Maximum resources the container can use.
  - **`readinessProbe`**: Ensures the container is ready to serve traffic.
  - **`livenessProbe`**: Checks if the container is alive and running.
  - **`env`**: Environment variables passed to the container.
  - **`volumeMounts`**: Mounts volumes into the container.

- **`volumes`**: Configures volumes for the Pods.
  - **`configMap`**: Uses a ConfigMap to provide configuration data to the container.

- **`nodeSelector`**: Ensures Pods are scheduled on nodes with specific labels, such as `disktype: ssd`.

- **`affinity`**:
  Configures Pod affinity and anti-affinity rules.
  - **`podAntiAffinity`**: Prevents scheduling Pods on the same node to enhance reliability.

- **`tolerations`**: Allows Pods to be scheduled on tainted nodes with specific conditions.

---

## Deployment Steps

1. Save the above YAML to `webapp-deployment.yaml`.
2. Apply the Deployment:
   ```bash
   kubectl apply -f webapp-deployment.yaml
   ```
3. Verify the Deployment and Pods:
   ```bash
   kubectl get deployments -n production
   kubectl get pods -n production
   ```
4. Check the Pod logs:
   ```bash
   kubectl logs <pod-name> -n production
   ```
5. Scale the Deployment:
   ```bash
   kubectl scale deployment webapp-deployment --replicas=5 -n production
   ```
6. Update the image:
   ```bash
   kubectl set image deployment/webapp-deployment webapp-container=webapp:1.1.0 -n production
   ```
7. Verify rollout status:
   ```bash
   kubectl rollout status deployment/webapp-deployment -n production
   ```
8. Check rollout history:
   ```bash
   kubectl rollout history deployment/webapp-deployment -n production
   ```
9. Rollback if needed:
   ```bash
   kubectl rollout undo deployment/webapp-deployment -n production
   
