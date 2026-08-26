# Tailnet

GitOps source of truth for the `taila659a.ts.net` tailnet policy file.

`policy.hujson` is the tailnet ACL. The `Tailnet` workflow tests it on pull
requests and applies it to Tailscale on push to `main`, using
[`tailscale/gitops-acl-action`](https://github.com/tailscale/gitops-acl-action)
with workload identity federation — no long-lived API key or OAuth secret is
stored in GitHub.

## Bootstrap

1. Export the current policy from
   [the admin console](https://login.tailscale.com/admin/acls/file) over
   `policy.hujson`. `apply` overwrites control unconditionally, so shipping an
   unverified file replaces the live ACL.
2. Create a federated identity with the `policy_file` scope in
   [keys settings](https://login.tailscale.com/admin/settings/keys) and bind it
   to this repository's issuer.
3. Set the repository variables:

   | Variable                    | Value                               |
   | --------------------------- | ----------------------------------- |
   | `TAILSCALE_TAILNET`         | tailnet name from the admin console |
   | `TAILSCALE_OAUTH_CLIENT_ID` | federated identity client ID        |
   | `TAILSCALE_AUDIENCE`        | federated identity audience         |

## Development

`direnv allow` (or `nix develop`) enters the devenv shell. Workflows and
`.gitignore` are generated from `flake.nix` on shell entry — edit the flake, not
the generated files. Run `nix fmt` before shipping.

## Structure

- `policy.hujson` — tailnet ACL, tag owners, SSH rules, and policy tests
- `flake.nix` — devenv shell and the generated `Tailnet` workflow
