# Changelog

Semver; what counts as major / minor / patch is in the README ("Versioning").
Consumers pin `@<sha> # vX.Y.Z`; Renovate proposes the bumps.

## Unreleased

## v1.2.0 (2026-11-25)

- `call-repository-linting`: new step "Check dagger-module-version" (#234).
  It warns on `dagger-module-version` overrides older than v3. It also fails
  early, with a clear error, when `dagger-version` is older than the module's
  `engineVersion` (v3.12.x needs >= v0.21.9). That combination failed before
  as well, but only after the engine had started. The engine check is best
  effort and is skipped when the module's `dagger.json` cannot be read.

## v1.1.0 (2026-11-25)

Dependency bumps (Renovate), no input changes:

- `stuttgart-things/dagger` modules v0.141.1, `dagger/dagger` CLI v0.21.12
  (all Dagger-based workflows)
- `stuttgart-things/blueprints` v3.12.2 (argocd/flux bootstrap, vault
  issuer/k8s-auth, flux-destroy, repository-linting)
- `controlplaneio-fluxcd/flux-operator` v0.63.1 (`call-flux-destroy`),
  `ghcr.io/fluxcd/flux-cli` v2.10.1 (`call-flux-bootstrap`)
- `joshkeegan/zip` v3.24.4 (`call-release-module`),
  `gofrolist/molecule-action` v2.9.22 (`call-moluecule-test`)
- digests: `actions/setup-go`, `actions/setup-python`,
  `goreleaser/goreleaser-action`, `skitionek/notify-microsoft-teams`, and the
  internal `verify-kustomize-base` pin

Docs: README versioning rules (pinning, Renovate group, major/minor/patch,
dependency bumps), usage examples pinned instead of `@main`, this CHANGELOG.

## v1.0.0 (2026-11-25)

First versioned release, equal to `main` at `01c3461`
(stuttgart-things/github-workflow-templates#235): the reusable workflows in
`.github/workflows/` and the composite actions in `actions/`
(`send-homerun-message`, `validate-artifacts`, `verify-kustomize-base`), as
they were. No behaviour change against `main`.
