---
# Argo CD Installation

## Purpose

Argo CD was installed as part of the Kubernetes cluster setup to enable **GitOps-based deployment** and configuration management. This ensures that the cluster is prepared for Git-driven application deployments and version control workflows.

---

## Installation Steps

### 1. Create Namespace

A dedicated namespace was created for Argo CD to maintain resource isolation:

```bash
kubectl create namespace argocd

```

### 2. Apply Manifests

Argo CD was installed using the official manifest with **server-side apply**:

```bash
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml \
  --server-side

```

---

## Reason for Server-Side Apply

Managed Kubernetes platforms, such as **Google Kubernetes Engine (GKE)**, enforce strict size limits on Kubernetes object annotations.

* **The Problem:** Some Argo CD CustomResourceDefinitions (CRDs), such as `ApplicationSet`, are quite large and may exceed these limits when using standard client-side apply.
* **The Solution:** Using `--server-side` apply avoids CRD annotation size issues and ensures a stable, production-safe installation.

---

## Result

Argo CD was successfully installed in the cluster and is ready to support GitOps workflows.

### Installation Verification

Check the status of the pods to ensure everything is running correctly:

```bash
kubectl get pods -n argocd

```

> [!TIP]
> **Verification Image:** >

<img width="1143" height="419" alt="Screenshot 2026-02-19 at 7 18 18 PM" src="https://github.com/user-attachments/assets/5e98f199-f4f4-4458-a645-21f873bcba37" />

---
