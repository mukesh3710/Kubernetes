# Kubernetes Authorization Explained

Authorization in Kubernetes determines what actions a user or service account can perform within a cluster. This document covers the distinction between authorization and authentication, various authorization types in Kubernetes, and how they are used in real-world projects with examples.

---

## Authorization vs. Authentication

- **Authentication**: Focuses on verifying the identity of a user or system. It answers the question: *Who are you?*
  - Examples: Username/password, tokens, certificates.

- **Authorization**: Determines what actions the authenticated user or system is allowed to perform. It answers the question: *What can you do?*
  - Examples: Permissions to create, update, delete resources in Kubernetes.

---

## Authorization Types in Kubernetes
Kubernetes provides several mechanisms for authorizing requests:

### 1. **Attribute-Based Access Control (ABAC)**
- Permissions are defined in policies based on attributes such as user, resource, action, and environment.
- Policies are stored as JSON files and loaded by the API server.
- **Example ABAC Policy:**
  ```json
  {
    "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
    "kind": "Policy",
    "spec": {
      "user": "jane",
      "namespace": "dev",
      "resource": "pods",
      "readonly": true
    }
  }
  ```
- **Usage**: ABAC is suitable for simple setups but is less flexible for dynamic environments.

### 2. **Role-Based Access Control (RBAC)**
- Permissions are granted through roles and role bindings.
- **Key Concepts:**
  - **Roles**: Define permissions within a namespace.
  - **ClusterRoles**: Define permissions across the entire cluster.
  - **RoleBindings**: Bind users/groups to roles within a namespace.
  - **ClusterRoleBindings**: Bind users/groups to ClusterRoles cluster-wide.

- **Example RBAC Configuration:**
  ```yaml
  apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    namespace: dev
    name: pod-reader
  rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["get", "list"]
  ---
  apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: read-pods-binding
    namespace: dev
  subjects:
    - kind: User
      name: jane
      apiGroup: ""
  roleRef:
    kind: Role
    name: pod-reader
    apiGroup: "rbac.authorization.k8s.io"
  ```
- **Usage**: RBAC is the most common method for managing permissions in Kubernetes.

### 3. **Node Authorization**
- Specific to kubelets; controls access for node agents.
- **Example**: Allows a node to read secrets only for the pods scheduled on it.

### 4. **Webhook Authorization**
- Delegates authorization decisions to an external service via a webhook.
- **Example Configuration:**
  ```yaml
  apiVersion: apiserver.k8s.io/v1
  kind: WebhookConfiguration
  metadata:
    name: webhook-config
  webhooks:
    - name: "authz.example.com"
      clientConfig:
        service:
          name: webhook-service
          namespace: default
        caBundle: <base64-encoded-ca-cert>
      rules:
        - apiGroups: ["*"]
          resources: ["*"]
          verbs: ["*"]
  ```
- **Usage**: Useful for integrating with custom access control systems.

### 5. **Kubeconfig**
- Defines user credentials and access to a Kubernetes cluster.
- **Example Kubeconfig File:**
  ```yaml
  apiVersion: v1
  kind: Config
  clusters:
  - name: my-cluster
    cluster:
      server: https://my-cluster-api-server:6443
      certificate-authority: /path/to/ca.crt
  users:
  - name: jane
    user:
      client-certificate: /path/to/jane.crt
      client-key: /path/to/jane.key
  contexts:
  - name: dev-context
    context:
      cluster: my-cluster
      namespace: dev
      user: jane
  current-context: dev-context
  ```
- **Usage**: Configures access for CLI tools (e.g., `kubectl`).

---

## Real-World Usage Example
### Scenario: Multi-Team Access Management
**Context:**
- A company has two teams: `dev` and `ops`.
- The `dev` team requires read/write access to their namespace.
- The `ops` team needs cluster-wide read access.

**Implementation:**
1. **RBAC Role and RoleBindings for `dev` team:**
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: Role
   metadata:
     namespace: dev
     name: dev-team-role
   rules:
     - apiGroups: [""]
       resources: ["pods"]
       verbs: ["create", "update", "delete", "get", "list"]
   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     name: dev-team-binding
     namespace: dev
   subjects:
     - kind: Group
       name: dev-team
       apiGroup: ""
   roleRef:
     kind: Role
     name: dev-team-role
     apiGroup: "rbac.authorization.k8s.io"
   ```

2. **ClusterRole and ClusterRoleBindings for `ops` team:**
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     name: ops-team-role
   rules:
     - apiGroups: [""]
       resources: ["*"]
       verbs: ["get", "list"]
   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: ops-team-binding
   subjects:
     - kind: Group
       name: ops-team
       apiGroup: ""
   roleRef:
     kind: ClusterRole
     name: ops-team-role
     apiGroup: "rbac.authorization.k8s.io"
   ```

---

## Conclusion
Understanding Kubernetes authorization mechanisms is essential for securing your cluster. RBAC is the most versatile and widely used approach, while ABAC, Node, and Webhook authorization are useful in specific contexts. Using Kubeconfig ensures secure and user-friendly access to clusters. By combining these methods effectively, you can manage permissions in complex environments.
