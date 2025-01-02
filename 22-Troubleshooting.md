# Kubernetes Troubleshooting Guide

This document provides step-by-step approaches to troubleshoot application failures, cluster component issues, and network problems in a production Kubernetes cluster.

---

## 1. Troubleshooting Application Failures

### Step 1: Verify Pod Status
- Check the status of the application pods:
  ```bash
  kubectl get pods -n <namespace>
  ```
- Look for `CrashLoopBackOff`, `Pending`, or `Error` states.

### Step 2: Describe the Pod
- Get detailed information about the problematic pod:
  ```bash
  kubectl describe pod <pod-name> -n <namespace>
  ```
- Look for events like image pull errors, scheduling issues, or resource limits.

### Step 3: Check Pod Logs
- View the logs of the container in the pod:
  ```bash
  kubectl logs <pod-name> -n <namespace> --container <container-name>
  ```
- For multiple containers:
  ```bash
  kubectl logs <pod-name> -n <namespace> --all-containers=true
  ```

### Step 4: Debug Using a Shell
- If logs are insufficient, open a shell in the container:
  ```bash
  kubectl exec -it <pod-name> -n <namespace> -- /bin/sh
  ```

### Step 5: Check Deployment Events
- View events related to the deployment or replica set:
  ```bash
  kubectl describe deployment <deployment-name> -n <namespace>
  ```

### Step 6: Resource Utilization
- Verify CPU and memory usage:
  ```bash
  kubectl top pods -n <namespace>
  ```

---

## 2. Troubleshooting Cluster Component Failures

### Step 1: Verify Cluster Component Status
- Check the status of control plane components:
  ```bash
  kubectl get componentstatuses
  ```

### Step 2: Analyze System Logs
- Review logs for control plane components (API server, etcd, scheduler, and controller-manager):
  ```bash
  sudo journalctl -u kube-apiserver
  sudo journalctl -u etcd
  sudo journalctl -u kube-scheduler
  sudo journalctl -u kube-controller-manager
  ```

### Step 3: Check Node Status
- Ensure all nodes are ready:
  ```bash
  kubectl get nodes
  ```
- Troubleshoot problematic nodes:
  ```bash
  kubectl describe node <node-name>
  sudo journalctl -u kubelet
  sudo journalctl -u kube-proxy
  ```

### Step 4: Inspect Etcd Health
- Verify etcd cluster health:
  ```bash
  ETCDCTL_API=3 etcdctl endpoint health \
    --endpoints=https://127.0.0.1:2379 \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --key=/etc/kubernetes/pki/etcd/server.key
  ```

### Step 5: Validate API Server Connectivity
- Check connectivity to the API server:
  ```bash
  kubectl cluster-info
  curl -k https://<api-server>:6443/healthz
  ```

---

## 3. Network Troubleshooting

### Step 1: Verify Pod Connectivity
- Test connectivity between pods:
  ```bash
  kubectl exec -it <pod-name> -n <namespace> -- ping <target-pod-IP>
  ```

### Step 2: Check Network Policies
- Review network policies applied to the pods:
  ```bash
  kubectl get networkpolicy -n <namespace>
  kubectl describe networkpolicy <policy-name> -n <namespace>
  ```

### Step 3: Verify Service Configuration
- Check if the service is exposing the application correctly:
  ```bash
  kubectl get svc -n <namespace>
  kubectl describe svc <service-name> -n <namespace>
  ```

### Step 4: Inspect DNS
- Test DNS resolution from within a pod:
  ```bash
  kubectl exec -it <pod-name> -n <namespace> -- nslookup <service-name>
  ```

### Step 5: Validate Ingress Configuration
- Check the ingress rules and status:
  ```bash
  kubectl get ingress -n <namespace>
  kubectl describe ingress <ingress-name> -n <namespace>
  ```

### Step 6: Analyze CNI Plugin Logs
- Check logs for the CNI plugin (e.g., Calico, Flannel):
  ```bash
  sudo journalctl -u kubelet
  ```

---

## Real-World Example
### Scenario: Application Cannot Be Accessed Externally
1. **Verify Service Status**:
   ```bash
   kubectl get svc -n production
   ```
   Ensure the service is of type `LoadBalancer` or `NodePort`.

2. **Check Ingress Logs**:
   ```bash
   kubectl logs <ingress-controller-pod> -n ingress-nginx
   ```

3. **Inspect Network Policies**:
   ```bash
   kubectl get networkpolicy -n production
   ```
   Allow traffic to the application pods if restricted.

4. **Validate DNS Resolution**:
   ```bash
   kubectl exec -it <pod-name> -n production -- nslookup <service-name>
   ```

By following these steps, you can systematically troubleshoot application, cluster, and network issues in a production Kubernetes environment.
