## 2026-02-25

### What I Explored
- Inspected Kubernetes control plane components inside kind cluster.
- Used `docker exec` and `crictl ps` to view running control-plane containers.
- Identified the following components running as static pods:
  - kube-apiserver
  - kube-controller-manager
  - kube-scheduler
  - etcd
  - kube-proxy
  - coredns
- Understood that control plane components in kind run as static pods managed by kubelet.

---

### Key Architectural Insights

1. Kubernetes Communication Model
- All components communicate with the API Server.
- Only the API Server reads/writes etcd.
- Controllers, scheduler, and kubelet watch the API Server.
- Nothing talks directly to etcd except the API Server.

Mental model:
Controllers → API Server → etcd

---

2. Controller Hierarchy
- kube-controller-manager contains multiple controllers:
  - Deployment controller
  - ReplicaSet controller
  - Node controller
  - Job controller
  - ServiceAccount controller
  - Others
- Deployment manages ReplicaSets.
- ReplicaSet manages Pods.
- Separation of concerns is intentional and layered.

---

3. Scaling vs Template Change

Scaling (replicas 3 → 5):
- No new ReplicaSet is created.
- Existing ReplicaSet replica count is updated.
- ReplicaSet controller creates additional Pods.

Template change (image/env/resources change):
- New ReplicaSet is created.
- Rolling update begins.
- Old ReplicaSet is scaled down but retained.
- Controlled by `revisionHistoryLimit`.

---

4. Rollback Mechanics

Kubernetes rollback:
- Uses stored old ReplicaSets.
- `kubectl rollout undo` switches back to previous ReplicaSet.

Argo CD rollback:
- Revert Git commit.
- Argo syncs desired state.
- Kubernetes creates new ReplicaSet accordingly.

Argo CD does not manage ReplicaSets directly.
Git is the version history in GitOps.

---

5. Static Pods in kind
- Control plane components run as static pods.
- Static pods are defined in manifest files and managed directly by kubelet.
- If deleted, kubelet recreates them.
- In EKS, control plane is fully managed and not visible as pods.
- etcd is not accessible in managed clusters like EKS.

---

### Mistakes Corrected Today
- Initially confused scaling with new ReplicaSet creation.
- Clarified that new ReplicaSet is created only when Pod template changes.
- Understood that controllers communicate only via API Server.
- Corrected assumption that Argo CD interacts with etcd directly.

---

### Current Confidence Level
- Confident understanding of control plane component roles.
- Clear distinction between Deployment and ReplicaSet responsibilities.
- Clear understanding of scaling vs template changes.
- Clear separation between GitOps (Argo CD) and Kubernetes rollout logic.