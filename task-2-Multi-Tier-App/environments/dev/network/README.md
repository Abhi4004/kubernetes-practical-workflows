## 📝 README: Network Security & Connectivity

### 1. Architecture Overview

This project implements a secure 3-tier architecture with namespace isolation.

* **Tier 1 (Frontend):** Located in the `default` namespace. Exposed via a GKE `LoadBalancer`.
* **Tier 2 (Backend):** Located in the `database` namespace. Exposed internally via `ClusterIP`.
* **Tier 3 (Database):** Located in the `database` namespace. Secured via `NetworkPolicy`.

### 2. Cross-Namespace Communication

Because the tiers are split across namespaces, the Frontend reaches the Backend using the **Fully Qualified Domain Name (FQDN)**:
`http://backend.database.svc.cluster.local:3000`

### 3. Network Policy: Zero-Trust Security

We implemented a **Least Privilege** security model. The MySQL database is locked down by default.

* **Target:** All pods with label `app: mysql`.
* **Allowed Ingress:** Only traffic originating from pods with label `app: backend`.
* **Result:** Even if a "Hacker" pod is created in the same namespace, it cannot access the database.

---

### 4. Troubleshooting & Verification Log

We encountered and resolved the following issues during implementation:

| Issue | Observation | Resolution |
| --- | --- | --- |
| **Bypass** | `hacker-pod` could reach MySQL | Enabled GKE Network Policy Addon and Enforcement. |
| **GKE Error 400** | Update failed | Enabled the Addon first, *then* the Enforcement engine. |
| **Fail-Open** | `nc` still showed "open" | Performed a rolling restart of the MySQL pod to force policy attachment. |
| **Final Proof** | `nc -zvw 5 mysql 3306` | **Success:** Returned "Connection timed out". |

---

### 5. Verification Commands

Use these commands to verify the security posture:

```bash
# Verify Backend can talk to DB
kubectl run verify-conn -it --rm -n database --image=curlimages/curl -- curl http://backend:3000

# Verify Unauthorized Pod is blocked (Should Time Out)
kubectl run hacker-pod -it --rm -n database --image=busybox -- nc -zvw 5 mysql 3306

```
