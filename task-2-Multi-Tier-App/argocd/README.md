
# 🚀 Multi-Tier Cloud-Native Application

**Author:** Peddireddy Abhiram Reddy

**Architecture:** 3-Tier (Frontend, Backend, Database)

**Environment:** Google Kubernetes Engine (GKE)

**Automation:** GitOps via ArgoCD

---

## 🏗 Architecture Overview

This project implements a secure 3-tier application deployment on GKE, utilizing namespace isolation and persistent storage.

<img width="1265" height="681" alt="Screenshot 2026-02-27 at 3 25 53 PM" src="https://github.com/user-attachments/assets/58e40fe0-108f-462e-8930-e9ef72154b1b" />


---

## 🔒 Security & Networking

### Namespace Isolation

The **Backend** and **Database** are isolated in the `database` namespace, while the **Frontend** serves traffic from the `default` namespace. Cross-namespace communication is handled via the internal FQDN:
`http://backend.database.svc.cluster.local:3000`

---

## ✅ Validation Commands

Use these commands to verify that each tier is functioning correctly and that security policies are being enforced.

### 1. Verify Frontend to Backend Connectivity

Ensure the Nginx proxy can reach the API in the other namespace.

```bash
# Get the LoadBalancer External IP
kubectl get svc frontend -n default

# Test the Reverse Proxy endpoint (should return backend data)
curl http://<EXTERNAL-IP>/api/

```

### 2. Verify Backend to Database Connectivity

Check if the Backend pod can successfully query the MySQL instance.

```bash
# Run a curl test from within the database namespace
kubectl run verify-db-conn -it --rm -n database \
  --image=curlimages/curl -- http://backend:3000

```

### 3. Verify Network Policy (Security Test)

Confirm that unauthorized pods are blocked from accessing the database.

```bash
# Attempt to reach MySQL from a generic pod (Expected: Connection Timeout)
kubectl run hacker-pod -it --rm -n database \
  --image=busybox -- nc -zvw 5 mysql 3306

```

### 4. Verify Persistent Storage

Ensure the GCP Filestore volume is correctly mounted and writable.

```bash
# Check PVC and PV status
kubectl get pvc,pv -n database

# Verify mount point inside the MySQL pod
kubectl exec -it <mysql-pod-name> -n database -- df -h | grep /var/lib/mysql

```

---

## ⚙️ GitOps Workflow & ArgoCD

### 1. Accessing the ArgoCD GUI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443

```

Open: **`https://localhost:8080`**

### 2. Retrieving Credentials

* **Username:** `admin`
* **Password:**

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

```

<img width="690" height="114" alt="Screenshot 2026-02-27 at 3 23 15 PM" src="https://github.com/user-attachments/assets/c3760707-f28d-4e2e-b38e-e1341d1d2a4d" />


---

## 🛠 Project Components

| Component | Type | Namespace | Purpose |
| --- | --- | --- | --- |
| `frontend` | LoadBalancer | `default` | External entry point |
| `backend` | ClusterIP | `database` | Internal API logic |
| `mysql` | StatefulSet | `database` | Persistent data storage |
| `network-policy` | NetPol | `database` | Least-privilege traffic control |

---
