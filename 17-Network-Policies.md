# Kubernetes Network Policies Explained

Network Policies in Kubernetes are used to control traffic flow at the IP address or port level, within or between pods, namespaces, and external entities. They define rules about what kind of traffic is allowed or denied.

---

## Prerequisites: Container Network Interface (CNI)
To enforce Network Policies, the cluster must have a CNI plugin that supports them. Examples of CNI plugins include:
- **Calico**
- **Weave Net**
- **Cilium**

### CNI Installation Example: Calico
Calico is a popular CNI plugin that supports Network Policies.

1. **Install Calico on Your Cluster:**
   ```bash
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```

2. **Verify the Installation:**
   Ensure all Calico pods are running.
   ```bash
   kubectl get pods -n kube-system
   ```

---

## Creating Network Policies
A Network Policy is a Kubernetes resource that specifies how pods communicate with each other and external resources.

### Key Components:
- **Pod Selector**: Specifies the targeted pods.
- **Ingress Rules**: Define incoming traffic rules.
- **Egress Rules**: Define outgoing traffic rules.
- **Namespace Selector**: Matches pods in specific namespaces.

### Example Network Policy
#### Use Case: Restrict Ingress Traffic
- Allow ingress traffic only from pods in the same namespace with a specific label.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-ingress
  namespace: my-namespace
spec:
  podSelector:
    matchLabels:
      role: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80
```

### Example Network Policy
#### Use Case: Restrict Egress Traffic
- Allow outgoing traffic only to specific external IPs and ports.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-egress
  namespace: my-namespace
spec:
  podSelector:
    matchLabels:
      role: database
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 192.168.1.0/24
    ports:
    - protocol: TCP
      port: 3306
```

---

## Real-World Usage Example
### Scenario: Securing a Microservices Architecture
**Context:**
- A microservices application has three components: `frontend`, `backend`, and `database`.
- Communication Rules:
  - The `frontend` should only talk to the `backend` on port 8080.
  - The `backend` should only talk to the `database` on port 3306.

**Implementation:**

1. **Frontend to Backend Policy:**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: frontend-to-backend
     namespace: app-namespace
   spec:
     podSelector:
       matchLabels:
         role: backend
     policyTypes:
     - Ingress
     ingress:
     - from:
       - podSelector:
           matchLabels:
             role: frontend
       ports:
       - protocol: TCP
         port: 8080
   ```

2. **Backend to Database Policy:**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: backend-to-database
     namespace: app-namespace
   spec:
     podSelector:
       matchLabels:
         role: database
     policyTypes:
     - Ingress
     ingress:
     - from:
       - podSelector:
           matchLabels:
             role: backend
       ports:
       - protocol: TCP
         port: 3306
   ```

3. **Test the Policies:**
   Deploy the application and validate traffic flows using `curl`, logs, or network tools like `netcat`.

---

## Conclusion
Network Policies are a critical aspect of Kubernetes security. They allow you to enforce fine-grained control over traffic flow within your cluster. Combining them with a compatible CNI plugin ensures a secure and well-regulated environment. By leveraging Network Policies, you can isolate workloads and reduce the attack surface effectively.
