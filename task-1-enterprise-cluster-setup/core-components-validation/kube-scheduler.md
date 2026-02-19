# `kube-scheduler` Validation

## Purpose

The `kube-scheduler` is a critical control plane component that watches for newly created Pods with no assigned node and selects a healthy node for them to run on. In a managed environment like GKE, this component is fully handled by Google to ensure high availability.

---

## Validation Method

Since the scheduler's success is reflected in the ability of system components to deploy and maintain a `Running` state, validation is performed by inspecting the `kube-system` namespace:

```bash
kubectl get pods -n kube-system

```

---

## Validation Results

### Execution Screenshot
<img width="1229" height="592" alt="Screenshot 2026-02-19 at 3 48 17 PM" src="https://github.com/user-attachments/assets/f9fa7dee-d5db-4f21-93ba-fe8ca62418ae" />

### Key Observations

* **Pod Status:** All essential system pods are in the `Running` state without failures.
* **Managed Infrastructure:** As this is a GKE cluster, the `kube-scheduler` is part of the managed control plane, ensuring automated lifecycle management and scaling.
* **Scheduling Efficiency:** The successful distribution of system services across the worker nodes confirms the scheduler is accurately evaluating node resources and constraints.

---

## Conclusion

The **kube-scheduler** is functioning correctly. The stability of the system-level workloads confirms that the scheduling logic is operational and successfully placing pods onto available resources.

---
