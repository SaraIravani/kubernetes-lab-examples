
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

### **🎯 Purpose:**
- Allows unrestricted internal communication within the `frontend` namespace.
- Ideal for microservices that need **free communication** within the same namespace.

---

## 🔗 **Scenario 2: Cross-Namespace Communication**

### **📝 Use Case:**

Enable controlled communication between namespaces where:
- `frontend` needs access to `backend`.
- `backend` needs access to `monitoring`.

### **📄 Policy 1: `frontend` → `backend`**

```yaml
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

### **📄 Policy 2: `backend` → `monitoring`**

```yaml
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

### **🎯 Purpose:**
- Enforces namespace-level **security boundaries** while allowing necessary communication paths.

---

## 🌐 **Scenario 3: Cross-Namespace Access with FQDN**

### **📝 Use Case:**

Allow communication between namespaces using **FQDN** (Fully Qualified Domain Name).

### **📄 NetworkPolicy:**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend-fqdn
  namespace: backend
spec:
  podSelector: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
```

### **🌎 Access Example:**

Access a service called `orders` in namespace `backend`:
```
http://orders.backend.svc.cluster.local
```

### **🧪 Testing the Policy:**

```bash
kubectl run tmp-shell --rm -i --tty --image busybox -n frontend -- /bin/sh
wget -qO- http://orders.backend.svc.cluster.local:8080
```

---

## 🚫 **Scenario 4: Denying All External Traffic (Default Deny)**

### **📝 Use Case:**

Secure a namespace by **blocking all incoming traffic** unless explicitly allowed.

### **📄 NetworkPolicy:**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-traffic
  namespace: backend
spec:
  podSelector: {}
  ingress: []
```

### **🎯 Purpose:**
- Provides a **zero-trust** security model for sensitive namespaces.
- Blocks all ingress traffic unless explicitly allowed through additional policies.

---

## 🔒 **Scenario 5: Allowing Egress Traffic to External Services**

### **📝 Use Case:**

Allow Pods to access **external APIs** or **internet resources**, such as payment gateways or cloud storage.

### **📄 NetworkPolicy:**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-egress-to-external
  namespace: backend
spec:
  podSelector: {}
  egress:
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
  policyTypes:
  - Egress
```

### **🎯 Purpose:**
- Enables **secure egress traffic** while maintaining strict ingress control.
- Useful for applications interacting with **external APIs** or cloud services.

---

## 📋 **How to Apply Policies**

1. **Clone this Repository:**
   ```bash
   git clone https://github.com/<your-github-username>/kubernetes-lab-examples.git
   cd kubernetes-lab-examples
   ```

2. **Apply a Specific Policy:**
   ```bash
   kubectl apply -f policies/allow-frontend-to-backend.yaml
   ```

3. **Verify Applied Policies:**
   ```bash
   kubectl get networkpolicies -n <namespace>
   ```

4. **Test Communication:**
   ```bash
   kubectl exec -it <pod-name> -n frontend -- curl http://orders.backend.svc.cluster.local:8080
   ```

---

## 🤝 **Contributing**

🔥 Contributions are welcome!
- Add new scenarios
- Improve examples
- Report issues

**Submit a pull request** and be part of the community!

---

## 📜 **License**

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---
