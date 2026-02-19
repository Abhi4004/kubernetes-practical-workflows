
---

# Node Failure Simulation: Kubernetes Self-Healing

## 🎯 Objective

To simulate a worker node failure in a Kubernetes cluster and observe how the Control Plane handles pod rescheduling and maintains high availability.

---

## 🔍 Phase 1: Initial Cluster State

Before simulating a failure, we must establish a baseline to confirm all nodes are healthy and pods are distributed across the infrastructure.

### 1. Worker Node Status

Verify that all nodes are currently in the `Ready` state.

```bash
kubectl get nodes

```

**Output:**

<img width="1466" height="371" alt="Screenshot 2026-02-19 at 6 04 57 PM" src="https://github.com/user-attachments/assets/10757164-7d95-4d62-b30a-5bfadbea4345" />


### 2. Pod Distribution

Capture the current pod placement. Take note of which pods are running on the node you intend to "fail."

```bash
kubectl get pods -o wide --all-namespaces

```

**Output:**

<img width="1659" height="521" alt="Screenshot 2026-02-19 at 6 06 12 PM" src="https://github.com/user-attachments/assets/6ee82270-d64d-4958-ae97-7f11c06c7804" />

---

## 🛠 Phase 2: Simulating Failure (Graceful Eviction)

To observe self-healing, we will "drain" a worker node. This command signals the Control Plane to stop scheduling new pods on the node and safely migrates existing pods to other healthy nodes.

### 1. Execute the Drain Command

Run the following command from your terminal, replacing `<NODE_NAME>` with the specific worker node you identified in Phase 1:

```bash
kubectl drain <NODE_NAME> --ignore-daemonsets --delete-emptydir-data

```

> **Note on Flags:**
> * `--ignore-daemonsets`: Skips pods managed by DaemonSets (which must run on every node).
> * `--delete-emptydir-data`: Continues even if pods use `emptyDir` volumes (which will be deleted).
> 
> 

### 2. Verification of Node Status

Once the drain is complete, check the node status. The node will remain `Ready`, but its **SchedulingDisabled** status will prevent new pods from landing there.

```bash
kubectl get nodes -o wide

```

**Output:**
The status of the targeted node should now show `Ready,SchedulingDisabled`.

<img width="1660" height="270" alt="Screenshot 2026-02-19 at 6 13 34 PM" src="https://github.com/user-attachments/assets/b2e55808-83d3-4e1d-ac13-601bc5db7956" />

---

## 🧪 Phase 3: Observing Self-Healing

With the node drained, we can verify that the cluster maintained the desired state by moving the workload to other healthy nodes.

### 1. Pod Migration Verification

Initially, the targeted node was running **8 pods**. After the drain command, only **6 pods** (specifically DaemonSet-managed pods) remain on the node. All application-level workloads have been successfully evicted and rescheduled.

To verify the pods remaining on the drained node, run:

```bash
kubectl get pods -A --field-selector spec.nodeName=gke-enterprise-cluster-default-pool-3ee0c216-1d7h

```

**Output:**

<img width="1126" height="202" alt="Screenshot 2026-02-19 at 6 22 46 PM" src="https://github.com/user-attachments/assets/eba52bea-6c53-4235-8aaf-1cdfcd8b2418" />

---

### 2. Global Cluster Verification

Confirm that the application pods that were evicted are now running on the surviving worker nodes.

```bash
kubectl get pods -o wide --all-namespaces

```

**Output:**

<img width="1667" height="558" alt="Screenshot 2026-02-19 at 6 24 20 PM" src="https://github.com/user-attachments/assets/f90c2ecd-b058-4575-befc-658402f811ea" />


---

### **Observation:**

While the cluster is attempting to maintain high availability, the recovery is currently incomplete:

* **Self-Healing in Progress:** Most system and application pods have successfully migrated to the healthy node (`...-2w98`).
* **Pending Pod:** The pod `kube-dns-7f686f5ff5-9pj59` is currently in a **Pending** state and has not been scheduled to a node.
* **Resource Constraints:** This typically occurs because the surviving node lacks the available CPU or Memory capacity to host another instance of `kube-dns`, or there are anti-affinity rules preventing multiple DNS pods from running on the same single node.
  
**Output:**

<img width="1657" height="237" alt="Screenshot 2026-02-19 at 6 30 44 PM" src="https://github.com/user-attachments/assets/1f6e4e3d-5fc5-493c-81a9-ed1c722f7d00" />

---

### **How to verify the exact cause**

To see exactly why that specific pod isn't scheduling, you can run:

```bash
kubectl describe pod kube-dns-7f686f5ff5-9pj59 -n kube-system

```

Look at the **Events** section at the bottom of the output. It will likely say something like:

* `0/1 nodes are available: 1 Insufficient cpu.`
* `node(s) had untolerated taint.`
* `node(s) didn't match pod anti-affinity rules.`
  
**Output:**

<img width="1662" height="325" alt="Screenshot 2026-02-19 at 6 31 47 PM" src="https://github.com/user-attachments/assets/69b64246-9cac-4739-b1a6-8ec72f35ce03" />

---

### **After You Finish (Cleanup)**

To restore the cluster to its full operational capacity and resolve any **Pending** states, you must bring the node back into the scheduling pool.

#### **1. Bring the node back**

Run the following command to allow the scheduler to place pods on the node again:

```bash
kubectl uncordon gke-enterprise-cluster-default-pool-3ee0c216-1d7h

```
**Output:** 

<img width="778" height="168" alt="Screenshot 2026-02-19 at 6 39 37 PM" src="https://github.com/user-attachments/assets/b6f6fde4-3dbf-4a82-ad05-1e651b05fdf0" />

---

#### **2. Verification**

After running this command, monitor your pods. The `kube-dns` pod should automatically transition:

* **From:** `Pending`
* **To:** `ContainerCreating`
* **Finally:** `Running`

This occurs because the scheduler now identifies a valid, schedulable target node.

**Output:**  
<img width="1586" height="527" alt="Screenshot 2026-02-19 at 6 39 54 PM" src="https://github.com/user-attachments/assets/7188cf2d-3f79-446e-a7a5-420917e6396c" />

---

### 📝 Summary of Observations

| Feature | Behavior Observed |
| --- | --- |
| **Node Detection** | Node status changed from `Ready` to `NotReady`. |
| **Pod Eviction** | Pods on the failed node were marked for eviction. |
| **Self-Healing** | Scheduler recreated pods on healthy nodes to match desired counts. |
| **Scheduling Limits** | `kube-dns` remained `Pending` due to Anti-Affinity/Resource constraints. |
| **Restoration** | `uncordon` allows the cluster to re-balance and resolve pending workloads. |

---
