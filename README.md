# stuttgart-things/github-workflow-templates

## REUSABLE WORKFLOW-CALLS

<details><summary>WORFKLOW-FOLDER</summary>

```bash
mkdir -p .github/workflows
```

</details>

<details><summary>TRIGGER</summary>

```yaml
on:
  workflow_dispatch:
  push:
    branches:
      - 'main'
      - 'feature/**'
      - 'fix/**'
  pull_request:
    types: [opened, reopened]
```

</details>


<details><summary>GOLANG BUILD</summary>

```yaml
jobs:
  validate-golang:
    name: Valdiate Golang
    uses: stuttgart-things/github-workflow-templates/.github/workflows/call-golang-validation.yaml@main
    with:
      module-name: kaeffken
      environment-name: k8s
      runs-on: ghr-kaeffken-skyami-cicd
      golint-version: v1.61.0-alpine
      golang-version: "1.23.1"
      accept-linterrors: true
      accept-failedtests: false
    secrets: inherit
```

</details>

<details><summary>KO BUILD (ttl.sh / ghcr.io)</summary>

```yaml
jobs:
  build-image:
    name: Build & Push Image
    uses: stuttgart-things/github-workflow-templates/.github/workflows/call-ko-build.yaml@main
    with:
      registry: ghcr.io
      image-repo: ${{ github.repository }}
      tags: pr-${{ github.event.number }}-${{ github.event.pull_request.head.sha }}
      push: true
      build-path: ./cmd/myapp
    secrets: inherit
```

Defaults to `registry: ttl.sh` with a random repo + `tag=latest` for throwaway smoke runs.
When `registry: ghcr.io` the workflow uses `GITHUB_TOKEN` to log in (caller must declare
`packages: write`). The first tag is exposed as the `image-ref` workflow output for
downstream scan/deploy/PR-comment jobs.

</details>

<details><summary>PUSH KUSTOMIZE OCI BASE</summary>

```yaml
jobs:
  push-kustomize:
    name: Push Kustomize OCI Base
    uses: stuttgart-things/github-workflow-templates/.github/workflows/call-push-kustomize.yaml@main
    with:
      kcl-source-dir: kcl
      kcl-profile-file: tests/kcl-deploy-profile.yaml
      kustomize-oci-repo: ghcr.io/${{ github.repository }}-kustomize
      tag: pr-${{ github.event.number }}-${{ github.event.pull_request.head.sha }}
    secrets: inherit
```

Wraps `dagger call -m <kcl-module> push-kustomize-base` for callers that need a
PR-coordinated kustomize artifact on every push (the release-only path lives in
`call-go-release.yaml`). The pushed `address:tag` is exposed as the `artifact-ref`
workflow output.

</details>

<details><summary>CLEANUP PR-TAGGED GHCR ARTIFACTS</summary>

```yaml
on:
  pull_request:
    types: [closed]

jobs:
  cleanup:
    name: Delete PR-tagged GHCR versions
    uses: stuttgart-things/github-workflow-templates/.github/workflows/call-cleanup-pr-artifacts.yaml@main
    with:
      packages: |
        myrepo
        myrepo-kustomize
    secrets: inherit
```

Defaults to deleting every GHCR version whose tag starts with `pr-<num>-` for the
closed PR. Set `dry-run: true` first to preview matches. Override `tag-pattern`
for custom tagging schemes. GHCR retains PR-tagged versions forever otherwise,
so wire this up alongside any workflow that pushes `pr-<num>-<sha>` artifacts.

</details>

<details><summary>YAML LINT</summary>

```yaml
jobs:
  yaml-lint:
    name: Lint yaml files
    uses: stuttgart-things/github-workflow-templates/.github/workflows/call-yaml-lint.yaml@feature/add-homerun-task-go
    with:
      runs-on: ghr-install-configure-docker-skyami-cicd
      environment-name: k8s
      continue-error: true
      yamllint-version: 1
      lintprofile-path: .yamllint
      artifact-name: yaml-lint
```

</details>

## ACTIONS

<details><summary>SEND MESSAGE TO HOMERUN</summary>

```yaml
jobs:
  send-to-homerun:
    runs-on: ghr-stuttgart-things-skyami-cicd
    steps:
      - name: Send Message To Homerun
        uses: stuttgart-things/github-workflow-templates/actions/send-homerun-message@main
        with:
          url: "https://homerun.homerun-dev.sthings-vsphere.labul.sva.de/generic"
          secretToken: "${{ secrets.HOMERUN_TOKEN }}" # Pass the secret
          title: "Test Homerun Title"
          message: "Test Homerun Message"
          severity: "INFO"
          artifacts: "Test Artifact"
          tags: "github,test"
          assigneeName: "patrick"
          assigneeAddress: ""
```

</details>


## DEV

<details><summary>ALL TASKS</summary>

```bash
task: Available tasks for this project:
* branch:              Create branch from main
* check:               Run pre-commit hooks
* commit:              Commit + push code into branch
* pr:                  Create pull request into main
* switch-local:        Switch to local branch
* switch-remote:       Switch to remote branch
* tag:                 Tag repo
* tasks:               Select a task to run
```

</details>

<details><summary>SELECT TASK</summary>

```bash
task=$(yq e '.tasks | keys' Taskfile.yaml | sed 's/^- //' | gum choose) && task ${task}
```

</details>

## AUTHORS

```yaml
---
authors:
  - patrick.hermann
```


## LICENSE

<details><summary><b>APACHE 2.0</b></summary>

Copyright 2023 patrick hermann.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

</details>
