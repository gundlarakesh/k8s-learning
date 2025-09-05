# Kubernetes Network Policies Hands-on Scenarios

This document provides **three levels of scenarios (Easy, Medium, Hard)** for practicing Kubernetes Network Policies.  
Use these to gain hands-on experience by writing and testing policies.

---

## 🟢 Easy Level

### Goal
Restrict **pod-to-pod communication** inside a namespace.

### Setup
- Namespace: `dev`
- Pods:
  - `frontend` (label: `app=frontend`)
  - `backend` (label: `app=backend`)
  - `db` (label: `app=db`)

### Requirements
1. By default, all pods can talk to each other.
2. Create a NetworkPolicy so that:
   - `frontend` can only talk to `backend`.
   - `backend` can only talk to `db`.
   - No one else can talk to `db`.

### Test
- `frontend → backend` → should work  
- `frontend → db` → should fail  
- `backend → db` → should work  

---

## 🟡 Medium Level

### Goal
Restrict **ingress and egress** traffic with namespace isolation.

### Setup
- Namespaces:
  - `team-a` (pods: `api`, `db`)
  - `team-b` (pods: `api`, `db`)
- External: Internet access

### Requirements
1. Pods in `team-a` should only communicate with pods in the same namespace.
2. Pods in `team-b` should only communicate with pods in the same namespace.
3. Allow `team-a/api` to access the internet.
4. Block internet access for `team-b`.

### Test
- `team-a/api → google.com` → should work  
- `team-b/api → google.com` → should fail  
- `team-a/api → team-b/api` → should fail  

---

## 🔴 Hard Level

### Goal
Combine **default-deny, port-based restrictions, and namespace selectors**.

### Setup
- Namespace: `payments`
  - `payment-api` (port 8080)
  - `payment-db` (port 5432)
- Namespace: `monitoring`
  - `prometheus` (port 9090)
- Namespace: `default`
  - `frontend`

### Requirements
1. Apply a **default deny-all policy** in `payments`.
2. Allow `frontend` (from `default`) to access `payment-api` on port **8080**.
3. Allow `payment-api` to connect to `payment-db` on port **5432**.
4. Allow `prometheus` (from `monitoring`) to scrape `payment-api` on port **8080`, but not `payment-db`.
5. Block all other cross-namespace communication.

### Test
- `frontend → payment-api:8080` → should work  
- `frontend → payment-db:5432` → should fail  
- `prometheus → payment-api:8080/metrics` → should work  
- `prometheus → payment-db:5432` → should fail  

---

## ⚡ Tips
- Always start with a **default-deny-all** NetworkPolicy before whitelisting traffic.
- Use `kubectl exec` with `curl` or `nc` to test connections.
- Combine pod labels, namespace selectors, and ports for fine-grained rules.
- Debug using:
  - `kubectl describe networkpolicy <name> -n <namespace>`
  - `kubectl get networkpolicy -A`

---

Happy Hacking 🎯
