# 🔨 Hands-on: My first issue template

## **Steps**

### **1. Create an Issue Template**

Read more here: https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-issues/creating-an-issue-template-for-your-repository
- In your GitHub repository, create a new file under `.github/ISSUE_TEMPLATE/template.yml`

   - add a body type "input"
   - add lable between `>>` and `<<` to mark the input
   - add a validation to make sure the input is required
   - add a description to the input 
    
- Save the template.

<details>
  <summary>Solution</Summary>

```yaml
  name: Create a new repository
  description: Template to provide input for repo creation
  title: "GitHub setting up IssueOps"
  labels: ["newRepo"]
  body:
    - type: "input"
      attributes:
        label: ">>repo-name<<"
        description: "New Repo name"
        placeholder: "Add name"
      validations:
        required: true
```
</details>

Continue to the next step to create a workflow that uses the template 

