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

4. Verify the new Pod is running:
   ```bash
   kubectl get pods
   ```
