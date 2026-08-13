# Cleanup Resources

## Delete ArgoCD Applications

```bash
argocd app delete guestbook-dev --cascade
argocd app delete guestbook-staging --cascade
argocd app delete guestbook-prod --cascade
argocd app delete guestbook-rollout --cascade
```

Or via kubectl:

```bash
kubectl delete -f applications/guestbook-dev.yaml
kubectl delete -f applications/guestbook-staging.yaml
kubectl delete -f applications/guestbook-prod.yaml
kubectl delete -f applications/guestbook-rollout.yaml
kubectl delete -f applications/project.yaml
```

## Uninstall ArgoCD and Argo Rollouts

```bash
kubectl delete namespace argocd
kubectl delete namespace argo-rollouts
```

## Delete the EKS Cluster

```bash
eksctl delete cluster -f cluster/dev-cluster.yaml
```

## Verify Cleanup

```bash
kubectl config get-contexts
aws eks list-clusters --region ap-south-1
```
