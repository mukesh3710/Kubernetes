# Kubernetes Pod deployments 

## Imperative Way
The imperative way involves issuing direct commands or API calls to the Kubernetes cluster to perform operations. This approach is suitable for quick tasks or experiments. Examples include:
- Using `kubectl` commands like `kubectl run`, `kubectl create`, or `kubectl delete`.
- Directly interacting with the Kubernetes API via tools like `curl` or Postman.

Advantages:
- Quick and straightforward for one-off tasks.
- Does not require creating or managing additional files.

Disadvantages:
- Difficult to track changes or reproduce configurations.
- Not suitable for managing complex or large-scale deployments.

## Create a Pod Imperatively

Use the following command to create a Pod using the nginx image:

```bash
kubectl run nginx --image=nginx --restart=Never
```

Verify the Pod is running:
```bash
kubectl get pods
```

---

## Declarative Way
The declarative way involves creating and applying manifest files to define the desired state of resources in the cluster. Kubernetes ensures the actual state matches the desired state.
- Manifest files are typically written in YAML or JSON.
- Use `kubectl apply` to apply the configuration.

Advantages:
- Provides version control and reproducibility.
- Easier to manage and maintain complex configurations.

Disadvantages:
- Requires more setup time compared to imperative commands.
- May be overkill for simple or one-off tasks.

## Generate YAML from Existing Pod and Modify

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

---

# Generating YAML for Pod Creation Using Imperative Commands

Kubernetes allows you to generate YAML manifest files for resources using imperative commands. This is especially useful when you want to start with a basic configuration and then modify it further. Below, we outline the steps to create YAML for a Pod using advanced options and provide an example.

## Imperative Command to Generate Pod YAML

Use the `kubectl run` command with advanced options to generate the YAML for a Pod.

### Command Syntax
```bash
kubectl run <pod-name> \
  --image=<image-name> \
  --restart=Never \
  --dry-run=client \
  -o yaml
```

### Explanation of Options:
- `<pod-name>`: The name of the Pod to create.
- `--image`: Specifies the container image to use.
- `--restart=Never`: Ensures the resource created is a Pod (not a Deployment or ReplicaSet).
- `--dry-run=client`: Simulates the command without creating the resource.
- `-o yaml`: Outputs the configuration in YAML format.

### Example
Create a Pod using the `nginx` image and export the YAML:

```bash
kubectl run nginx-pod \
  --image=nginx \
  --restart=Never \
  --dry-run=client \
  -o yaml > nginx-pod.yaml
```

The above command generates a file named `nginx-pod.yaml` with the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-pod
  name: nginx-pod
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
dnsPolicy: ClusterFirst
restartPolicy: Never
status: {}
```

## Advanced Options for Pod YAML Generation

1. **Adding Environment Variables**:
   ```bash
   kubectl run nginx-pod \
     --image=nginx \
     --env="ENV_VAR=value" \
     --restart=Never \
     --dry-run=client \
     -o yaml > nginx-pod.yaml
   ```

2. **Specifying Resource Limits**:
   ```bash
   kubectl run nginx-pod \
     --image=nginx \
     --limits=cpu=200m,memory=512Mi \
     --restart=Never \
     --dry-run=client \
     -o yaml > nginx-pod.yaml
   ```

3. **Defining Ports**:
   ```bash
   kubectl run nginx-pod \
     --image=nginx \
     --port=80 \
     --restart=Never \
     --dry-run=client \
     -o yaml > nginx-pod.yaml
   ```

4. **Assigning Node Selector**:
   ```bash
   kubectl run nginx-pod \
     --image=nginx \
     --restart=Never \
     --dry-run=client \
     -o yaml | sed "/spec:/a \  nodeSelector:\n    disktype: ssd" > nginx-pod.yaml
   ```

## Applying the Generated YAML
Once the YAML is generated and optionally modified, you can apply it to the cluster:

```bash
kubectl apply -f nginx-pod.yaml
```

Verify the Pod is created:
```bash
kubectl get pods
```

---

## Additional Tips
- Use `kubectl explain` to understand each field in the YAML file:
  ```bash
  kubectl explain pod.spec.containers
  ```
- Validate your YAML before applying:
  ```bash
  kubectl apply -f nginx-pod.yaml --dry-run=client
  


4. Verify the new Pod is running:
   ```bash
   kubectl get pods
   ```
