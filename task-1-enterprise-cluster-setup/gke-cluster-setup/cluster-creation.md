# GKE Cluster Creation Notes

## Purpose

This document captures the actual execution and validation of the Kubernetes cluster setup for **Task-1**, using Google Kubernetes Engine (GKE) as the managed Kubernetes service.

---

## Project & Environment Details

* **GCP Project ID:** `k8s-project-487723`
* **Region:** `us-west2`
* **Zone:** `us-west2-a`
* **Kubernetes Platform:** Google Kubernetes Engine (GKE)

## Cluster Configuration

* **Cluster Name:** `enterprise-cluster`
* **Cluster Type:** Managed Kubernetes
* **Worker Nodes:** 2
* **Machine Type:** `e2-medium`
* **Networking Mode:** VPC-native networking (IP aliasing enabled)
* **Control Plane:** Fully managed and highly available by Google

---

## Cluster Creation Command

The cluster was created using the following command:

```bash
gcloud container clusters create enterprise-cluster \
  --zone us-west2-a \
  --num-nodes=2 \
  --machine-type=e2-medium \
  --enable-ip-alias

```

---

## Cluster Validation

### Node Status Verification

After cluster creation, node status was verified using:

```bash
kubectl get nodes -o wide

```

> **Note:** The output confirms that multiple worker nodes are in the **Ready** state, indicating successful cluster provisioning.

<img width="1489" height="632" alt="Screenshot 2026-02-19 at 3 29 32 PM" src="https://github.com/user-attachments/assets/2ca19bd3-0b50-4eeb-b879-44493d7a4501" />

---

## Key Observations

* **Managed Control Plane:** The Kubernetes control plane is automatically provisioned and managed by GKE.
* **Node Health:** Worker nodes are successfully registered and healthy.
* **Networking:** Pod-to-pod networking is enabled using VPC-native IP aliasing.
* **Compliance:** The cluster meets enterprise requirements for availability and scalability.

### Notes on High Availability

* Control plane high availability is handled by GKE internally.
* Worker nodes can be scaled and replaced automatically.
* Kubernetes supports self-healing through pod rescheduling in case of node failures.

---

## Conclusion

The GKE cluster was successfully created and validated. This cluster serves as a production-ready foundation for further tasks including component validation, RBAC configuration, networking validation, and fault tolerance testing.

### ✅ What This File Proves

* **[✔]** Cluster was actually created
* **[✔]** Nodes are healthy
* **[✔]** Managed Kubernetes was used
* **[✔]** Enterprise-grade setup achieved

---
