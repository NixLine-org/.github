# NixLine-org Reusable Workflows

This repository provides **organization-wide reusable GitHub Actions workflows** used by all repositories in the **[NixLine-org](https://github.com/NixLine-org)** organization.

These workflows standardize CI, dependency updates, SBOM generation, and policy enforcement across projects.

---

## Usage

Reference any workflow from another repository in this org like so:

```yaml
uses: NixLine-org/.github/.github/workflows/nixline-ci.yml@main
```

## Available Workflows

| Workflow                               | Description                                                                 |
| -------------------------------------- | --------------------------------------------------------------------------- |
| **nixline-ci.yml**                     | Runs the core NixLine CI — materializes selected packs and runs org checks. |
| **nixline-sbom.yml**                   | Generates SBOMs via `syft` (CycloneDX/SPDX).                                |
| **nixline-flake-update.yml**           | Automates flake.lock updates with Determinate Systems’ updater.             |
| **nixline-dependabot-automerge.yml**   | Securely auto-merges Dependabot PRs (minor & security updates).             |
| **nixline-policy-flake-lock-only.yml** | Enforces that only `flake.lock` may change in certain PRs.                  |

---

## Pinned Versions

| Tool | Version |
|------|----------|
| Determinate Nix Action | [`v3.5.2`](https://github.com/DeterminateSystems/determinate-nix-action/releases/tag/v3.5.2) |
| Flake Lock Updater | [`v24`](https://github.com/DeterminateSystems/update-flake-lock/releases/tag/v24) |

---

## Example Consumer Repo

A demo consumer (`nixline-demo1`) uses the CI workflow in **ephemeral mode** for testing.  
See [NixLine-org/nixline-demo1](https://github.com/NixLine-org/nixline-demo1).

---

## Permissions

All workflows follow the principle of least privilege.  
They only grant write access to `contents` or `pull-requests` when necessary (e.g., Dependabot or flake updates).
