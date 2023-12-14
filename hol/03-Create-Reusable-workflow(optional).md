
###  Create the Reusable Workflow

This is an optional step. If added then the previous worklow needs to be modified as well.

- Create a new file in **`.github/workflows/`** named **`comment_in_issue_workflow.yml`**.
- Add the following content:

<details>
    <summary>Solution</Summary>

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
- Move getting Github App token to the top of the workflow in a different job and output the token so that it can be used in the other jobs.
- Have different jobs to call out the reusabe workflow to start and finish the workflow.


<details>
    <summary>Solution</Summary>

```yaml

name: Issue Triggered Workflow

on:
  issues:
    types: [opened, edited]

jobs:
    get-token:
        runs-on: ubuntu-latest
        outputs:
            token: ${{ steps.get_workflow_token_target.outputs.token }}
        steps:
            - name: Get Token for checkout target organizatiom
                id: get_workflow_token_target
                uses: peter-murray/workflow-application-token-action@v2
                with:
                    application_id: ${{ secrets.APP_ID }}
                    application_private_key: ${{ secrets.APP_SECRET }}
        
    start-workflow:            ## use token from get-token job
        - name: Comment on issue
            uses: ./.github/workflows/comment_in_issue_workflow.yml
            with:
                issue_comment: "Workflow started"
                issue_number: ${{ github.event.issue.number }}
            secrets: 
                temp-token: ${{ needs.get-token.outputs.token }}


    handle-issue:
        runs-on: ubuntu-latest
        needs: get-token
        steps:
        
            # Checkout
            - name: Checkout
              uses: actions/checkout@v3

    ...

    finish-workflow:            ## use token from get-token job
        - name: Comment on issue
            uses: ./.github/workflows/comment_in_issue_workflow.yml
            with:
            issue_comment: "Workflow Finished"
            issue_number: ${{ github.event.issue.number }}
            secrets: 
            temp-token: ${{ needs.get-token.outputs.token }}
    ...

    
```
</details>