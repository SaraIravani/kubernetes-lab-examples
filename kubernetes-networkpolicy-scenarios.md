# 🚀 Kubernetes NetworkPolicy Scenarios  

![Kubernetes NetworkPolicy](https://img.shields.io/badge/Kubernetes-NetworkPolicy-blue?style=for-the-badge&logo=kubernetes)  

Welcome to the **Kubernetes NetworkPolicy Scenarios** repository! 🌐  

This repository demonstrates **real-world use cases** of Kubernetes **NetworkPolicies** for managing network access between Pods across namespaces. Whether you're a beginner or an experienced DevOps engineer, these examples will help you **secure** your Kubernetes workloads effectively.  

---

## 📖 **Table of Contents**  

1. [🌍 Scenario 1: Internal Communication within Namespace](#-scenario-1-internal-communication-within-namespace)  
2. [🔗 Scenario 2: Cross-Namespace Communication](#-scenario-2-cross-namespace-communication)  
3. [🌐 Scenario 3: Cross-Namespace Access with FQDN](#-scenario-3-cross-namespace-access-with-fqdn)  
4. [🚫 Scenario 4: Denying All External Traffic (Default Deny)](#-scenario-4-denying-all-external-traffic-default-deny)  
5. [🔒 Scenario 5: Allowing Egress Traffic to External Services](#-scenario-5-allowing-egress-traffic-to-external-services)  

---

## 🌍 **Scenario 1: Internal Communication within Namespace**  

### **📝 Use Case:**  

Allow all Pods **within the same namespace** to communicate with each other.  

### **📄 NetworkPolicy:**  

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-same-namespace
  namespace: frontend
spec:
  podSelector: {}
  ingress:
  - from:
    - podSelector: {}
```
🎯 Purpose:

    Allows unrestricted internal communication within the frontend namespace.
    Ideal for microservices that need free communication within the same namespace.
## 🔗 **Scenario 2: Cross-Namespace Communication**
### **📝 Use Case:**

Enable controlled communication between namespaces where:

    . frontend needs access to backend.
    . backend needs access to monitoring.  
📄 Policy 1: frontend → backend    
```
 apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: backend
spec:
  podSelector: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
```
📄 Policy 2: backend → monitoring
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-backend-to-monitoring
  namespace: monitoring
spec:
  podSelector: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: backend
```

   
