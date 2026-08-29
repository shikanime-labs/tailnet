<!-- owner: shikanime | zone: internal | purpose: tailnet policy architecture and apply flow -->

# Architecture

`tailnet` is the GitOps source of truth for the shikanime Tailscale policy
file (`taila659a.ts.net`). `policy.hujson` is the only artifact that reaches
control; everything else is tooling.

## Policy file

`policy.hujson` is HuJSON (JSON with comments and trailing commas) with four
top-level sections:

- `tagOwners` — who may apply each tag to a node
- `acls` — allow/deny rules between tags, users, and ports
- `ssh` — Tailscale SSH authorization rules
- `tests` — policy `tests` exercised by CI on every pull request

## Apply flow

1. Pull request: `tailscale/gitops-acl-action` runs the policy `tests`
   against the live tailnet control plane. Nothing is written.
2. Push to `main`: the same action applies `policy.hujson`, **overwriting**
   the live ACL unconditionally.

Auth uses GitHub workload identity federation (`id-token: write` plus
repository variables) — no long-lived OAuth secret or API key is stored.

## Tooling

- `flake.nix` — devenv shell and the generated `Tailnet` workflow.
- `.github/workflows/` — generated from `flake.nix` on shell entry; never
  edit by hand.
