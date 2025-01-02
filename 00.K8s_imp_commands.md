# Kubernetes Must-Know Commands

Here is a categorized list of essential Kubernetes commands grouped by their usage. These commands will help you demonstrate your expertise in Kubernetes during interviews.

---

## Cluster Management
- Check cluster information:
  ```bash
  kubectl cluster-info
  ```
- List all nodes in the cluster:
  ```bash
  kubectl get nodes
  ```
- Check the configuration file being used:
  ```bash
  kubectl config view
  ```
- Set the current context:
  ```bash
  kubectl config use-context <context-name>
  ```
- Test cluster accessibility:
  ```bash
  kubectl version --short
  ```

---

## Pod Management
- List all Pods in the current namespace:
  ```bash
  kubectl get pods
  ```
- List Pods across all namespaces:
  ```bash
  kubectl get pods --all-namespaces
  ```
- Describe a specific Pod:
  ```bash
  kubectl describe pod <pod-name>
  ```
- View Pod logs:
  ```bash
  kubectl logs <pod-name>
  ```
- Execute a command inside a running Pod:
  ```bash
  kubectl exec -it <pod-name> -- <command>
  ```

---

## Deployment Management
- List Deployments:
  ```bash
  kubectl get deployments
  ```
- Scale a Deployment:
  ```bash
  kubectl scale deployment <deployment-name> --replicas=<number>
  ```
- Update a Deployment:
  ```bash
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
  ```
- Rollback a Deployment:
  ```bash
  kubectl rollout undo deployment/<deployment-name>
  ```
- View rollout status:
  ```bash
  kubectl rollout status deployment/<deployment-name>
  ```

---

## Service and Networking
- List all Services:
  ```bash
  kubectl get services
  ```
- Describe a Service:
  ```bash
  kubectl describe service <service-name>
  ```
- Port-forward a Service:
  ```bash
  kubectl port-forward service/<service-name> <local-port>:<service-port>
  ```
- List Ingress resources:
  ```bash
  kubectl get ingress
  ```

---

## Namespace Management
- List all namespaces:
  ```bash
  kubectl get namespaces
  ```
- Create a namespace:
  ```bash
  kubectl create namespace <namespace-name>
  ```
- Switch to a namespace:
  ```bash
  kubectl config set-context --current --namespace=<namespace-name>
  ```
- Delete a namespace:
  ```bash
  kubectl delete namespace <namespace-name>
  ```

---

## Persistent Volumes
- List Persistent Volume Claims (PVCs):
  ```bash
  kubectl get pvc
  ```
- Describe a PVC:
  ```bash
  kubectl describe pvc <pvc-name>
  ```
- List Persistent Volumes (PVs):
  ```bash
  kubectl get pv
  ```

---

## Debugging and Troubleshooting
- Debug a Pod:
  ```bash
  kubectl debug pod/<pod-name> --image=<debug-image>
  ```
- Check events in the current namespace:
  ```bash
  kubectl get events
  ```
- Inspect the status of all resources:
  ```bash
  kubectl get all
  ```
- View logs from the Kubernetes API server:
  ```bash
  kubectl logs -n kube-system kube-apiserver-<node-name>
  ```

---

## Resource Management
- Apply a YAML file:
  ```bash
  kubectl apply -f <file-name>.yaml
  ```
- Delete a resource:
  ```bash
  kubectl delete <resource-type> <resource-name>
  ```
- Create a resource from a YAML file:
  ```bash
  kubectl create -f <file-name>.yaml
  ```
- Get detailed YAML of a resource:
  ```bash
  kubectl get <resource-type> <resource-name> -o yaml
  ```

---

## Important Shortcuts
- Get resources with a label:
  ```bash
  kubectl get <resource-type> -l <label-key>=<label-value>
  ```
- Watch resource changes:
  ```bash
  kubectl get <resource-type> --watch
  ```
- Dry-run a command:
  ```bash
  kubectl apply -f <file-name>.yaml --dry-run=client
  
