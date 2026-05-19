# DumbPad Helm Chart

A Helm chart for deploying [DumbPad](https://github.com/dumbwareio/dumbpad) — a minimal, self-hosted notepad app — on Kubernetes.

---

## 📁 Chart Structure

```
dumbpad/
├── charts/
├── Chart.yaml
├── templates/
│   ├── deployment.yaml
│   ├── ingress.yaml
│   └── service.yaml
└── values.yaml
```

---

## ✅ Prerequisites

- Kubernetes cluster (v1.21+)
- [Helm](https://helm.sh/docs/intro/install/) v3+
- `kubectl` configured and connected to your cluster
- NGINX Ingress Controller installed
- Namespace `dumbpad-ns` created (or use `--create-namespace`)

---

## 🚀 Getting Started

### 1. Create the Helm Chart (from scratch)

```bash
helm create dumbpad
```

Clean up the default templates and create only what's needed:

```bash
rm -rf dumbpad/templates/*
touch dumbpad/templates/deployment.yaml
touch dumbpad/templates/service.yaml
touch dumbpad/templates/ingress.yaml
```

---

## ⚙️ Default Values

Key defaults from `values.yaml`:

| Parameter | Default Value |
|---|---|
| `image.repository` | `dumbwareio/dumbpad` |
| `image.tag` | `latest` |
| `replicaCount` | `1` |
| `namespace` | `dumbpad-ns` |
| `env.siteTitle` | `DumbPad` |
| `env.baseUrl` | `http://dumbpad.devopsindia.dev` |
| `service.type` | `ClusterIP` |
| `service.port` | `3000` |
| `ingress.enabled` | `true` |
| `ingress.host` | `dumbpad.devopsindia.dev` |
| `ingress.className` | `nginx` |
| `persistence.hostPath` | `/mnt/ssd/kubernetes/dumbpad` |
| `persistence.mountPath` | `/app/data` |
| `resources.requests.cpu` | `100m` |
| `resources.requests.memory` | `100Mi` |
| `resources.limits.cpu` | `100m` |
| `resources.limits.memory` | `100Mi` |

---

## 📦 Helm Commands

### Install

Install the chart into the `dumbpad-ns` namespace (creates namespace if it doesn't exist):

```bash
helm install dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --create-namespace
```

Install with a custom values file:

```bash
helm install dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --create-namespace \
  -f custom-values.yaml
```

Install overriding specific values inline:

```bash
helm install dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --create-namespace \
  --set ingress.host=mynotepad.example.com \
  --set env.baseUrl=http://mynotepad.example.com
```

---

### Upgrade

Upgrade the release after making changes to chart files or values:

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns
```

Upgrade with a custom values file:

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  -f custom-values.yaml
```

Upgrade and override specific values inline:

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --set replicaCount=2
```

Install if not present, upgrade if already installed:

```bash
helm upgrade --install dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --create-namespace
```

---

### Uninstall

Remove the release (does not delete the namespace):

```bash
helm uninstall dumbpad --namespace dumbpad-ns
```

Remove the release and delete the namespace:

```bash
helm uninstall dumbpad --namespace dumbpad-ns
kubectl delete namespace dumbpad-ns
```

---

### Template / Dry Run

Render templates locally without deploying (for debugging):

```bash
helm template dumbpad ./dumbpad
```

Render and save output to a file:

```bash
helm template dumbpad ./dumbpad > rendered-manifests.yaml
```

Dry run against the cluster (server-side validation):

```bash
helm install dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --create-namespace \
  --dry-run
```

---

### Status & Inspection

Check the release status:

```bash
helm status dumbpad --namespace dumbpad-ns
```

List all Helm releases in the namespace:

```bash
helm list --namespace dumbpad-ns
```

List all Helm releases across all namespaces:

```bash
helm list --all-namespaces
```

Show the computed values for the deployed release:

```bash
helm get values dumbpad --namespace dumbpad-ns
```

Show all values (including defaults):

```bash
helm get values dumbpad --namespace dumbpad-ns --all
```

Show the rendered manifests of the deployed release:

```bash
helm get manifest dumbpad --namespace dumbpad-ns
```

---

### Rollback

View release history:

```bash
helm history dumbpad --namespace dumbpad-ns
```

Rollback to the previous release:

```bash
helm rollback dumbpad --namespace dumbpad-ns
```

Rollback to a specific revision number:

```bash
helm rollback dumbpad 1 --namespace dumbpad-ns
```

---

### Lint & Validate

Lint the chart for errors:

```bash
helm lint ./dumbpad
```

Lint with a custom values file:

```bash
helm lint ./dumbpad -f custom-values.yaml
```

---

### Package

Package the chart into a `.tgz` archive (for distribution):

```bash
helm package ./dumbpad
```

Install directly from the packaged archive:

```bash
helm install dumbpad dumbpad-0.1.0.tgz \
  --namespace dumbpad-ns \
  --create-namespace
```

---

## 🔧 Common Customizations

### Change the hostname

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --set ingress.host=notes.example.com \
  --set env.baseUrl=http://notes.example.com
```

### Change the data storage path

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --set persistence.hostPath=/data/dumbpad
```

### Scale replicas

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --set replicaCount=3
```

### Disable Ingress

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --set ingress.enabled=false
```

### Change resource limits

```bash
helm upgrade dumbpad ./dumbpad \
  --namespace dumbpad-ns \
  --set resources.limits.cpu=200m \
  --set resources.limits.memory=256Mi
```

---

## 🩺 Verify Deployment

```bash
# Check pods are running
kubectl get pods -n dumbpad-ns

# Check service
kubectl get svc -n dumbpad-ns

# Check ingress
kubectl get ingress -n dumbpad-ns

# View pod logs
kubectl logs -l app=dumbpad -n dumbpad-ns

# Describe the deployment
kubectl describe deployment dumbpad-dep -n dumbpad-ns
```

---

## 📝 Notes

- **Persistence** uses a `hostPath` volume, so data is stored directly on the node at `/mnt/ssd/kubernetes/dumbpad`. Make sure this path exists on the node before deploying.
- **Ingress** assumes NGINX Ingress Controller is installed. SSL is disabled by default.
- **Image tag** defaults to `latest`. For production, pin to a specific version (e.g., `--set image.tag=1.2.3`).
- The chart version is tracked in `Chart.yaml` under `version`. Bump it with each change.