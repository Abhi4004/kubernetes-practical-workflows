# Frontend Service – Kubernetes Deployment

## Overview

This directory contains the Kubernetes manifests for the **frontend tier** of the multi-tier application. As part of **Assignment-2**, the frontend is implemented as a **React application** served by an **NGINX** web server. It serves as the external entry point for the entire system, handling user traffic and proxying API requests to the backend.

> [!IMPORTANT]
> **Repository Navigation**
>
> This document specifically covers the frontend tier located in the `default` namespace. To view the implementation of the other tiers, please check the other directories within the `manifests/` folder for **Backend** and **Database** related files.
---

## 🏗 Full System Architecture

The application is architected as a **Three-Tier System** distributed across two logical namespaces to ensure security, isolation, and service discovery.

### 1. Traffic Flow Lifecycle

A single user request follows this logical path:
**User Browser**  **LoadBalancer (Port 80)**  **Frontend Pod (NGINX)**  **Internal DNS**  **Backend Service**  **Backend Pod (Node.js)**  **MySQL Database**.

### 2. Architectural Components

* **Public Tier (Namespace: `default`):**
* **React Frontend:** Served as static content by NGINX.
* **NGINX Reverse Proxy:** Bridges the gap between the external browser and the internal cluster network.
* **LoadBalancer Service:** Provisions a public-facing IP address on GKE.


* **Private Tier (Namespace: `database`):**
* **Node.js API:** Handles business logic and database queries.
* **ClusterIP Service:** Restricts the backend so it is only reachable internally.
* **MySQL StatefulSet:** Provides data persistence with Persistent Volumes (PV).



---

## Kubernetes Resources Used

| Resource | Purpose |
| --- | --- |
| **Deployment** | Manages 2 replicas of the NGINX frontend pods. |
| **ConfigMap** | Injects the custom `default.conf` to enable the NGINX Reverse Proxy. |
| **Service (LoadBalancer)** | Provides the public IP for external user access. |

---

## Configuration: The Reverse Proxy Bridge

To allow the browser to communicate with a backend in a different namespace, NGINX is configured to forward all `/api/` traffic to the backend service.

**NGINX Proxy Configuration:**

```nginx
location /api/ {
    proxy_pass http://backend.database.svc.cluster.local:80/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
}

```

---

## 📸 Validation

### 1️⃣ Frontend Service & External IP

Verified that GKE has successfully provisioned a public-facing LoadBalancer.

**Command:**

```bash
kubectl get svc frontend -n default

```

**LoadBalancer Status**

<img width="906" height="139" alt="Screenshot 2026-02-20 at 6 10 01 PM" src="https://github.com/user-attachments/assets/855e15c4-7404-4fa6-adc8-f956e41ccae9" />


---

### 2️⃣ Multi-Tier Connectivity (End-to-End Test)

Verified the full communication chain: **Browser  Frontend  Backend  Database.**

**Command:**

```bash
# Testing the connection via the public IP
curl http://<EXTERNAL-IP>/api/

```

**Successful Handshake**

<img width="1083" height="396" alt="Screenshot 2026-02-20 at 6 10 55 PM" src="https://github.com/user-attachments/assets/9ff805f2-604c-4459-b13d-83616c60bf31" />


---

### 3️⃣ Namespace & Resource Distribution

Verified that the application tiers are correctly isolated across namespaces as per the assignment brief.

**Command:**

```bash
kubectl get pods -A | grep -E 'frontend|backend|mysql'

```

**Architecture Verification**

<img width="1282" height="255" alt="Screenshot 2026-02-20 at 6 12 17 PM" src="https://github.com/user-attachments/assets/1f9380d2-7578-4cb1-8f1a-93a6308109cd" />


---

## Conclusion

The frontend tier successfully integrates with the backend and database tiers through an NGINX reverse proxy. This setup demonstrates a production-ready Kubernetes architecture using **LoadBalancers**, **ConfigMaps**, **Namespaces**, and **Service DNS** to provide a seamless user experience while maintaining internal security.

---

Would you like me to help you create the **Network Policy** requested in Task 3 to finalize the security layer between your backend and database?
