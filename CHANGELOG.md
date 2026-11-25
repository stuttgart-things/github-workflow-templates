# Changelog

Semver; what counts as major / minor / patch is in the README ("Versioning").
Consumers pin `@<sha> # vX.Y.Z`; Renovate proposes the bumps.

## Unreleased

- README: versioning rules, pinning and Renovate setup; usage examples
  pinned to `v1.0.0` instead of `@main`. CHANGELOG added. Docs only.

## v1.0.0 (2026-11-25)

First versioned release, equal to `main` at `01c3461`
(stuttgart-things/github-workflow-templates#235): the reusable workflows in
`.github/workflows/` and the composite actions in `actions/`
(`send-homerun-message`, `validate-artifacts`, `verify-kustomize-base`), as
they were. No behaviour change against `main`.
