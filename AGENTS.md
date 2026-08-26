# Tailnet

GitOps source of truth for the shikanime tailnet policy file, applied to
Tailscale by CI.

**Language:** HuJSON (policy), Nix (tooling)

## Structure

- `policy.hujson` — tailnet ACL: `tagOwners`, `acls`, `ssh`, `tests`
- `flake.nix` — devenv shell and the generated `Tailnet` workflow
- `.github/workflows/` — generated from `flake.nix` on shell entry; never edit
  by hand

## Functionality

- Pull request: `gitops-acl-action` runs the policy `tests` against control
- Push to `main`: applies `policy.hujson`, overwriting the live ACL
- Auth is workload identity federation (`id-token: write` + repository
  variables), so no OAuth secret or API key lives in GitHub

## Pitfalls

- `apply` is unconditional overwrite. Never merge a `policy.hujson` that was not
  reconciled against the admin console export.
- A manual edit in the admin console makes the run warn, not fail. Re-export
  before touching the file.
- Adding a `tagOwners` entry does not tag any node; nodes are tagged where they
  are provisioned (`machines`, `manifests`).

## Commit Style

- Plain-text capitalized title, no conventional-commit prefix
- Body with labels: `Design:`, `Related:`, `Closes #`
- Keep Markdown lines wrapped at 80 columns and run `nix fmt` before shipping

## Protect `main`

- Require 1 approving review
- Require linear history (no merge commits)
- Require signed commits
- Squash+rebase merge only

_Licensed under Apache-2.0. Run `nix flake check` before submitting._
