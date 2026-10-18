# platform-iac-gitops

Argo CD Application and Kubernetes manifest examples for a lab platform.

## Public example scope

This repository is an allowlisted public snapshot. `PUBLIC-ALLOWLIST.txt` enumerates every published path. Runtime inventories, encrypted secrets, credentials, private keys, certificates, state, saved plans, internal operations reports and personal contact data are excluded.

Domains under example.com/example.internal and addresses in 192.0.2.0/24 and 198.51.100.0/24 are documentation values. Host and cluster names are generic. CIDR prefixes broader than /24 are reduced to documentation ranges; this is an illustrative configuration, not a ready-to-apply environment. Supply your own reviewed inventory, credentials, keys, backend and networking before deployment. Secret references name required runtime inputs; they do not contain secret values.

Dependency versions retain the reviewed source baseline and selected patch/minor updates. Major migrations are excluded. No deployment or cloud mutation was performed when validating this snapshot.

## Local validation

Parse all YAML and run `kubeconform -strict -summary -ignore-missing-schemas lab-cluster`. Render chart versions from Application declarations with their inline values using `helm lint` and `helm template`; custom-resource schemas may need additional local schema inputs. Do not synchronize this example directly to a live cluster.
