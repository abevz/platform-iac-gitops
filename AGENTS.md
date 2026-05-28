# platform-iac-gitops - AI Agent Guide

This file provides repository guidance to AI coding agents working in
`~/github/platform-iac-gitops`.

## Instruction Files

- `AGENTS.md` is tracked in git and lives on `main` so new worktrees inherit it.
- `CLAUDE.md` should point to `AGENTS.md`.

## Git Workflow

- This repository uses a bare repo plus git worktrees.
- Bare repo path: `~/github/platform-iac-gitops/.bare`
- Main worktree path: `~/github/platform-iac-gitops/main`
- Treat `main` as read, update, and status only.
- Keep repository-wide instruction files in `main` so every new worktree starts
  with the same baseline.
- Make changes on feature branches in sibling worktrees under
  `~/github/platform-iac-gitops/`.
- One task means one branch, one sibling worktree, and one merge request.
- Create new worktrees from `main`, then do task changes inside that sibling
  worktree.
- Do not push directly to `main`.
- Remove completed worktrees with `git worktree remove`, not raw directory
  deletion.

## Attribution Rules

- Do not add AI assistant names, agent names, or bot identities to commit
  authors, committers, co-author trailers, commit messages, merge request
  descriptions, or repository documentation.
- Do not add `Co-authored-by`, `Generated-by`, `Assisted-by`, or similar
  attribution lines for AI tools.

## Project Context

- This repo is the GitOps source of truth for the `k8s-lab-01` cluster apps in
  this repo family.
- It manages Argo CD Applications plus the manifests for `democicd`,
  External Secrets, Kyverno, and related platform components.
- Prefer small, targeted manifest changes; avoid broad rewrites of generated or
  vendor-like YAML.

## Validation

```bash
kubectl apply --dry-run=client -f k8s-lab-01/apps/
kubectl apply --dry-run=client -f k8s-lab-01/democicd/
kubectl apply --dry-run=client -f k8s-lab-01/external-secrets-config/
kubectl apply --dry-run=client -f k8s-lab-01/kyverno-policies/
```

## Manifest Rules

- Keep `Application.spec.source.repoURL`, `path`, and destination namespace
  intentional and consistent with the real target app.
- Keep sync-wave, finalizer, and sync-option changes narrow and justified.
- Treat `k8s-lab-01/kyverno-crds/*.yaml` as imported/generated content: update
  them deliberately, not as drive-by formatting cleanup.
- Do not commit live tokens, passwords, or decrypted secret material.
- Keep registry secret names, namespaces, and image references aligned across
  `democicd`, External Secrets, and Kyverno policy manifests.

## Operational Guidance

- When a change affects the application deployment contract, coordinate it with
  `~/github/democicd/main` in the same task.
- When debugging Argo CD drift, verify the specific child application manifest
  before changing shared project-wide settings.
- Preserve private-environment intent; do not replace private repo URLs,
  registry hosts, or secret references with fake placeholders unless the task
  explicitly requires sanitization.

## Commit Guidance

- Use Conventional Commits such as `feat:`, `fix:`, `docs:`, `refactor:`,
  `test:`, and `chore:`.
- Keep messages in English and focus on what changed and why.
