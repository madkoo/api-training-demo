#  **Create a New Workflow**

- Go to the **`Actions`** tab in your repository.
- Click on **`New workflow`** and choose **`set up a workflow yourself`**.

## 1. Configure the Workflow File **

In the `issue_workflow.yml` file, add the following steps to the content:

* Trigger on issue creation or update (types: [opened, edited])
* Checkout the repository
* Get the Github App token using the action peter-murray/workflow-application-token-action
* Parse the issue body using the action peter-murray/issue-forms-body-parser
* Start a comment on the issue to indicate the workflow has started
* Create a new repository using the Github API
* Finish a comment on the issue to indicate the workflow has finished
* Add secerts used in the workflow to the repository

<details>
    <summary>Solution</Summary>
    
```yaml
name: Issue Triggered Workflow

on:
  issues:
    types: [opened, edited]

jobs:
  handle-issue:
    
    runs-on: ubuntu-latest
    steps:
    
        # Checkout
        - name: Checkout
          uses: actions/checkout@v3

        #Get token
        - name: Get Token for checkout target organizatiom
          id: get_workflow_token_target
          uses: peter-murray/workflow-application-token-action@v2
          with:
            application_id: ${{ secrets.APP_ID }}
            application_private_key: ${{ secrets.APP_SECRET }}
          

        #TODO: Add step to read input by using action peter-murray/issue-forms-body-parser
        - name: Run Issue form parser
          id: parse
          uses: peter-murray/issue-forms-body-parser@v2.0.0
          with:
            issue_id: ${{ github.event.issue.number }}
            separator: '###'
            label_marker_start: '>>'
            label_marker_end: '<<'

        - name: Start Workflow Comment
          uses: actions/github-script@v7
          with:
            github-token: ${{ steps.get_workflow_token_target.outputs.token }}
            script: |
              const issueNr = context.payload.issue.number;
              github.rest.issues.createComment({
                issue_number: issueNr,
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: 'Workflow started 🚀'
              });

        # Step to read input by using 
        - name: Publish output variables
          uses: actions/github-script@v7
          id: generate_variables
          env:
            demo_payload: ${{ steps.parse.outputs.payload  }}
          with:
            script: |
                  demoPayload = JSON.parse(process.env.demo_payload);
                  const repository = demoPayload['repo-name'];
                  return repository;
        
        - name: Create a New Repository
          uses: actions/github-script@v7
          with:
            github-token: ${{ secrets.MY_PAT }}
            script: |
              const repoName = ${{ steps.generate_variables.outputs.result }};
              await github.rest.repos.createForAuthenticatedUser({
                name: repoName
              });
              
        - name: Finish Workflow Comment
          uses: actions/github-script@v6
          with:
            github-token: ${{ steps.get_workflow_token_target.outputs.token }}
            script: |
              const issueNr = context.payload.issue.number;
              github.rest.issues.createComment({
                issue_number: issueNr,
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: 'Workflow finished 🏁'
              });

```

</details>


### 1. Commit the Workflow File**

* Commit the `issue_workflow.yml`  file to your repository.
* Set up necessery secrets that were used in the workflow, to retrive a temporary the token from the Github App. Make sure Github App has all of the needed permissions

