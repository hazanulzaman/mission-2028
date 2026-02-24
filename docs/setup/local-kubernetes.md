# Local Kubernetes Setup (kind) – Mission 2028

## Objective

Set up a fully functional local Kubernetes cluster to understand core Kubernetes architecture before working with managed cloud solutions like AWS EKS.

This setup simulates a real cluster environment locally using Docker.

---

## Tools Used

- Docker
- kind (Kubernetes in Docker)
- kubectl

---

## What is kind?

kind = Kubernetes IN Docker.

It runs a real Kubernetes control plane inside Docker containers on the local machine.

This allows:
- Local cluster experimentation
- Deployment testing
- Scheduling behavior understanding
- Zero cloud cost learning

---

## Installation Steps

### 1. Install Docker

Install using Homebrew:

    brew install --cask docker

Start Docker application and verify:

    docker ps
    docker run --rm hello-world

---

### 2. Install kubectl

    brew install kubectl
    kubectl version --client

---

### 3. Install kind

    brew install kind
    kind --version

---

## Create Cluster

    kind create cluster --name mission-2028

This command:

- Pulls the Kubernetes node image
- Creates a Docker container acting as a Kubernetes node
- Starts control plane components
- Updates kubeconfig
- Sets kubectl context

---

## Verify Cluster

    kubectl cluster-info
    kubectl get nodes

Expected Output:

    mission-2028-control-plane   Ready   control-plane

---

## Architecture Understanding

Local Setup:

Mac OS  
↓  
Docker  
↓  
kind control-plane container  
↓  
Kubernetes control plane  
↓  
Pods  

Important Concepts:

- Node = Machine that runs containers
- Control Plane = Brain of Kubernetes
- Scheduler assigns pods to nodes
- etcd stores cluster state
- kubelet runs on nodes

---

## Key Learnings

- Kubernetes is infrastructure-agnostic.
- EKS is managed Kubernetes, not a different system.
- Pods are scheduled based on declared resource requirements.
- If a node fails, pods are recreated elsewhere (if capacity exists).
- Desired state enforcement is core Kubernetes principle.