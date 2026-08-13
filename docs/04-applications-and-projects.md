# Step 4 — Applications & Projects

ArgoCD uses two core CRDs to manage GitOps deployments:

- **AppProject** — defines boundaries (which repos, clusters, namespaces are allowed)
- **Application** — maps a Git path to a cluster namespace

## Register the Git Repository

Connect the lab repository to ArgoCD:

```bash
argocd repo add https://github.com/DPP-2026/Argocd.git
```

For private repos, add credentials:

```bash
argocd repo add https://github.com/DPP-2026/Argocd.git \
  --username <github-user> \
  --password <github-pat>
```

The Application manifests already point to this repo:

```yaml
sourceRepos:
  - https://github.com/DPP-2026/Argocd.git
```

## Create AppProject

```bash
kubectl apply -f applications/project.yaml
```

### AppProject Features Explained

| Field | Purpose | Our Lab Value |
|-------|---------|---------------|
| `sourceRepos` | Whitelist of allowed Git repos | Only this lab repo |
| `destinations` | Allowed cluster + namespace pairs | `dev`, `staging`, `prod` namespaces |
| `clusterResourceWhitelist` | Cluster-scoped resources allowed | `Namespace`, `Rollout` |

View in UI: **Settings → Projects → guestbook**

## Create Guestbook Applications (dev, staging, prod)

Three Applications deploy the same app to different namespaces on the same cluster:

| Application | Manifest path | Namespace | Replicas | Image |
|-------------|---------------|-----------|----------|-------|
| `guestbook-dev` | `manifests/guestbook/dev` | `dev` | 1 | `gb-frontend:v5` |
| `guestbook-staging` | `manifests/guestbook/staging` | `staging` | 2 | `gb-frontend:v5` |
| `guestbook-prod` | `manifests/guestbook/prod` | `prod` | 3 | `gb-frontend:v5` |

> **Image note:** Google Samples currently publishes only `gb-frontend:v5`. Tags v1–v4 and v6 are not available. See [sync guide](05-sync-and-image-updates.md#guestbook-image-availability).

```bash
kubectl apply -f applications/guestbook-dev.yaml
kubectl apply -f applications/guestbook-staging.yaml
kubectl apply -f applications/guestbook-prod.yaml
```

### Application Spec Explained

| Field | Purpose | Example (dev) |
|-------|---------|---------------|
| `spec.project` | Links to AppProject | `guestbook` |
| `spec.source.repoURL` | Git repository | `https://github.com/DPP-2026/Argocd.git` |
| `spec.source.path` | Directory with manifests | `manifests/guestbook/dev` |
| `spec.source.targetRevision` | Branch, tag, or commit | `main` |
| `spec.destination.server` | Target cluster API | `https://kubernetes.default.svc` |
| `spec.destination.namespace` | Target namespace | `dev` |
| `spec.syncPolicy.automated` | Auto-sync on Git changes | Enabled with prune + selfHeal |
| `syncOptions.CreateNamespace` | Create namespace if missing | `true` |

## First Sync

**Via UI:** Open each app (`guestbook-dev`, `guestbook-staging`, `guestbook-prod`) → **Sync** → **Synchronize**

**Via CLI:**

```bash
argocd app sync guestbook-dev
argocd app sync guestbook-staging
argocd app sync guestbook-prod
argocd app list
```

Verify workloads:

```bash
kubectl get deploy,svc,pods -n dev -l app=guestbook-ui
kubectl get deploy,svc,pods -n staging -l app=guestbook-ui
kubectl get deploy,svc,pods -n prod -l app=guestbook-ui
```

## Access Guestbook

```bash
kubectl port-forward svc/guestbook-ui -n dev 8081:80
kubectl port-forward svc/guestbook-ui -n staging 8082:80
kubectl port-forward svc/guestbook-ui -n prod 8083:80
```

## Application Status Indicators

| Status | Meaning |
|--------|---------|
| **Synced** | Cluster matches Git at `targetRevision` |
| **OutOfSync** | Git has changes not yet applied |
| **Progressing** | Resources are being created/updated |
| **Healthy** | All resources pass health checks |
| **Degraded** | One or more resources are unhealthy |

## Sync Policies

| Policy | Behavior |
|--------|----------|
| Manual sync | You trigger sync from UI/CLI |
| `automated` | ArgoCD syncs when Git changes |
| `prune: true` | Removes resources deleted from Git |
| `selfHeal: true` | Reverts manual `kubectl edit` changes |

Our guestbook apps use automated sync with prune and self-heal. Try editing the dev deployment manually:

```bash
kubectl scale deployment guestbook-ui -n dev --replicas=5
```

Within ~3 minutes, ArgoCD reverts replicas back to `1` (the Git value for dev).

## Deploy Guestbook Rollout (Optional Second App)

After completing the basic guestbook lab, deploy the rollout version:

```bash
kubectl apply -f applications/guestbook-rollout.yaml
argocd app sync guestbook-rollout
kubectl argo rollouts get rollout guestbook-ui -n default
```

## Next Step

→ [Sync & Image Updates](05-sync-and-image-updates.md)
