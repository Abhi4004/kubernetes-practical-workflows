---

# Backend Service – Kubernetes Deployment

## Overview

This directory contains Kubernetes manifests for deploying the **backend tier** of the multi-tier application as part of **Assignment-2**.
The backend is a scalable Node.js service that securely communicates with a MySQL database.

---

## Architecture & Design

The backend layer is designed with high-availability and robust initialization patterns:

* **Init Container Pattern:** Uses a dedicated `install-deps` container to manage `npm` dependencies. This ensures the main application container remains lightweight and only starts once dependencies are fully available.
* **Shared Ephemeral Storage:** Employs an `emptyDir` volume to securely pass the `node_modules` directory from the init container to the application container.
* **Decoupled Connectivity:** Uses the Fully Qualified Domain Name (FQDN) to reach the MySQL StatefulSet across the cluster.
* **Secure Secrets:** Consumes sensitive DB credentials through namespace-scoped Kubernetes Secrets.

---

## Kubernetes Resources Used

| Resource | Purpose |
| --- | --- |
| **Deployment** | Manages the desired state of 2 backend replicas. |
| **Init Container** | Handles the `npm install` of the `mysql2` driver. |
| **EmptyDir Volume** | Shared mounting point for application dependencies. |
| **Service (ClusterIP)** | Internal load balancer for the backend pods. |
| **Secret** | Injects DB host, user, and password into the environment. |

---

## Validation & Verification

### 1️⃣ Multi-Container Pod Status

The deployment was validated to ensure the lifecycle transitions from `Init` to `Running`.

```bash
kubectl get pods -n database

```

<img width="1486" height="290" alt="Screenshot 2026-02-20 at 5 25 10 PM" src="https://github.com/user-attachments/assets/403cf139-f4f1-4ab5-83fe-1e5798f713ad" />


**Observation:** Pods show a status of `Running`, confirming the `install-deps` init container finished successfully and the application container started.

---

### 2️⃣ End-to-End Database Connectivity (The Handshake)

To verify that the backend can successfully communicate with the MySQL database, a manual request was triggered from within the pod.

**Validation Command:**

```bash
kubectl exec -it <backend-pod-name> -n database -- curl localhost:3000

```

**Expected Output:**

```text
Backend: Connected to MySQL

```
<img width="927" height="186" alt="Screenshot 2026-02-20 at 5 33 21 PM" src="https://github.com/user-attachments/assets/0449b423-9bf1-415f-8563-c953d798186a" />


**What this proves:**

1. **Networking:** The backend pod can reach the MySQL service via DNS.
2. **Authentication:** The backend successfully used the **Kubernetes Secrets** to log into the database.
3. **Logic:** The Node.js `mysql2` driver is correctly installed and functional.

---

### 3️⃣ Application Logs

The logs confirm that the Node.js server is listening for traffic on the designated port.

**Command:**

```bash
kubectl logs -n database <pod-name>

```

**Output:**

```text
Defaulted container "backend" out of: backend, install-deps (init)
Backend running on port 3000

```
<img width="733" height="191" alt="Screenshot 2026-02-20 at 5 34 02 PM" src="https://github.com/user-attachments/assets/b6c71ccc-6089-4510-ab86-a8ba804e83fa" />

---

## Conclusion

The backend layer is successfully deployed and validated. By utilizing an **Init Container** for dependency management and **Secrets** for credential injection, the deployment follows Kubernetes best practices for security and reliability in a multi-tier architecture.

---
