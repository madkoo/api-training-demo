
###  Create the Reusable Workflow

This is an optional step. If added then the previous worklow needs to be modified as well.

- Create a new file in **`.github/workflows/`** named **`comment_in_issue_workflow.yml`**.
- Add the following content:

<details>
    <summary>Solution</Summary>
    
```yaml


```yaml
name: Write comment to issue

on:
  workflow_call:
    inputs:
      issue_comment:
        required: true
        type: string
      issue_number:
        required: true
        type: number
    secrets:
      temp-token:
        required: true

jobs:
  create-comment:
    runs-on: ubuntu-latest
    steps:
    - name: Add comment to issue
      uses: actions/github-script@v6
        with:
            github-token: ${{ inputs.temp-token }}
            script: |
              const issueComment = context.payload.issue.number;
              github.rest.issues.createComment({
                issue_number: ${{inputs.issue_number}},
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: ${{ inputs.issue_comment }}
              });

```
</details>

###  Update the Workflow

- Update the **`issue_workflow.yml`** file to use the new reusable workflow.
- Add the following content:

<details>
    <summary>Solution</Summary>
    
```yaml

...
name: Issue Triggered Workflow
