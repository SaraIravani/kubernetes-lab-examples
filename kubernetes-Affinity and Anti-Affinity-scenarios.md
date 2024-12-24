
# Kubernetes Affinity and Anti-Affinity Examples (Advanced)

This repository demonstrates **advanced Kubernetes scheduling strategies** using **Pod Affinity**, **Anti-Affinity**, **Node Affinity**, and **Topology Spread Constraints** for Kubernetes **v1.29+**.  
It covers **real-world scenarios** for **distributed systems**, **fault tolerance**, and **optimized performance**.

---

## Table of Contents
- [Overview](#overview)
- [Use Cases](#use-cases)
- [Prerequisites](#prerequisites)
- [Examples](#examples)
  - [Affinity with Topology Key](#affinity-with-topology-key)
  - [Weighted Preferences for Node Affinity](#weighted-preferences-for-node-affinity)
  - [Pod Anti-Affinity with Fault Domains](#pod-anti-affinity-with-fault-domains)
  - [Advanced Example: Multi-Zone Scheduling](#advanced-example-multi-zone-scheduling)
  - [Topology Spread Constraints](#topology-spread-constraints)
- [Deployment](#deployment)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This guide explores **advanced scheduling configurations** in Kubernetes **v1.29+**, including:

- **Node Affinity**: Prefer or require specific node labels for resource constraints.  
- **Pod Affinity/Anti-Affinity**: Co-locate or separate pods based on labels and rules.  
- **Topology Key**: Optimize workloads across failure domains (zones, regions).  
- **Weights**: Prioritize scheduling rules without strict enforcement.  
- **Spread Constraints**: Ensure even distribution across topologies (e.g., zones).  

---

## Use Cases

1. **Optimize Performance**: Place latency-sensitive pods closer together.  
2. **Ensure Fault Tolerance**: Spread replicas across failure domains (zones).  
3. **Enforce GPU Nodes**: Schedule AI/ML workloads on GPU-enabled nodes.  
4. **Distribute Traffic**: Balance workloads across availability zones.  

---

## Examples

### **1. Affinity with Topology Key**
**Scenario**: Ensure pods with `app=frontend` are scheduled in the **same failure domain** as pods with `app=backend`.  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - backend
        topologyKey: failure-domain.beta.kubernetes.io/zone
  containers:
  - name: frontend
    image: nginx
```
**Explanation**:  
- **topologyKey** groups pods within the **same zone** for low-latency communication.  
- Ensures **frontend** pods run **close** to **backend** pods for **high performance**.  

---

### **2. Weighted Preferences for Node Affinity**
**Scenario**: Prefer nodes in `us-west1` region but allow fallback to other regions.  
```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 90
      preference:
        matchExpressions:
        - key: region
          operator: In
          values:
          - us-west1
```
**Explanation**:  
- A **weight of 90** prioritizes `us-west1` nodes without strict enforcement.  
- Useful for **multi-region deployments** where latency matters.  

---

### **3. Pod Anti-Affinity with Fault Domains**
**Scenario**: Avoid placing multiple replicas of the same app in the **same zone** to improve fault tolerance.  
```yaml
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
        - key: app
          operator: In
          values:
          - web
      topologyKey: failure-domain.beta.kubernetes.io/zone
```
**Explanation**:  
- Pods labeled `web` are forced into **different zones**, improving **availability** during zone failures.  

---

### **4. Advanced Example: Multi-Zone Scheduling**
**Scenario**: Co-locate a `frontend` pod with a `cache` pod but **avoid** placing them near `db` pods for **isolation**.  
```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
        - key: app
          operator: In
          values:
          - cache
      topologyKey: failure-domain.beta.kubernetes.io/zone
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 80
      podAffinityTerm:
        labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - db
        topologyKey: failure-domain.beta.kubernetes.io/zone
```
**Explanation**:  
- Ensures **`frontend`** and **`cache`** pods are **co-located** for **low latency**.  
- Avoids placing **`frontend`** near **`db`** pods for **security** and **performance isolation**.  

---

### **5. Topology Spread Constraints**
**Scenario**: Spread `nginx` pods evenly across **zones** for load balancing and resilience.  
```yaml
topologySpreadConstraints:
- maxSkew: 1
  topologyKey: failure-domain.beta.kubernetes.io/zone
  whenUnsatisfiable: DoNotSchedule
  labelSelector:
    matchLabels:
      app: nginx
```
**Explanation**:  
- Ensures pods are **distributed evenly** across zones.  
- **`maxSkew: 1`** keeps the **imbalance** between zones **minimal**.  
- Prevents scheduling (`DoNotSchedule`) if constraints are **unsatisfiable**.  

---

## Deployment Instructions

### Using kubectl:
```bash
kubectl apply -f examples/frontend.yaml
kubectl apply -f examples/backend.yaml
kubectl apply -f examples/anti-affinity.yaml
```

---

## Testing

### Check Node Labels:
```bash
kubectl get nodes --show-labels
```

### Describe Pods:
```bash
kubectl describe pod <pod-name>
```

### View Scheduling Decisions:
```bash
kubectl get events --namespace=default
```

---

## Contributing

We welcome contributions! Follow the steps below:  
1. Fork the repository.  
2. Create a feature branch (`git checkout -b feature/my-feature`).  
3. Commit your changes (`git commit -m 'Add feature'`).  
4. Push to the branch (`git push origin feature/my-feature`).  
5. Submit a Pull Request.  

---

## License

Licensed under the **MIT License**.  

---

## Author

**Sara Iravani**  
[LinkedIn Profile](https://linkedin.com/in/sarairavani)  

