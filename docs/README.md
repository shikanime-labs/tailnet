<!-- owner: Automata | zone: technical | purpose: Canonical technical index for tailnet, the GitOps tailnet ACL repository -->
# Tailnet

GitOps source of truth for the `taila659a.ts.net` tailnet policy. `policy.hujson`
is the ACL &mdash; tag owners, grants, SSH rules, and policy tests. CI tests it on
pull requests and applies it to Tailscale on push to `main` via
`tailscale/gitops-acl-action` with workload identity federation; no long-lived
API key or OAuth secret is stored in GitHub.

## Policy file

`policy.hujson` holds `tagOwners`, `acls`, `ssh`, and `tests`. Each `src`/`dst`
array is a union (matches any member, not all). Tag ownership forms a DAG:
`autogroup:it-admin` owns the machine fleet and can impersonate
`system:masters`; `tag:machine` owns the k8s nodes/operators; `tag:k8s-operator`
owns the service fleet but cannot tag machines. `tag:web` marks the Funnel nodes
that are the only inbound-from-internet surface. The full line-cited boundary
map lives in the wiki's Network Boundaries page.

## Deployment flow

- Pull request &mdash; `gitops-acl-action` runs the policy `tests` against
  control; the job runs `test` only, never `apply`.
- Push to `main` (and `workflow_dispatch`) &mdash; `apply` overwrites the live
  ACL unconditionally.
- Authentication &mdash; workload identity federation with `id-token: write`
  and repository variables `TAILSCALE_TAILNET`, `TAILSCALE_OAUTH_CLIENT_ID`,
  and `TAILSCALE_AUDIENCE`.

An admin-console edit makes the next CI run warn but not fail, so always
re-export before editing.

## Bootstrap

1. Export the current policy from the admin console over `policy.hujson` &mdash;
   `apply` overwrites control unconditionally, so an unverified file replaces
   the live ACL.
2. Create a federated identity with the `policy_file` scope and bind it to this
   repository's issuer.
3. Set the three repository variables listed above.

Adding a `tagOwners` entry does not tag any node &mdash; nodes are tagged where
they are provisioned (`machines` and `manifests`).

## Development

`direnv allow` (or `nix develop`) enters the devenv shell. The `Tailnet`
workflow and `.gitignore` are generated from `flake.nix` on shell entry &mdash;
edit the flake, not the generated files. Run `nix fmt` before shipping. Commits
use plain-text capitalized titles; `main` requires one approving review,
linear history, signed commits, and squash+rebase.

## Repository layout

- `policy.hujson` &mdash; the tailnet ACL, tag owners, SSH rules, policy tests
- `flake.nix` / `flake.lock` &mdash; devenv shell and generated workflow
- `.github/workflows/tailnet.yaml` &mdash; generated `Tailnet` test/apply workflow
- `docs/` &mdash; this canonical index

## Canonical sources

- [Repository README](https://github.com/shikanime-labs/tailnet#readme)
- [Technical wiki](https://github.com/shikanime-labs/tailnet/wiki)
  ([Architecture](https://github.com/shikanime-labs/tailnet/wiki/Architecture),
  [Operations](https://github.com/shikanime-labs/tailnet/wiki/Operations),
  [Network Boundaries](https://github.com/shikanime-labs/tailnet/wiki/Network-Boundaries))

In-repo `docs/` is canonical for technical detail; the wiki is a rendered human
index that links here.
