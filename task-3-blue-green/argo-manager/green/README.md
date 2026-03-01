---

# 🏗️ Task 3: Blue-Green Deployment Architecture

This repository implements a **Blue-Green Deployment** strategy for a multi-tier application. It allows for zero-downtime updates and instant rollbacks by running two identical environments (Blue/v1 and Green/v2) side-by-side.

## 🗺️ Architecture Overview

The system is split into two independent "stacks." While they share the same cluster, they are isolated by unique resource naming and Kubernetes labels.

| Feature | 🔵 Blue Environment (v1) | 🟢 Green Environment (v2) |
| --- | --- | --- |
| **Status** | Stable / Production | New / Testing |
| **Labels** | `version: v1` | `version: v2` |
| **Suffix** | `-blue` | `-green` |
| **ArgoCD App** | `multi-tier-app-blue` | `multi-tier-app-green` |

---

## 📂 Environment Breakdown

### 1. Naming & Isolation

To prevent naming collisions, every resource in the `green/` folder is a clone of the `blue/` folder but with the `-green` suffix.

* **Services:** `frontend-green`, `backend-green`, `mysql-green`
* **Storage:** `mysql-pvc-green` (Ensures v2 testing doesn't touch v1 production data).
* **Secrets:** `mysql-secret-green`

### 2. Internal Networking (Service Discovery)

The apps are configured via environment variables to stay within their own "color" boundaries:

* **Green Backend:** Points to `DB_HOST: mysql-green.database.svc.cluster.local`.
* **Green Frontend:** Points to `BACKEND_URL: http://backend-green`.

---

## 🚦 Traffic Control (The "Master Switch")

The **Traffic Control** layer lives in the `traffic-control/` directory. It consists of a single `LoadBalancer` Service that acts as the entry point for all end-users.

### How to Perform a Switch:

1. **Test Green:** Access the application via the `frontend-green` Service IP.
2. **Flip the Switch:** Update the `selector` in `traffic-control/prod-service.yaml`:
```yaml
selector:
  app: frontend
  version: v2 # Change from v1 to v2

```


3. **Sync:** Push to Git. ArgoCD will update the Production Service, and traffic will immediately flow to the Green pods.

---

## 🛠️ Operations & Troubleshooting

### Viewing Parallel Resources

To see both versions of your application running at once:

```bash
kubectl get pods,svc,pvc -n default

```

### Checking Environment Health

To verify that the Green environment is correctly isolated and connecting to its own database:

```bash
kubectl logs deployment/backend-green -n database

```

*Expected output: `Backend: Connected to MySQL` (connecting to mysql-green).*

### Rollback Procedure

If the Green environment shows errors after the switch:

1. Revert the `version` label in `prod-service.yaml` back to `v1`.
2. Push to Git and Sync.
3. Traffic returns to the stable Blue environment in milliseconds.

---
