// Generate hands on labs instruction how to use GrapQl to update a repository name in github using the Graphql explorer

https://docs.github.com/en/graphql/overview/explorer
https://docs.github.com/en/graphql/reference/mutations#updaterepository

# Hands-On Lab: Using GraphQL to Update a Repository Name in GitHub

## Objective
Learn how to use GraphQL to update a repository name in GitHub using the GitHub GraphQL Explorer.

## Prerequisites
- A GitHub account.
- Basic knowledge of GraphQL.

## Tools
- GitHub GraphQL Explorer: [GitHub GraphQL Explorer](https://docs.github.com/en/graphql/overview/explorer)

## Lab Steps

### Step 1: Understanding GraphQL
- Briefly review the basics of GraphQL queries and mutations.
- Understand the difference between a query (retrieving data) and a mutation (modifying data).

### Step 2: Accessing the GitHub GraphQL Explorer
- Navigate to the GitHub GraphQL Explorer.
- Log in with your GitHub credentials.

### Step 3: Fetching Repository Information
- Write a simple query to fetch details of your repositories. For example:
  ```graphql
  query {
    viewer {
      repositories(first: 5) {
        nodes {
          name
        }
      }
    }
  }

### Step 4: Constructing the Mutation Query

- Learn how to construct a mutation query to change a repository name.
- Understand the required arguments for the mutation, such as repository ID and the new name.

### Step 5: Finding the Repository ID
- Execute a query to find the ID of the repository you want to rename. Repository IDs are typically encoded in Base64.

### Step 6: Executing the Mutation

- Construct and execute the mutation query. For example:

```graphql

mutation {
  updateRepository(input: {repositoryId: "YOUR_REPO_ID", name: "NEW_REPO_NAME"}) {
    repository {
      name
    }
  }
}
```

### Step 7: Verifying the Change
After executing the mutation, verify if the repository name has been updated.
Use the GitHub website to check the new repository name.

### Tips
Always test your queries in the GraphQL Explorer before implementing them in your application.
Be cautious with mutations; they can alter or delete data.
Conclusion
Upon completion of this lab, you should be able to use GraphQL to update a repository name in GitHub. This skill can be applied to automate and manage repositories more efficiently.

### Additional Resources
GitHub GraphQL API Documentation:  [GitHub GraphQL API](https://docs.github.com/en/graphql)