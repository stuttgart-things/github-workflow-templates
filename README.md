# github-workflow-templates
github-workflow-templates

test

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
