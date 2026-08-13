# Step 1 — Create an EKS Cluster with eksctl

## Prerequisites

Install and configure these tools before starting:

| Tool | Install guide |
|------|---------------|
| AWS CLI | [docs.aws.amazon.com/cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) |
| eksctl | [eksctl.io/installation](https://eksctl.io/installation/) |
| kubectl | [kubernetes.io/docs/tasks/tools](https://kubernetes.io/docs/tasks/tools/) |

Configure AWS credentials:

```bash
aws configure
# Enter: Access Key, Secret Key, region (ap-south-1), output format (json)
```

Verify:

```bash
aws sts get-caller-identity
eksctl version
```

## Cluster Config

The lab uses [`cluster/dev-cluster.yaml`](../cluster/dev-cluster.yaml). It does **not** specify a custom VPC — eksctl automatically creates a VPC with public and private subnets in your AWS account.

```yaml
metadata:
  name: dev-cluster
  region: ap-south-1
  version: "1.32"

nodeGroups:
  - name: ng-1
    instanceType: t2.small
    desiredCapacity: 2
```

## Create the Cluster

```bash
eksctl create cluster -f cluster/dev-cluster.yaml
```

This takes approximately 15–20 minutes. eksctl will:

1. Create a VPC with public and private subnets across availability zones
2. Create the EKS control plane (`dev-cluster`)
3. Launch 2 worker nodes (`t2.small`)

Expected final output:

```
[✔]  EKS cluster "dev-cluster" in "ap-south-1" region is ready
```

## Configure kubectl

```bash
aws eks update-kubeconfig --region ap-south-1 --name dev-cluster
kubectl config current-context
```

## Verify

```bash
kubectl get nodes
kubectl cluster-info
```

Expected: 2 nodes in `Ready` state.

```
NAME                                          STATUS   ROLES    AGE   VERSION
ip-xxx.ap-south-1.compute.internal            Ready    <none>   5m    v1.32.x
ip-xxx.ap-south-1.compute.internal            Ready    <none>   5m    v1.32.x
```

## Cluster Config Reference

| Field | Value | Purpose |
|-------|-------|---------|
| `metadata.name` | `dev-cluster` | Cluster name in AWS |
| `metadata.region` | `ap-south-1` | AWS region (change if needed) |
| `nodeGroups[].instanceType` | `t2.small` | Worker node size |
| `nodeGroups[].desiredCapacity` | `2` | Number of worker nodes |

> **Tip:** To use a different region, change `region` in `cluster/dev-cluster.yaml` and update `--region` in subsequent commands.

## Delete the Cluster

```bash
eksctl delete cluster -f cluster/dev-cluster.yaml
```

## Next Step

→ [Install ArgoCD](02-install-argocd.md)
