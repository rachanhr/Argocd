# Create EKS Cluster

Create the lab cluster with **eksctl** using the default auto-created VPC and subnets.

```bash
aws configure
eksctl create cluster -f cluster/dev-cluster.yaml
aws eks update-kubeconfig --region ap-south-1 --name dev-cluster
kubectl get nodes
```

Full guide: [docs/01-create-eks-cluster.md](docs/01-create-eks-cluster.md)

Delete when done:

```bash
eksctl delete cluster -f cluster/dev-cluster.yaml
```
