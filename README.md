# platform-iac-gitops

GitOps source of truth for a self-hosted Kubernetes lab (`k8s-lab-01`),
deployed via ArgoCD.

## Architecture

- **Kubernetes** cluster (`k8s-lab-01`) is provisioned separately by
  [`platform-iac`](https://github.com/abevz/platform-iac) (OpenTofu + Ansible
  on Proxmox).
- **ArgoCD** app-of-apps: `k8s-lab-01/apps/*.yaml` defines each Application;
  each points to a directory in this repo with the actual manifests.
- **Networking:** Cilium (kube-proxy replacement, L2 announcement + LB
  IPAM), Istio ingress gateway with cert-manager TLS.
- **Secrets:** External Secrets Operator backed by HashiCorp Vault
  (`ClusterSecretStore` + per-app `ExternalSecret`) — no secret material is
  committed to this repo.
- **Policy:** Kyverno cluster policies — disallow `latest` tags, disallow
  privileged containers, require resource limits, require cosign-signed
  images.
- **CI/CD demo app:** `democicd` — used to exercise the full
  build → sign → deploy pipeline.

## Repo map

| Path | What it deploys |
|---|---|
| `k8s-lab-01/apps/` | ArgoCD `Application` manifests, one per component |
| `k8s-lab-01/cilium-lb-config/` | Cilium L2 announcement policy + LB IP pool |
| `k8s-lab-01/cilium-l2-rbac/` | RBAC for Cilium L2 announcements |
| `k8s-lab-01/coredns-config/` | CoreDNS ConfigMap overrides |
| `k8s-lab-01/democicd/` | Demo CI/CD target application |
| `k8s-lab-01/external-secrets-config/` | Vault `ClusterSecretStore` + `ExternalSecret` objects |
| `k8s-lab-01/istio-argocd-ingress/` | Istio Gateway/VirtualService + TLS cert for the ArgoCD UI |
| `k8s-lab-01/kyverno-crds/` | Kyverno CRDs (vendored) |
| `k8s-lab-01/kyverno-policies/` | Kyverno `ClusterPolicy` guardrails |
| `k8s-lab-01/prometheus-external-rbac/` | RBAC for external Prometheus scraping and Vault token creation |
| `k8s-lab-01/vault-bootstrap/` | Vault Kubernetes-auth bootstrap scripts |

## Lab vs. production

This is a personal home lab used to practice production-style GitOps
patterns (ArgoCD, External Secrets, policy-as-code, signed supply chain). It
runs on a single home cluster with no external users or SLAs.

## Related repos

- [`platform-iac`](https://github.com/abevz/platform-iac) — cluster
  provisioning (OpenTofu + Ansible on Proxmox).
- `democicd` — the CI/CD demo application deployed by this repo.
