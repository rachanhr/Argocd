# Guestbook Manifests

Plain Kubernetes manifests for dev, staging, and prod. Each environment folder is deployed by its matching ArgoCD Application.

## Layout (same files in each environment)

```
manifests/guestbook/
├── dev/
├── staging/
└── prod/
```

| File | Kind | Name | Purpose |
|------|------|------|---------|
| `deployment.yaml` | Deployment | `guestbook-ui` | PHP frontend |
| `service.yaml` | Service | `guestbook-ui` | ClusterIP — port 80 |
| `redis-leader-deployment.yaml` | Deployment | `redis-leader` | Redis write master |
| `redis-leader-service.yaml` | Service | `redis-leader` | ClusterIP — port 6379 |
| `redis-follower-deployment.yaml` | Deployment | `redis-follower` | Redis read replica |
| `redis-follower-service.yaml` | Service | `redis-follower` | ClusterIP — port 6379 |

## Per-environment differences

| Environment | Namespace | Frontend replicas | Redis follower replicas |
|-------------|-----------|-------------------|-------------------------|
| dev | `dev` | 1 | 1 |
| staging | `staging` | 2 | 2 |
| prod | `prod` | 3 | 2 |

All environments use:

- Frontend: `us-docker.pkg.dev/google-samples/containers/gke/gb-frontend:v5`
- Redis leader: `redis:7-alpine`
- Redis follower: `us-docker.pkg.dev/google-samples/containers/gke/gb-redis-follower:v2`
- `GET_HOSTS_FROM=dns` on the frontend (required for Submit to work)

## Apply locally (Docker Desktop example)

```bash
kubectl config use-context docker-desktop
kubectl apply -n dev -f manifests/guestbook/dev/
kubectl get pods,svc -n dev
kubectl port-forward svc/guestbook-ui -n dev 8081:80
```

Use `-n staging` or `-n prod` with the matching folder for other environments.
