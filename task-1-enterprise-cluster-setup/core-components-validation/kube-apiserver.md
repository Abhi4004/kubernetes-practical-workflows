# `kube-apiserver` Validation

## Purpose

The `kube-apiserver` is the central management component of Kubernetes. It acts as the front end for the control plane, exposing the Kubernetes API. All cluster operations—whether initiated by users, external components, or internal cluster processes—are performed through the API server.

---

## Validation Method

The status and connectivity of the API server were validated using the following command:

```bash
kubectl cluster-info

```

### Execution Screenshot

<img width="1381" height="429" alt="Kube-apiserver" src="https://github.com/user-attachments/assets/96e86ba7-e52f-4163-9a37-431f022c7aa9" />


---

## Validation Results

As shown in the execution output, the API server and core cluster services are fully operational:

* **Kubernetes Control Plane:** Running at `https://35.235.93.207`
* **GLBCDefaultBackend:** Running at `https://35.235.93.207/api/v1/namespaces/kube-system/services/default-http-backend:http/proxy`
* **KubeDNS:** Running at `https://35.235.93.207/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy`
* **Metrics-server:** Running at `https://35.235.93.207/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy`

The successful response from these endpoints confirms that the **kube-apiserver** is correctly routing requests to the appropriate system services.

---
