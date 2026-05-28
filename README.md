# platform-iac-gitops

GitOps manifests for `k8s-lab-01` — a production-like Kubernetes homelab cluster.

This repository is the **GitOps layer**. The provisioning layer (OpenTofu + Ansible that creates the VMs and bootstraps the cluster) lives in [platform-iac](https://github.com/abevz/platform-iac).

---

## What's inside

### ArgoCD App-of-Apps

`k8s-lab-01/apps/` contains one ArgoCD `Application` manifest per component. The root app (`platform-project.yaml`) bootstraps all others — ArgoCD manages itself after the first `kubectl apply`.

### Components

| Directory | What it does |
|---|---|
| `apps/` | ArgoCD Application manifests (app-of-apps root) |
| `argocd-config/` | ArgoCD ConfigMap patches (e.g. custom health checks) |
| `cilium-lb-config/` | L2 announcement policy + LoadBalancer IP pool |
| `cilium-l2-rbac/` | RBAC for Cilium L2 announcements |
| `coredns-config/` | CoreDNS ConfigMap — forwards homelab domains to internal DNS |
| `external-secrets-config/` | ClusterSecretStore (Vault backend) + ExternalSecret resources |
| `istio-argocd-ingress/` | Istio Gateway + VirtualService + cert-manager Certificate for ArgoCD |
| `kyverno-crds/` | Kyverno CRD manifests (managed separately from the controller) |
| `kyverno-policies/` | Admission policies: no latest tag, no privileged containers, resource limits, signed images |
| `democicd/` | Demo app — Deployment + Service + Namespace, image deployed by digest via cosign-signed CI |
| `vault-bootstrap/` | One-time script to configure Vault Kubernetes auth for ESO |

### Key patterns

**External Secrets Operator + Vault** — `ClusterSecretStore` authenticates to Vault via Kubernetes ServiceAccount. `ExternalSecret` resources pull secrets (GitLab repo token, Harbor registry credentials) into Kubernetes Secrets without storing values in git.

**Kyverno supply chain policy** — `require-signed-images` blocks any pod whose image was not signed with cosign. The GitLab CI pipeline signs images after build; ArgoCD deploys by digest.

**Istio ingress** — TLS termination via cert-manager wildcard certificate (`*.k8s.example.com`), gateway + virtualservice pattern.

**democicd** — minimal Go HTTP server demonstrating the full supply chain: GitLab CI → build → cosign sign → push by digest → ArgoCD sync → Kyverno admission check.

---

## Repository layout

```
k8s-lab-01/
  apps/                     # ArgoCD Application manifests
  argocd-config/
  cilium-l2-rbac/
  cilium-lb-config/
  coredns-config/
  democicd/
  external-secrets-config/
  istio-argocd-ingress/
  kyverno-crds/
  kyverno-policies/
  vault-bootstrap/
```

---

## Related

- [platform-iac](https://github.com/abevz/platform-iac) — OpenTofu + Ansible provisioning (VMs, Kubernetes bootstrap, Vault, Harbor, monitoring)
