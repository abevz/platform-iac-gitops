# vector-reranker

Query-time cross-encoder for the vault vector search, built from
`utils/vector-indexer/reranker-service` and pushed to
`harbor.example.com/library/vector-reranker`. Runs on the GPU node
(`runtimeClassName: nvidia`, one time-sliced `nvidia.com/gpu`), reachable at
`http://192.0.2.211:8089` (Cilium LB-IPAM).

## Secret

`RERANKER_API_TOKEN` is not in git. The `vector-reranker` Secret in namespace
`vector` was created directly from the token of the former host service:

```bash
kubectl -n vector create secret generic vector-reranker --from-literal=RERANKER_API_TOKEN=<token>
```

To move it to External Secrets, write the token to Vault
(`vault kv put secret/vector/reranker token=<token>`), allow the path in the
`eso-sandbox` policy and add an ExternalSecret that targets the same Secret name.

## Model cache

`/var/lib/models/vector-reranker` on the GPU node (hostPath, Hugging Face cache,
2.6 GB). On a rebuilt node the model is downloaded again on first start.

## Rollback

Point `reranker.url` in `vector-indexer/config.yaml` back to
`http://192.0.2.101:8089` and `systemctl enable --now vector-reranker` on the
Proxmox host (CPU drop-in `10-cpu.conf` is still in place there).
