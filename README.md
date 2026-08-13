# ArgoCD Hands-On Lab

A focused curriculum for learning ArgoCD on a single EKS cluster — from cluster creation through GitOps sync with the guestbook app.

## What You Will Learn

1. Create an EKS cluster with eksctl
2. Install ArgoCD and access the UI
3. Install Argo Rollouts
4. Create **AppProjects** and **Applications**
5. Understand sync policies, health status, and drift detection
6. Trigger a deployment by changing the guestbook image in Git

## Lab Flow

| Step | Topic | Guide |
|------|-------|-------|
| 1 | Create EKS cluster | [docs/01-create-eks-cluster.md](docs/01-create-eks-cluster.md) · [cluster/dev-cluster.yaml](cluster/dev-cluster.yaml) |
| 2 | Install ArgoCD | [docs/02-install-argocd.md](docs/02-install-argocd.md) |
| 3 | Install Argo Rollouts | [docs/03-install-argo-rollouts.md](docs/03-install-argo-rollouts.md) |
| 4 | Applications & Projects | [docs/04-applications-and-projects.md](docs/04-applications-and-projects.md) |
| 5 | Sync & image updates | [docs/05-sync-and-image-updates.md](docs/05-sync-and-image-updates.md) |
| 6 | RBAC (optional) | [RBAC/rbac.md](RBAC/rbac.md) |
| — | Cleanup | [cleanup-resource.md](cleanup-resource.md) |

## Repository Layout

```
├── cluster/
│   └── dev-cluster.yaml          # eksctl config — auto VPC/subnets
├── manifests/
│   ├── guestbook/                # dev, staging, prod manifests
│   └── guestbook-rollout/        # Argo Rollout — progressive delivery
├── applications/
│   ├── project.yaml              # AppProject definition
│   ├── guestbook-dev.yaml        # Application → dev namespace
│   ├── guestbook-staging.yaml    # Application → staging namespace
│   ├── guestbook-prod.yaml       # Application → prod namespace
│   └── guestbook-rollout.yaml    # Application (Rollout)
└── docs/                         # Step-by-step guides
```

## Quick Start

```bash
# 1. Create cluster
eksctl create cluster -f cluster/dev-cluster.yaml
aws eks update-kubeconfig --region ap-south-1 --name dev-cluster

# 2. Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 3. Install Argo Rollouts
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml

# 4. Register repo and deploy AppProject + Application
argocd repo add https://github.com/DPP-2026/Argocd.git
kubectl apply -f applications/project.yaml
kubectl apply -f applications/guestbook-dev.yaml
kubectl apply -f applications/guestbook-staging.yaml
kubectl apply -f applications/guestbook-prod.yaml
```

Repository: [https://github.com/DPP-2026/Argocd](https://github.com/DPP-2026/Argocd)
