## 2026-02-26 – Day 3 (Argo CD + GitOps Deep Dive)

### Key GitOps Concepts Learned

1. Argo CD is NOT part of workload execution.
   - It does not run containers.
   - It does not perform rolling updates.
   - It only enforces that Git desired state matches cluster state.

2. If Argo CD is shut down:
   - Running Pods continue running.
   - Deployment controller continues managing ReplicaSets.
   - ReplicaSet controller continues managing Pods.
   - Argo only affects configuration reconciliation.

3. Ownership Chain in Kubernetes:
   - Deployment owns ReplicaSet.
   - ReplicaSet owns Pods.
   - Deleting Deployment cascades deletion to ReplicaSets and Pods.
   - Ownership is critical for understanding garbage collection behavior.

4. Difference Between Kubernetes Reconciliation and GitOps Reconciliation:
   - Kubernetes controllers reconcile object relationships (Deployment → ReplicaSet → Pod).
   - Argo reconciles Git state vs cluster spec state.
   - Argo enforces declarative drift.
   - Kubernetes enforces runtime drift.

5. Service Deletion Behavior:
   - If Service is deleted and Argo is disabled → it is not recreated.
   - Kubernetes has no controller to recreate Services.
   - If Argo is enabled → drift detected → Service recreated.

6. Pod Deletion Behavior:
   - If Pod is deleted → ReplicaSet recreates it.
   - Argo does not react because spec did not change.

7. API Server as Central Gateway:
   - All components communicate through API server.
   - Nothing talks directly to etcd except API server.
   - If API server is down → control plane is frozen.

8. Deployment Controller Behavior:
   - If Deployment controller is down:
     - Deployment object can still be stored in etcd.
     - ReplicaSet will not be created until controller restarts.
     - When controller returns, reconciliation happens automatically.

9. Auto-Sync Behavior in Argo:
   - Manual cluster changes create drift.
   - Auto-sync automatically restores Git-defined state.