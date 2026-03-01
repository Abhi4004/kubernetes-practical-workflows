# 🔵 Blue Environment (Stable - V1)

This folder contains the **Source of Truth** for the current stable production environment. 

## 🏗️ Architecture Overview
The Blue environment is a multi-tier application isolated by unique resource naming and version-specific labeling.

### 1. Naming Convention
Every resource in this directory follows the `-blue` suffix pattern to prevent naming collisions during parallel deployments.
- **Frontend:** `frontend-blue`
- **Backend:** `backend-blue`
- **Database:** `mysql-blue`

### 2. Traffic Routing (The "Switch")
All pods in this environment are tagged with the following version label:
- `version: v1`

The production service (`payment-gateway-prod`) currently points to this label to route 100% of user traffic here.

### 3. Networking & Service Discovery
Internal communication is strictly routed through Blue-specific DNS names:
- **Backend Host:** `http://backend-blue`
- **Database Host:** `mysql-blue.database.svc.cluster.local`

### 4. Persistence
- **PVC:** `mysql-pvc-blue` (Manual 1Ti)
- **StatefulSet Template:** `mysql-data-mysql-blue-0` (Dynamic 10Gi)

## 🚀 Deployment Status
- **ArgoCD Application Name:** `multi-tier-app-blue`
- **Branch:** `task3`
- **Sync Status:** Synced & Healthy ✅

<img width="1502" height="692" alt="Screenshot 2026-03-01 at 2 38 40 PM" src="https://github.com/user-attachments/assets/bde75ae5-8cd7-40ae-a615-3af0073aa0ed" />
