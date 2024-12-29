# Kubernetes Pod Tasks

## Task 1: Create a Pod Imperatively

Use the following command to create a Pod using the nginx image:

```bash
kubectl run nginx --image=nginx --restart=Never
```

Verify the Pod is running:
```bash
kubectl get pods
```

## Task 2: Generate YAML from Existing Pod and Modify

1. Export the YAML of the nginx Pod created in Task 1:
   ```bash
   kubectl get pod nginx -o yaml > nginx-pod.yaml
   ```

2. Open `nginx-pod.yaml` and update the `metadata.name` field to `nginx-new`:

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx-new
     labels:
       run: nginx
   spec:
     containers:
     - image: nginx
       name: nginx
   ```

3. Create the new Pod using the modified YAML:
   ```bash
   kubectl apply -f nginx-pod.yaml
   ```

4. Verify the new Pod is running:
   ```bash
   kubectl get pods
   ```

## Task 3: Apply YAML with Errors and Troubleshoot

### Problematic YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: test
  name: redis
spec:
  containers:
  - image: rediss
    name: redis
```

### Steps to Troubleshoot

1. Attempt to apply the YAML:
   ```bash
   kubectl apply -f redis-pod.yaml
   ```

2. Observe the error:
   ```bash
   error: unable to pull image "rediss": invalid image name
   ```

3. Edit the YAML to use the correct image (`redis` instead of `rediss`):

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     labels:
       app: test
     name: redis
   spec:
     containers:
     - image: redis
       name: redis
   ```

4. Apply the corrected YAML:
   ```bash
   kubectl apply -f redis-pod.yaml
   ```

5. Verify the Pod is running:
   ```bash
   kubectl get pods
   ```

6. Check the Pod logs to ensure it is working correctly:
   ```bash
   kubectl logs redis
   
