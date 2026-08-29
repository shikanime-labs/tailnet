<!-- owner: shikanime | zone: internal | purpose: local setup and how to change policy -->

# Development

## Prerequisites

- `direnv` (or run `nix develop` manually) to enter the devenv shell.
- Push access to this repository and the Tailscale federated identity.

## Local loop

1. `direnv allow` (or `nix develop`) to load the shell.
2. Edit `policy.hujson`. Add comments and trailing commas freely — HuJSON
   permits them.
3. `nix fmt` before shipping to keep generated files and docs tidy.
4. Open a pull request; CI runs the policy `tests` against control.

## How to add

- A new access rule: add an entry under `acls` and a matching `tests` case
  so CI proves it.
- A new tag: declare it under `tagOwners`, then tag nodes where they are
  provisioned (`machines`, `manifests`), not here.
- Workflow changes: edit `flake.nix`; the workflow file is regenerated on
  shell entry. Never hand-edit `.github/workflows/`.
