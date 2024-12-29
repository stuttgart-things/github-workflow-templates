# github-workflow-templates
github-workflow-templates

## REUSABLE WORKFLOW-CALLS

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

```bash
task: Available tasks for this project:
* branch:       Create branch from main
* check:        Run pre-commit hooks
* commit:       Commit + push code into branch
* pr:           Create pull request into main
* tag:          Tag repo
```
