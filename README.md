# GitOps Namespace Factory

A fully automated, GitOps-driven platform for provisioning Kubernetes tenant namespaces with baseline governance (Quotas, Limits, RBAC).

## 🏗 Architecture
This project utilizes the **App of Apps (Bootstrap)** pattern to ensure the entire platform—including the automation logic itself—is managed by GitOps.

1.  **Bootstrap App:** Manages the *ApplicationSet*.
2.  **ApplicationSet:** Watches the `tenants/` folder and generates an *Application* for each file.
3.  **Tenant Application:** Deploys the *Namespace Factory* Helm Chart (Namespace, Quota, LimitRange, RoleBindings).

## 🚀 Key Features
* **Zero-Touch Provisioning:** No manual `kubectl` commands required after the initial bootstrap.
* **Strict Governance:** Enforces ResourceQuotas and LimitRanges automatically.
* **Security First:** Uses `RoleBindings` to map external Groups to Namespace-scoped permissions (no unnecessary ClusterAdmin access).
* **Predictable Ordering:** ArgoCD automatically applies built-in Kubernetes oredering

## 🛠 Prerequisites
* Kubernetes Cluster
* Argo CD installed

## ⚡️ Quick Start

### 1. Bootstrap the Platform
Apply the bootstrap manifest. This is the **only** manual command you will ever run.

```bash
kubectl apply -f bootstrap.yaml
```

*This creates the `bootstrap-factory` App in Argo CD, which immediately syncs the `namespace-factory` ApplicationSet.*

### 2. Onboard a New Tenant
To provision a new environment, simply create a file in `tenants/` (e.g., `team-b.yaml`) and push to Git:

```yaml
namespace: team-b-dev
owner: team-b
environment: dev
quota:
  cpuRequests: '4'
  cpuLimits: '8'
  memoryRequests: 8Gi
  memoryLimits: 16Gi
rbac:
  adminGroup: team-b-admins
  viewGroup: team-b-viewers
```

Argo CD will automatically detect the file and provision the isolated namespace with all security controls.