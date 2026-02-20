
# Backend Service – Kubernetes Deployment

## Overview

This directory contains Kubernetes manifests for deploying the **backend tier** of the multi-tier application as part of **Assignment-2**.
The backend is implemented as a scalable internal service that securely communicates with a MySQL database running as a StatefulSet.

---

## Architecture & Design

The backend layer is designed with the following principles:

* Deployed using a **Kubernetes Deployment** for scalability and self-healing
* Exposed internally using a **ClusterIP Service**
* Communicates with the database using **Kubernetes Service DNS**
* Consumes database credentials using **Kubernetes Secrets**
* Runs in the same namespace as the database to allow secure secret access

This design ensures **service isolation**, **secure configuration management**, and **fault tolerance**.

---

## Kubernetes Resources Used

| Resource            | Purpose                                  |
| ------------------- | ---------------------------------------- |
| Deployment          | Runs backend pods with multiple replicas |
| Service (ClusterIP) | Enables internal access to backend       |
| Secret              | Stores database credentials securely     |
| Namespace           | Logical isolation and secret scoping     |

---

## Database Connectivity

The backend connects to the MySQL database using the internal service DNS:

```
mysql.database.svc.cluster.local:3306
```

This ensures:

* No direct pod-to-pod dependency
* Stable communication even during pod restarts or scaling
* Kubernetes-native service discovery

---

## Validation

### 1️⃣ Backend Pods Running

The backend Deployment was validated by confirming that multiple backend pods are running successfully.

📸 **Output**
`kubectl get pods -n database`

<img width="1178" height="297" alt="Screenshot 2026-02-20 at 4 30 38 PM" src="https://github.com/user-attachments/assets/337c966f-c499-4866-bf02-d5ea8c8b8b95" />


**What this proves:**

* Backend Deployment is created successfully
* Pods are running without crashes
* Replicas are scheduled across nodes

---

### 2️⃣ Backend Service Created

The backend is exposed internally using a ClusterIP Service.

📸 **Output**
`kubectl get svc backend -n database`

<img width="754" height="178" alt="Screenshot 2026-02-20 at 4 27 56 PM" src="https://github.com/user-attachments/assets/991d55e2-975e-4a2c-ba36-ffa9ea886e95" />

**What this proves:**

* Backend is accessible internally within the cluster
* No external exposure of backend service

---

### 3️⃣ Service Endpoints Resolution

The backend Service correctly resolves to backend pod IPs via Kubernetes endpoints.

📸 **Output**
`kubectl get ep backend -n database`

<img width="909" height="167" alt="Screenshot 2026-02-20 at 4 28 38 PM" src="https://github.com/user-attachments/assets/961c0954-69fc-484a-8ee1-31d82a4575d8" />

**What this proves:**

* Service-to-pod mapping is working
* Load balancing is handled by Kubernetes

---

### 4️⃣ Backend → Database Connectivity

Database connectivity was validated from backend pods by verifying MySQL service reachability.

📸 **Output**
`kubectl logs -n database backend-<pod-name>`

<img width="726" height="315" alt="Screenshot 2026-02-20 at 4 29 44 PM" src="https://github.com/user-attachments/assets/e5ea6f92-ee94-4420-857d-18bfc53421b7" />


**What this proves:**

* Backend can successfully reach the database service
* Internal DNS and networking are configured correctly

---

## Security Considerations

* Database credentials are not hardcoded and are injected via **Kubernetes Secrets**
* Backend service is not exposed externally
* Communication between backend and database occurs only within the cluster

---

## Assignment Mapping

This backend implementation satisfies the following Assignment-2 requirements:

* Multi-tier application deployment
* Secure internal service communication
* Kubernetes-native service discovery
* Secret-based configuration management
* High availability through pod replicas

---

## Conclusion

The backend layer is successfully deployed and validated as part of the multi-tier Kubernetes application. It demonstrates correct use of Kubernetes Deployments, Services, Secrets, and namespaces while maintaining secure and reliable communication with the database tier.

> [!NOTE]
> **Namespace Consideration**
>
> The backend service is deployed in the same Kubernetes namespace as the MySQL database. This architecture ensures seamless access to database credentials because:
>
> * **Security:** Kubernetes Secrets are namespace-scoped.
> * **Connectivity:** Internal DNS resolution is simplified for services sharing the same namespace.

---
