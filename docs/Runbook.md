<!-- owner: shikanime | zone: internal | purpose: bootstrap, apply, and rollback -->

# Runbook

## Bootstrap (one-time)

1. Export the current policy from the admin console over `policy.hujson`.
   `apply` overwrites control unconditionally, so ship only a reconciled
   file.
2. Create a federated identity with the `policy_file` scope and bind it to
   this repository's issuer.
3. Set the repository variables:

   | Variable                    | Value                        |
   | --------------------------- | ---------------------------- |
   | `TAILSCALE_TAILNET`         | tailnet name from console    |
   | `TAILSCALE_OAUTH_CLIENT_ID` | federated identity client ID |
   | `TAILSCALE_AUDIENCE`        | federated identity audience  |

## Apply

Merge a pull request to `main`. The action applies `policy.hujson` to
control automatically — no manual step.

## Rollback

The live ACL is the source of truth after a bad apply. Re-export the last
known-good policy, commit it, and merge. Do not hand-edit in the console and
expect CI to stay in sync.
