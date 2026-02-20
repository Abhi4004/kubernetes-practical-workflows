
---

# Kubernetes Cluster Architecture (Task-1)

## Overview

This architecture represents an **enterprise-grade Kubernetes cluster deployed on Google Kubernetes Engine (GKE)**, designed to support high availability, scalability, and GitOps-based continuous deployment using **Argo CD**.

The cluster follows a **managed control plane + worker node execution model**, with GitHub acting as the single source of truth for application and infrastructure manifests.

---

## High-Level Architecture Components

The cluster is divided into two primary layers:

1. **Control Plane (Managed by GKE)**
2. **Worker Node Plane (Application & System Execution)**

Additionally, **GitHub and Argo CD** enable a GitOps workflow for declarative configuration management.

---

## Control Plane (Managed by GKE)

The control plane is fully managed by Google and is responsible for **cluster state management and decision-making**. It does not run user workloads.

### Control Plane Components

* **kube-apiserver**

  * Entry point for all cluster interactions
  * Handles requests from `kubectl`, Argo CD, controllers, and worker nodes
  * Performs authentication, authorization (RBAC), and validation

* **kube-scheduler**

  * Decides which worker node should run a newly created pod
  * Considers resource availability, affinity rules, and constraints

* **kube-controller-manager**

  * Runs core Kubernetes controllers (Deployment, ReplicaSet, Node, etc.)
  * Continuously reconciles actual state with desired state

* **etcd**

  * Distributed key-value store
  * Stores the entire cluster state and configuration

> In GKE, all control plane components are abstracted and maintained by Google, improving reliability and operational simplicity.

---

## Worker Node Plane

The worker node plane is where **all workloads and system pods execute**.
The cluster contains a **worker node pool** with multiple nodes to ensure fault tolerance and scalability.

### Worker Node Components

Each worker node runs:

* **kubelet**

  * Node-level agent (not a pod, not in any namespace)
  * Communicates with the kube-apiserver
  * Ensures containers are running as instructed

* **Container Runtime (containerd)**

  * Pulls container images
  * Runs and manages containers

* **Application and system pods**

  * Scheduled and managed by the control plane

---

## kube-system Namespace

The `kube-system` namespace contains **critical Kubernetes system pods** that support networking and service discovery.

### Components in kube-system

* **CoreDNS**

  * Provides internal DNS resolution for services
  * Enables service-to-service communication

* **kube-proxy**

  * Runs as a DaemonSet (one pod per node)
  * Handles service networking and load balancing using iptables/IPVS

> These pods run on worker nodes but are logically grouped under the `kube-system` namespace.

---

## Argo CD Namespace (GitOps Layer)

Argo CD is deployed in a dedicated namespace and enables **GitOps-based continuous delivery**.

### Argo CD Components

* **argocd-repo-server**

  * Connects to GitHub repositories
  * Fetches Kubernetes manifests

* **argocd-application-controller**

  * Compares desired state (Git) with live state (cluster)
  * Applies changes and corrects drift

* **argocd-applicationset-controller**

  * Enables managing multiple applications using templates
  * Used for multi-environment deployments (dev, staging, production)

All Argo CD components run as pods on worker nodes.

---

## GitOps Workflow Integration

* **Users commit Kubernetes manifests to GitHub**
* **GitHub acts as the single source of truth**
* **Argo CD continuously pulls manifests from GitHub**
* **Argo CD syncs the desired state to the Kubernetes API**
* **The control plane schedules workloads onto worker nodes**
* **Continuous drift detection and reconciliation is enforced**

This ensures:

* Declarative deployments
* Auditability via Git history
* Consistent environments across stages

---

## Communication Flow Summary

1. **Admin → kube-apiserver**

   * Cluster administrator using `kubectl`

2. **User → GitHub**

   * Code and manifest changes committed

3. **GitHub → Argo CD**

   * Argo CD pulls manifests (pull-based GitOps model)

4. **Argo CD → kube-apiserver**

   * Desired state reconciliation

5. **Control Plane → Worker Nodes**

   * Scheduling and execution of pods

---

## Key Architectural Benefits

* High availability through multi-node worker pools
* Managed control plane reduces operational overhead
* GitOps ensures consistency, traceability, and automation
* Clear separation of control, execution, and delivery layers
* Ready for multi-environment deployments in future tasks

---

## Conclusion

This architecture establishes a **production-ready Kubernetes foundation** using GKE and Argo CD. It aligns with enterprise best practices by combining managed infrastructure, strong separation of responsibilities, and a GitOps-driven deployment model. This setup serves as the base for deploying and managing multi-tier applications in subsequent tasks.

---
