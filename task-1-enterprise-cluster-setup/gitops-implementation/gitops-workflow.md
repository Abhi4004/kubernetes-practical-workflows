# GitOps Workflow Using Argo CD

## Overview
This document describes the GitOps workflow implemented for the Kubernetes cluster using Argo CD.

GitOps ensures that Git is the single source of truth for all Kubernetes configurations and that the cluster state is continuously reconciled with the desired state stored in Git.

---

## Git as the Source of Truth
All Kubernetes manifests and configuration changes are stored and version-controlled in Git repositories.

Any change to the cluster is performed by:
1. Updating configuration in Git
2. Committing the change to the appropriate branch
3. Allowing Argo CD to automatically reconcile the cluster state

Direct manual changes to the cluster are avoided to prevent configuration drift.

---

## Role of Argo CD
Argo CD runs as a controller inside the Kubernetes cluster and continuously performs the following actions:

- Monitors Git repositories for configuration changes
- Compares the desired state in Git with the actual cluster state
- Automatically synchronizes the cluster when differences are detected
- Provides visibility into deployment status and configuration drift

This enables automated deployments, rollbacks, and consistent environments.

---

## Environment and Branching Strategy
The following Git branches are used to manage different environments:

- **dev**: Development and testing environment
- **staging**: Pre-production validation environment
- **production**: Live production environment

Each branch represents the desired state of its corresponding environment and is independently tracked by Argo CD.

This branching strategy will be actively used in **Assignment-2** to deploy and manage a multi-tier application.

---

## Rollouts and Rollbacks
- New application versions are deployed by committing changes to Git
- Rollbacks are performed by reverting to a previous commit or tag
- Argo CD automatically reconciles the cluster to the selected Git state

This approach provides safe, auditable, and repeatable deployment workflows.

---

## Conclusion
By integrating Argo CD into the Kubernetes cluster, GitOps practices are established at the platform level. This workflow enables consistent, automated, and version-controlled deployments and prepares the cluster for application-level GitOps usage in subsequent assignments.
