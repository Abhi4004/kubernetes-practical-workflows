
---

# Database Layer – MySQL (StatefulSet)

## Overview

This directory contains Kubernetes manifests for deploying the **database tier** of the multi-tier e-commerce application as part of **Assignment-2**.

The database is implemented using **MySQL running as a StatefulSet** to ensure:

* Stable network identity
* Persistent storage
* Data durability across pod restarts

---

## Architecture Choice

### Why StatefulSet for MySQL?

The database layer requires:

* Persistent data storage
* Stable pod identity
* Ordered startup and shutdown

A **StatefulSet** is the recommended Kubernetes workload for stateful applications like databases.

---

## Components Deployed

### 1. Namespace

All database-related resources are isolated in a dedicated namespace:

* **Namespace:** `database`

---

### 2. Secrets

Sensitive database credentials are stored securely using Kubernetes Secrets.

Stored values:

* Root password
* Application database name
* Application user credentials

---

### 3. Headless Service

A **headless service** is used to provide stable DNS resolution for the MySQL pod.

* Service name: `mysql`
* Port: `3306`
* Cluster-internal access only

This enables predictable hostname resolution (`mysql-0.mysql.database.svc.cluster.local`).

---

### 4. MySQL StatefulSet

The MySQL database runs as a StatefulSet with:

* One replica (`mysql-0`)
* Persistent volume mounted at `/var/lib/mysql`
* Environment variables sourced from Kubernetes Secrets

---

### 5. Persistent Storage

Persistent storage is provisioned using:

* **PersistentVolumeClaim (PVC)** created via `volumeClaimTemplates`
* Default GKE StorageClass
* Storage size: **10Gi**

This ensures database data persists even if the MySQL pod is restarted or rescheduled.

---

## Deployment Manifests

| File Name                | Description                               |
| ------------------------ | ----------------------------------------- |
| `mysql-namespace.yaml`   | Creates the `database` namespace          |
| `mysql-secret.yaml`      | Stores MySQL credentials                  |
| `mysql-service.yaml`     | Headless service for MySQL                |
| `mysql-statefulset.yaml` | MySQL StatefulSet with persistent storage |

---

## Deployment Steps

Apply the manifests in the following order:

```bash
kubectl apply -f mysql-namespace.yaml
kubectl apply -f mysql-secret.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f mysql-statefulset.yaml
```

---

## Validation

Verify the database deployment using:

```bash
kubectl get pods -n database
kubectl get svc -n database
kubectl get pvc -n database
```

Expected results:

* MySQL pod (`mysql-0`) is in **Running** state
* PVC (`mysql-data-mysql-0`) is **Bound**
* MySQL service is **ClusterIP (headless)**

---

## Assignment Mapping

This database implementation satisfies the following Assignment-2 requirements:

* ✅ Stateful database deployment
* ✅ Persistent storage using PV and PVC
* ✅ Secure internal-only database access
* ✅ Kubernetes-native database management
* ✅ Git-based version-controlled manifests

---

## Next Steps

* Deploy **Backend (Node.js API)** to connect to MySQL
* Configure **Network Policies** for secure backend-to-database communication
* Integrate deployment with **ArgoCD** for GitOps-based synchronization

---

