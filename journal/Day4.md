Topic: Argo CD Reconciliation Loop & Self-Healing

1. Custom Resource Definition (CRD)

Kubernetes allows extending its API using Custom Resource Definitions (CRDs).
A CRD defines a new resource type that Kubernetes understands.

Example from the cluster:

applications.argoproj.io

This CRD was installed by Argo CD. It defines a new resource type called Application.

CRD vs Custom Resource:

CRD → Defines the resource type
CR  → Actual instance of that resource

Example:

CRD:
applications.argoproj.io

Custom Resource:
hello-ngnix

The hello-ngnix object is an instance of the Application resource type.

⸻

2. Argo CD Application Resource

An Argo CD Application is a Kubernetes object that tells Argo CD:
	•	Where the application configuration exists in Git
	•	Where to deploy it in the cluster
	•	How to synchronize it

Example fields from the Application YAML:

spec.source
spec.destination
syncPolicy
status

⸻

3. spec.source (Where the desired state comes from)

spec.source defines the Git repository and path containing the application manifests.

Example:

spec:
source:
repoURL: https://github.com/hazanulzaman/mission-2028.git
path: apps/hello-nginx
targetRevision: HEAD

Meaning:

repoURL → Git repository containing the manifests
path → Folder containing deployment.yaml and service.yaml
targetRevision → Branch or commit Argo CD should track

Argo CD continuously reads this location to determine the desired state.

⸻

4. spec.destination (Where to deploy)

spec.destination tells Argo CD where to apply the manifests.

Example:

destination:
namespace: default
server: https://kubernetes.default.svc

Meaning:

server → Target Kubernetes cluster
namespace → Namespace where the application will run

In this setup:

Application CR → argocd namespace
Deployment & Service → default namespace

⸻

5. syncPolicy (How synchronization works)

syncPolicy controls how Argo CD performs deployment.

Example:

syncPolicy:
automated:
prune: true
selfHeal: true

Meaning:

automated → Automatically sync when Git changes
prune → Delete resources removed from Git
selfHeal → Fix manual changes in the cluster

⸻

6. Self-Healing Experiment

Goal: Demonstrate the reconciliation loop.

Steps:
	1.	Enable auto-sync and self-heal in the Application.
	2.	Manually change the deployment in the cluster.
	3.	Wait and observe Argo CD revert the change.

Result:

When replicas were manually changed to 5, Argo CD automatically restored the value defined in Git.

This proves that Git is the source of truth.

⸻

7. Reconciliation Loop in Argo CD

Argo CD continuously performs a reconciliation loop.

Process:
	1.	Watch Application custom resources.
	2.	Fetch desired state from Git.
	3.	Compare desired state with live cluster state.
	4.	Detect drift between Git and cluster.
	5.	Apply changes to make cluster match Git.

This loop ensures the cluster always reflects the configuration stored in Git.

⸻

8. Key Insight

Manual changes to the cluster do not persist when GitOps is used.

If a user modifies resources directly using kubectl, Argo CD will revert them to match the Git repository.

Therefore:

Git = single source of truth.

### 9. GitOps Definition

GitOps is an operational model where Git acts as the single source of truth for infrastructure and application configuration, and an automated controller continuously reconciles the live system to match the state defined in Git.

In a GitOps workflow:

1. Infrastructure and application manifests are stored in Git.
2. Any change to the system is made through a Git commit.
3. A controller monitors the repository.
4. The controller compares the desired state in Git with the actual state in the cluster.
5. If differences are detected, the controller updates the cluster to match Git.

In this setup, Argo CD acts as the controller that implements GitOps for Kubernetes by continuously synchronizing the cluster with the configuration stored in Git.

⸻

Commands Used Today

List Argo CD CRDs:

kubectl get crd | grep argoproj

List Argo CD Applications:

kubectl get applications.argoproj.io -n argocd

Get YAML of the Application:

kubectl get application hello-ngnix -n argocd -o yaml

View Argo CD controller logs:

kubectl get pods -n argocd

kubectl logs -n argocd argocd-application-controller-0

Enable auto-sync and self-heal:

kubectl edit application hello-ngnix -n argocd

Add:

syncPolicy:
automated:
prune: true
selfHeal: true

or, run the cmd ;

kubectl -n argocd patch application hello-ngnix --type merge -p '{ 
  "spec": {
    "syncPolicy": {
      "automated": {
        "prune": true,
        "selfHeal": true
      }
    }
  }
}'

Scale deployment manually (drift test):

kubectl -n default scale deployment hello-nginx –replicas=5

Watch deployment changes:

kubectl -n default get deployment hello-nginx -w

Stop watch:

Ctrl + C

Check replica count:

kubectl -n default get deploy hello-nginx -o jsonpath=’{.spec.replicas}’