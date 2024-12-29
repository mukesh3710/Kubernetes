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
