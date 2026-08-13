# Step 2 — Install ArgoCD

## Install

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait until all pods are running:

```bash
kubectl get pods -n argocd
```

## Access the UI

**Option A — Port forward (local lab):**

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open https://localhost:8080 (accept the self-signed certificate warning).

**Option B — LoadBalancer (shared demo):**

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc argocd-server -n argocd
```

Use the `EXTERNAL-IP` or hostname from the output.

## Login Credentials

Default username: `admin`

Retrieve the initial password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d; echo
```

## Install ArgoCD CLI (optional)

**macOS:**

```bash
brew install argocd
```

**Linux:**

```bash
VERSION=$(curl -s https://api.github.com/repos/argoproj/argo-cd/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -sSL -o argocd "https://github.com/argoproj/argo-cd/releases/download/${VERSION}/argocd-linux-amd64"
chmod +x argocd && sudo mv argocd /usr/local/bin/
```

Login via CLI:

```bash
argocd login localhost:8080 --username admin --password <password> --insecure
```

## ArgoCD Architecture (Key Components)

| Component | Role |
|-----------|------|
| **Application Controller** | Compares live cluster state vs Git desired state; runs reconciliation loop |
| **API Server** | Powers the UI and CLI |
| **Repo Server** | Clones Git repos and renders manifests (plain YAML, Helm, Kustomize) |
| **Redis** | Caches rendered manifests |

## Next Step

→ [Install Argo Rollouts](03-install-argo-rollouts.md)
