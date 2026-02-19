---
## Objective
To validate that **Argo CD** is successfully deployed and operational as the GitOps controller within the Kubernetes cluster.

---

## Pod Validation
All core Argo CD components are running successfully in the `argocd` namespace.

**Command used:**

```bash
kubectl get pods -n argocd

```

**Observed components:**

* `argocd-application-controller`
* `argocd-applicationset-controller`
* `argocd-server`
* `argocd-repo-server`
* `argocd-dex-server`
* `argocd-redis`
* `argocd-notifications-controller`

### Evidence

<img width="774" height="188" alt="Screenshot 2026-02-19 at 7 50 40 PM" src="https://github.com/user-attachments/assets/e41976db-3de3-4d34-afe7-af2993e58e77" />


---

## Service Validation

Argo CD services are available within the cluster using `ClusterIP` services, enabling secure internal communication between components.

**Command used:**

```bash
kubectl get svc -n argocd

```

### Evidence

<img width="1085" height="217" alt="Screenshot 2026-02-19 at 7 49 39 PM" src="https://github.com/user-attachments/assets/3ee773ee-3f34-43e3-b8ee-df9aea361a1e" />


---

## GitOps Readiness

The successful validation confirms that:

1. **Argo CD is operational:** All controller and server components are healthy.
2. **GitOps tooling is active:** The cluster can now monitor Git repositories for changes.
3. **Platform Ready:** The environment is prepared for Git-driven application deployments.

## Conclusion

The Kubernetes cluster is **GitOps-ready** and prepared to support application-level GitOps workflows in subsequent assignments.
