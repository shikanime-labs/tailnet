<!-- owner: shikanime | zone: internal | purpose: known failure modes and fixes -->

# Troubleshooting

## Apply replaced the live ACL unexpectedly

`apply` is an unconditional overwrite. If the merged `policy.hujson` was not
reconciled against the admin console export, control drifted. Re-export the
console policy, diff it against the file, and merge the corrected version.

## CI warns but does not fail

A manual edit in the admin console makes the apply run warn, not fail. The
file and control are now out of sync. Re-export before editing the file so
the next apply is a clean overwrite.

## New tag has no effect

Adding a `tagOwners` entry does not tag any node. Nodes are tagged where they
are provisioned (`machines`, `manifests`). Confirm the node carries the tag
there.

## Workflow does not run

Check the repository `id-token: write` permission and the three
`TAILSCALE_*` variables. Federation fails silently when the audience or
client ID is wrong, so the apply step never authenticates.
