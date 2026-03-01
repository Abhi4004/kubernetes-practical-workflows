# Persistent Storage & Shared Volumes

This directory contains the manifests required to fulfill the **Data Persistence** and **Inter-Container Communication** requirements of the e-commerce platform deployment.

## 📂 Overview
* **`filestore-sc.yaml`**: Defines a Custom StorageClass using the Google Cloud Filestore CSI driver to support `ReadWriteMany` (RWX) access.
* **`shared-pvc.yaml`**: A PersistentVolumeClaim (PVC) that provisions a 1Ti shared volume, allowing real-time file exchange between the Backend and Database tiers.

---

## 🛠️ Implementation Strategy

### 1. High Availability Shared Storage
To allow the **Backend (Deployment)** and **Database (StatefulSet)** to share files in real-time across different nodes, we implemented **Google Cloud Filestore**. Standard Persistent Disks (RWO) were insufficient as they only allow a single node to mount the volume at one time.

### 2. Data Persistence
* **Database Retention**: The MySQL database uses a `volumeClaimTemplate` within the StatefulSet to ensure core database data (`/var/lib/mysql`) is retained across pod restarts.
* **File Exchange**: Both tiers mount the `shared-data-pvc` at `/var/www/shared-data` to simulate a shared filesystem for e-commerce media or logs.

---

## ⚙️ Why the Filestore CSI Driver is Required

In a managed Kubernetes environment like GKE, the cluster doesn't natively know how to provision every type of cloud storage. We must enable the **Filestore CSI (Container Storage Interface) Driver** for the following reasons:

* **API Communication:** The CSI driver acts as the "translator" between Kubernetes and the Google Cloud Filestore API. Without it, Kubernetes cannot request the creation of the underlying NFS instance.
* **Access Mode Support:** Standard GKE persistent disks only support `ReadWriteOnce`. Enabling this specific driver is the only way to unlock the `ReadWriteMany` (RWX) capability required for our inter-pod file sharing.
* **Dynamic Provisioning:** The driver allows for "Dynamic Provisioning," meaning that when we apply our PVC, the driver automatically spins up the necessary hardware in the background without manual intervention in the Google Cloud Console.

---

## 🚀 Deployment & Validation

### Deployment
```bash
# 1. Enable Filestore CSI Driver (Cluster Level)
gcloud container clusters update enterprise-cluster --zone us-west2-a --update-addons=GcpFilestoreCsiDriver=ENABLED

# 2. Apply Storage Manifests
kubectl apply -f filestore-sc.yaml
kubectl apply -f shared-pvc.yaml

```

### Verification (The "Storage Bridge" Test)

To verify real-time file exchange between the tiers:

1. **Write from Backend**:
`kubectl exec -it <backend-pod-name> -n database -- sh -c "echo 'Real-time file exchange test: SUCCESS' > /var/www/shared-data/task2-verification.txt"`
2. **Read from MySQL**:
`kubectl exec -it mysql-0 -n database -- cat /var/www/shared-data/task2-verification.txt`

**Result:** The file created in the Backend is instantly visible in the Database pod, satisfying the Inter-Container Communication requirement.
