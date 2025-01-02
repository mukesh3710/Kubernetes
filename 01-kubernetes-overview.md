# Kubernetes Overview

Kubernetes, often referred to as K8s, is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF).

## Key Components of Kubernetes

### 1. **Master Node (Control Plane)**
   The master node manages the Kubernetes cluster and orchestrates all container deployments.

   - **API Server**: Serves as the frontend for the Kubernetes control plane. All administrative commands interact with the cluster through the API server.
   - **Scheduler**: Assigns workloads to worker nodes based on resource availability and constraints.
   - **Controller Manager**: Ensures the desired state of the cluster by managing controllers (e.g., Node Controller, Replication Controller).
   - **etcd**: A distributed key-value store that stores cluster configuration and state.

### 2. **Worker Nodes**
   Worker nodes host and run the containerized applications.

   - **Kubelet**: An agent running on each node to ensure containers are running as expected.
   - **Kube-Proxy**: Manages network rules and communication within the cluster.
   - **Container Runtime**: The software responsible for running containers (e.g., Docker, containerd).

### 3. **Pods**
   - The smallest deployable unit in Kubernetes, representing one or more containers sharing the same network namespace and storage.

### 4. **Services**
   - An abstraction that defines a logical set of Pods and provides a stable endpoint for accessing them.

### 5. **ConfigMaps and Secrets**
   - ConfigMaps: Used for managing configuration data.
   - Secrets: Used for storing sensitive information like passwords or API keys.

### 6. **Ingress**
   - Manages external HTTP and HTTPS access to services within the cluster.

---

## Kubernetes Architecture

Kubernetes architecture is designed to manage large-scale containerized applications efficiently. It consists of the following components:

### Control Plane Components

1. **API Server**:
   - Acts as the gateway to the Kubernetes cluster.
   - All kubectl commands and client requests are handled here.

2. **etcd**:
   - A distributed key-value store that stores cluster state and configuration data.

3. **Scheduler**:
   - Assigns workloads (Pods) to worker nodes based on resource availability and policies.

4. **Controller Manager**:
   - Ensures the cluster is in the desired state by managing controllers, such as ReplicaSets and Deployments.

5. **Cloud Controller Manager** (optional):
   - Manages cloud-specific resources, like load balancers or storage.

### End-to-End Flow: What Happens When You Run a kubectl Command?

1. **kubectl command execution**:
   - The user runs a command (e.g., `kubectl apply -f deployment.yaml`).

2. **API Server Interaction**:
   - kubectl sends the request to the API Server.
   - The API Server validates the request and updates the cluster state in etcd.

3. **Scheduler Action**:
   - The Scheduler identifies the optimal worker node to run the requested Pod based on available resources.

4. **Kubelet Execution**:
   - The Kubelet on the chosen worker node pulls the container image and starts the Pod.

5. **Networking and Proxying**:
   - Kube-Proxy sets up the necessary networking rules to enable communication.

6. **Monitoring**:
   - The control plane continuously monitors the application to ensure it matches the desired state.

---

## Pods and Containers

### Pod
- A Pod is the smallest deployable unit in Kubernetes.
- It can contain one or more tightly coupled containers.
- Containers within a Pod share the same network namespace, IP address, and storage volumes.
- Use cases:
  - Running a single container (most common scenario).
  - Running sidecar containers (e.g., logging or proxy containers).

### Container
- A container is a lightweight, standalone, and executable package of software.
- It includes the application code and its dependencies.
- Containers in Kubernetes are managed and orchestrated within Pods.

---

## Challenges of Using Standalone Containers

1. **Manual Scaling**:
   - Adding or removing containers requires manual intervention.

2. **High Availability**:
   - Ensuring containers are always running and restarting failed ones is cumbersome.

3. **Networking Complexity**:
   - Establishing communication between containers and ensuring secure access is challenging.

4. **Load Balancing**:
   - Distributing traffic across multiple containers is not straightforward.

5. **Storage Management**:
   - Managing persistent storage and ensuring data consistency is difficult.

6. **Configuration Management**:
   - Sharing configuration and secrets securely among containers is problematic.

7. **Monitoring and Logging**:
   - Lack of integrated tools for monitoring and logging across multiple containers.

---

## How Kubernetes Solves These Challenges

1. **Automated Scaling**:
   - Kubernetes automatically scales Pods up or down based on resource usage or custom metrics.

2. **Self-Healing**:
   - Detects and replaces failed containers automatically.

3. **Simplified Networking**:
   - Provides a flat network namespace and Service abstraction for seamless communication.

4. **Built-In Load Balancing**:
   - Distributes traffic across Pods using Services and Ingress.

5. **Persistent Storage**:
   - Supports dynamic provisioning of storage using Persistent Volumes (PVs) and Persistent Volume Claims (PVCs).

6. **Configuration and Secrets Management**:
   - Centralized management of ConfigMaps and Secrets with controlled access.

7. **Integrated Monitoring and Logging**:
   - Extensible to support tools like Prometheus and Fluentd for observability.

---

## When to Use Kubernetes

1. **Microservices Architecture**:
   - Ideal for managing a large number of loosely coupled microservices.

2. **Scalable Applications**:
   - Applications requiring automated horizontal and vertical scaling.

3. **CI/CD Pipelines**:
   - Facilitates rolling updates, canary deployments, and blue-green deployments.

4. **Hybrid or Multi-Cloud Deployments**:
   - Suitable for workloads spanning multiple cloud providers or on-premises environments.

5. **Applications with High Availability Requirements**:
   - Ensures redundancy and failover with self-healing and replication features.

---

## When Not to Use Kubernetes

1. **Simple or Small Applications**:
   - For straightforward use cases, Kubernetes adds unnecessary complexity.

2. **Limited Resources**:
   - Kubernetes requires significant computational and operational overhead.

3. **Static Workloads**:
   - Applications that don’t require scaling or frequent updates may not benefit from Kubernetes.

4. **Tight Deadlines**:
   - Kubernetes has a steep learning curve, which might slow down projects with time constraints.

5. **Single Node Applications**:
   - If the application runs efficiently on a single server, Kubernetes may be overkill.

---

By understanding the strengths and limitations of Kubernetes, you can make informed decisions about when to use it for managing your containerized workloads.

---

# apiVersion:
This field specifies the version of the Kubernetes API used for the resource being defined.   
It's crucial for ensuring compatibility between your YAML file and the Kubernetes cluster. 

# kind:
This field defines the type of Kubernetes resource you are creating.

---

# Finding apiVersion and kind for specific resources

- Kubernetes Documentation: The official Kubernetes documentation provides comprehensive information on all available Kubernetes resources, including their apiVersion and kind. Refer to the documentation for the specific resource you want to create (e.g., Deployments, Services, Pods).

- Use kubectl api-resources - Below to find the API version for "Deployment
  ```bash
  kubectl api-resources | grep Deployment
  ```

- kubectl explain command: This command provides information about a specific Kubernetes resource, including its API version and kind.
  ```bash
  kubectl explain deployments
  ```
  This command will display detailed information about the Deployment resource, including its API version (apps/v1), kind (Deployment), and other relevant fields.

- Finding information from resource
  ```bash
  kubectl get <resource> -o yaml
  ```
  This command retrieves an existing resource from the cluster and displays its YAML representation. This can be helpful in understanding the structure and available fields for a particular resource type.

---
# Kubernetes API Versions: Common Examples

This document provides a list of common Kubernetes API versions for various resource types.

| Resource | API Version |
|---|---|
| Pod | v1 |
| Service | v1 |
| Deployment | apps/v1 |
| ReplicaSet | apps/v1 |
| StatefulSet | apps/v1 |
| ConfigMap | v1 |
| Secret | v1 |
| Namespace | v1 |
| Job | batch/v1 |
| CronJob | batch/v1beta1 |
| DaemonSet | apps/v1 |
| Ingress | networking.k8s.io/v1 |
| ServiceAccount | v1 |

**Important Notes:**

* These are common examples, and the specific `apiVersion` might vary depending on the Kubernetes version and any custom resources you are using.
* Always refer to the official Kubernetes documentation for the most up-to-date and accurate information on API versions and resource definitions.

