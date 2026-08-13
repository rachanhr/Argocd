# Cluster Creation

Use **eksctl** with [`dev-cluster.yaml`](dev-cluster.yaml). No custom VPC or Terraform required — eksctl creates the VPC and subnets automatically.

```bash
eksctl create cluster -f cluster/dev-cluster.yaml
aws eks update-kubeconfig --region ap-south-1 --name dev-cluster
kubectl get nodes
```

Full guide: [docs/01-create-eks-cluster.md](../docs/01-create-eks-cluster.md)

Delete:

```bash
eksctl delete cluster -f cluster/dev-cluster.yaml
```
