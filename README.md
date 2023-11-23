
# **GitHub Actions Workflow Lab**

## **Objective**

Learn how to create a GitHub Actions workflow that triggers on issue creation or updates, and includes an issue template for repository name input. This lab also demonstrates how to create a reusable workflow for creating a new repository.

## **Prerequisites**

- A GitHub account
- Basic understanding of GitHub and GitHub Actions

## **Steps**

### **1. Create an Issue Template**

- In your GitHub repository, create a new file under `.github/ISSUE_TEMPLATE/template.yml`
    
    ```yaml
    name: Name of your template
    description: Short description of what it does
    title: "GitHub setting up IssueOps"
    labels: ["newRepo"]
    body:
      - type: "input"
        attributes:
          label: ">>repo-name<<"
          description: "New Repo name"
          placeholder: ""
        validations:
          required: true
    
    ```
    
- Save the template.

### 2. **Create a New Workflow**

- Go to the **`Actions`** tab in your repository.
- Click on **`New workflow`** and choose **`set up a workflow yourself`**.

****3. Configure the Workflow File****

In the `**issue_workflow.yml`** file, add the following content:

```yaml
name: Issue Triggered Workflow

on:
  issues:
    types: [opened, edited]

jobs:
  handle-issue:
    runs-on: ubuntu-latest
    steps:
	  
//TODO: add step to checkout repository

//Get token
    - name: Get Token for checkout target organizatiom
      id: get_workflow_token_target
      uses: peter-murray/workflow-application-token-action@v2
      with:
       application_id: ${{ secrets.APP_ID }}
       application_private_key: ${{ secrets.APP_SECRET }}
          

//TODO: Add step to read input by using action peter-murray/issue-forms-body-parser
    - name: Run Issue form parser
      id: parse
      uses: peter-murray/issue-forms-body-parser@v2.0.0
      with:
        issue_id: ${{ github.event.issue.number }}
        separator: '###'
        label_marker_start: '>>'
        label_marker_end: '<<'

    - name: Start Workflow Comment
      uses: actions/github-script@v6
      with:
        script: |
          const issueComment = context.payload.issue.number;
          github.issues.createComment({
            issue_number: issueComment,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: 'Workflow started 🚀'
          });
		
		//TODO: Add step to read input by using 

      - name: Publish output variables
        uses: actions/github-script@v6
        id: generate_variables
        env:
          demo_payload: ${{ steps.parse.outputs.payload  }}
        with:
          script: |
            demoPayload = JSON.parse(process.env.demo_payload);
            const repository = demoPayload['repo-name'];
						return repository
            
          

    - name: Call Reusable Workflow
      uses: ./.github/workflows/create_repo_workflow.yml
      with:
        repo-name: ${{ steps.generate_variables.outputs.result }}
			secrets:
	      temp-token: ${{ steps.get_workflow_token_target.outputs.token }}

    - name: Finish Workflow Comment
      uses: actions/github-script@v6
      with:
        script: |
          const issueComment = context.payload.issue.number;
          github.issues.createComment({
            issue_number: issueComment,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: 'Workflow finished 🏁'
          });

```

### **4. Create the Reusable Workflow**

- Create a new file in **`.github/workflows/`** named **`create_repo_workflow.yml`**.
- Add the following content:

```yaml
yam
name: Create Repository Workflow

on:
  workflow_call:
    inputs:
      repo-name:
        required: true
        type: string
		 secrets:
      temp-token:
        required: true

jobs:
  create-repository:
    runs-on: ubuntu-latest
    steps:
    - name: Create a New Repository
      uses: actions/github-script@v6
      with:
				github-token: ${{ secrets.temp-token }}
        script: |
          const repoName = ${{ inputs.repo-name }};
          // Logic to create a new repository
					octokit.rest.repos.createForAuthenticatedUser({
					  repoName,
					});

```

### 5**. Commit the Workflow File**

- Commit the **`issue_workflow.yml`** and **`create_repo_workflow.yml`** files to your repository.

1. Set up necessery secrets, to retrive a temporary the token from the Github App. Make sure Github App has all of the needed permissions

### **4. Testing the Workflow**

- Create or update an issue in your repository.
- Observe the Actions tab to see your workflow run.

### **Notes**

- Ensure your script correctly parses the issue body to extract repository name and owner for creating a new repository.
- Adjust the privacy settings of the new repository as needed in the script.
