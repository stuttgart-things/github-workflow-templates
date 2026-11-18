---
name: semver-start-v0
description: Make a new service/repo with semantic-release start at v0.1.0 instead of v1.0.0. Use when bootstrapping a new repo that releases via semantic-release (stuttgart-things Go services like claims/pvectl, .releaserc.json, Release workflow), before its first release PR is merged, or when a new repo was already released as v1.0.0 by mistake.
---

# Start semantic-release at v0.1.0

semantic-release releases **1.0.0** when the repo has no version tag. New
stuttgart-things services start at **0.1.0**. The fix is a seed tag
`v0.0.0` on main *before* the first release. With it, `feat:` gives 0.1.0
and `fix:` gives 0.0.1.

## Before the first release (new repo)

Do this as soon as main has its first commit, and before merging the first
`feat:` PR. The Release workflow runs on main when that PR is merged.

1. Check that there is no release yet and read the tag format:
   ```bash
   gh release list -R <owner>/<repo> -L 3      # must be empty
   git ls-remote --tags origin                  # no v* tags
   jq -r .tagFormat .releaserc.json             # usually v${version}
   ```
   If a release already exists, go to "Already released as v1.0.0".
2. Tag the first commit on main. A lightweight tag is fine, and you don't
   need a GitHub release:
   ```bash
   git fetch origin main
   git tag v0.0.0 "$(git rev-list --max-parents=0 origin/main | tail -1)"
   git push origin v0.0.0
   ```
   The tag has to match `tagFormat` and be an ancestor of main. Pushing a tag
   is outward-facing, so the user has to OK it.
3. Give the first release PR a `feat:` title, so the release is 0.1.0. A
   `fix:` gives 0.0.1. Avoid a `!` or a `BREAKING CHANGE:` footer, because
   either one jumps to 1.0.0.
4. After the merge, check the result:
   `gh release list -R <owner>/<repo> -L 1` should show `v0.1.0`.

Say in the PR body or the handover that the repo is seeded with v0.0.0, so
nobody deletes the tag.

## Already released as v1.0.0

Only with the user's explicit OK. This deletes a published release and
rewrites what consumers see. Do it right after the release, before anyone
uses it.

1. Check who could have consumed it: the binary download, the container
   image (`ghcr.io/<owner>/<repo>:v1.0.0`) and, for Go, the module proxy.
   **Go caveat:** once proxy.golang.org has fetched v1.0.0, it keeps it
   forever, and `go install …@latest` keeps picking v1.0.0 over any 0.x.
   Retracting it would need a v1.0.1 release. If the proxy may have fetched
   it (someone ran `go install`/`go get` with `@latest` or `@v1.0.0`), tell
   the user and let them decide whether to stay on 1.x.
2. Delete the release and the tag:
   ```bash
   gh release delete v1.0.0 -R <owner>/<repo> --cleanup-tag --yes
   ```
   Optionally also delete the `v1.0.0` image tag in GHCR. That needs the
   package API or the web UI.
3. Seed `v0.0.0` on the first commit as in step 2 above.
4. Open a PR that removes the `1.0.0` section from `CHANGELOG.md`, with a
   `feat:` title, or `fix:` if a patch is enough. Once it is merged,
   semantic-release looks at every commit since v0.0.0 and releases v0.1.0.
   The `chore(release): 1.0.0` commit stays in the history, which is
   harmless.
5. Check that `gh release list` shows v0.1.0 as Latest. Point anything that
   pinned v1.0.0 (README install lines, Renovate, Flux) to v0.1.0.

## Lasting fix

If the repo comes from a template or a bootstrap script (for example the
Go repo layout copied from claims), put the `v0.0.0` seed step into that
bootstrap, so new repos never hit this again.
