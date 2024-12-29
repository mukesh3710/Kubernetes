# Creating a KIND Multi-Node Cluster on macOS

This guide walks you through creating a Kubernetes multi-node cluster using KIND (Kubernetes IN Docker) on macOS.

## Prerequisites
1. Install Docker Desktop and ensure it is running.
2. Install KIND (Kubernetes IN Docker):
   ```bash
   brew install kind
   ```
3. Verify KIND installation:
   ```bash
   kind version
   ```

## Steps to Create a KIND Multi-Node Cluster

### 1. Define the Cluster Configuration
Create a YAML configuration file named `kind-cluster.yaml` with the following content:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
  - role: worker
kubeadmConfigPatches:
  - |
    kind: ClusterConfiguration
    apiVersion: kubeadm.k8s.io/v1beta3
    kubernetesVersion: "v1.30.0"
```

### 2. Create the Cluster
Run the following command to create the cluster:
```bash
kind create cluster --config kind-cluster.yaml --name kind-cka
```

### 3. Verify the Cluster
- Check the cluster context:
  ```bash
  kubectl config current-context
  ```
- Ensure all nodes are ready:
  ```bash
  kubectl get nodes
  ```

### 4. Verify Nodes as Containers
As KIND uses Docker to create nodes, verify the containers:
```bash
docker ps
```

You should see containers representing the control-plane and worker nodes.

## Installing kubectl Client
1. Install kubectl:
   ```bash
   brew install kubectl
   ```
2. Verify installation:
   ```bash
   kubectl version --client
   ```

## Setting the Current Context
The current context is set automatically when the cluster is created. To manually set it:
```bash
kubectl config use-context kind-kind
```

## Important KIND Commands
Here are some useful KIND commands for managing your cluster:

- **Create a Cluster**:
  ```bash
  kind create cluster --name <cluster-name>
  ```
- **Delete a Cluster**:
  ```bash
  kind delete cluster --name <cluster-name>
  ```
- **List Clusters**:
  ```bash
  kind get clusters
  ```
- **Export Kubeconfig**:
  ```bash
  kind export kubeconfig --name <cluster-name>
  ```
- **Load Docker Image to Cluster**:
  ```bash
  kind load docker-image <image-name> --name <cluster-name>
  ```

## Summary
By following the steps above, you will:
- Create a KIND multi-node cluster with 1 control plane and 3 worker nodes.
- Verify that the nodes are running and are represented as Docker containers.
- Manage the cluster using kubectl and KIND commands.

This setup is perfect for local development and testing Kubernetes deployments.
