<!-- owner: shikanime | zone: internal | purpose: policy fields and repository variables -->

# Reference

## `policy.hujson` sections

- `tagOwners` — map of tag → list of owners allowed to apply it
- `acls` — list of `{action, src, dst}` rules
- `ssh` — list of Tailscale SSH auth rules
- `tests` — list of `{src, dst, accept}` assertions run by CI

## Repository variables

| Variable                    | Meaning                             |
| --------------------------- | ----------------------------------- |
| `TAILSCALE_TAILNET`         | tailnet name from the admin console |
| `TAILSCALE_OAUTH_CLIENT_ID` | federated identity client ID        |
| `TAILSCALE_AUDIENCE`        | federated identity audience         |

## CI action

- `tailscale/gitops-acl-action` — runs `tests` on PR, applies on push to
  `main`. Authenticated via GitHub workload identity federation.

## Branch protection

- 1 approving review, linear history, signed commits, squash+rebase only.
- Run `nix flake check` and `nix fmt` before submitting.
