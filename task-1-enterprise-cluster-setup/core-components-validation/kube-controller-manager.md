# `kube-controller-manager` Validation

## Purpose

The `kube-controller-manager` is the control plane component that runs controller processes. Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process. These include the Node controller, Job controller, EndpointSlice controller, and ServiceAccount controller.

---

## Validation Method

Because the controller-manager is responsible for maintaining the shared state of the cluster and ensuring that the current state matches the desired state, its health is validated by monitoring the stability of system-level resources:

```bash
kubectl get pods -n kube-system

```

---

## Validation Results

### Execution Screenshot

<img width="1229" height="592" alt="Screenshot 2026-02-19 at 3 48 17 PM" src="https://github.com/user-attachments/assets/327e5526-3850-4491-8f4a-d6c57e321c68" />


### Key Observations

* **State Management:** Controllers are successfully maintaining the desired cluster state, as evidenced by the consistent availability of system services.
* **Stability:** No abnormal pod restarts or "CrashLoopBackOff" states were observed, indicating that the reconciliation loops (Node, Deployment, and Endpoint controllers) are functioning correctly.
* **GKE Managed:** In this environment, the `kube-controller-manager` is managed by Google, providing automated health monitoring and failover.

---

## Conclusion

The **kube-controller-manager** is healthy and operational. The lack of state drift and the steady status of system pods confirm that the control loops are successfully regulating the cluster.

---
