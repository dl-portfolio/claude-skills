# GraphQL Query & Mutation Templates

> **Read-only reference file.** Claude reads these templates via the Read tool
> and constructs `gh api graphql` commands inline, substituting `$VARIABLES`.

---

## 1. Query All Fields

Fetch every field on a project with its ID, name, type, and option IDs.

```graphql
query {
  node(id: "$PROJECT_ID") {
    ... on ProjectV2 {
      fields(first: 30) {
        nodes {
          ... on ProjectV2Field {
            id
            name
            dataType
          }
          ... on ProjectV2SingleSelectField {
            id
            name
            dataType
            options {
              id
              name
              color
            }
          }
          ... on ProjectV2IterationField {
            id
            name
            dataType
          }
        }
      }
    }
  }
}
```

---

## 2. Query Items With Fields (Paginated)

List project items with their field values. Use `$CURSOR` for pagination (omit `after` for the first page).

```graphql
query {
  node(id: "$PROJECT_ID") {
    ... on ProjectV2 {
      items(first: 50, after: "$CURSOR") {
        pageInfo {
          hasNextPage
          endCursor
        }
        nodes {
          id
          content {
            ... on Issue {
              number
              title
              state
              url
              labels(first: 10) {
                nodes { name }
              }
              assignees(first: 5) {
                nodes { login }
              }
            }
          }
          fieldValues(first: 20) {
            nodes {
              ... on ProjectV2ItemFieldTextValue {
                text
                field { ... on ProjectV2Field { name } }
              }
              ... on ProjectV2ItemFieldSingleSelectValue {
                name
                field { ... on ProjectV2SingleSelectField { name } }
              }
              ... on ProjectV2ItemFieldNumberValue {
                number
                field { ... on ProjectV2Field { name } }
              }
              ... on ProjectV2ItemFieldDateValue {
                date
                field { ... on ProjectV2Field { name } }
              }
            }
          }
        }
      }
    }
  }
}
```

---

## 3. Add Item to Project

Add an existing issue (by node ID) to a project.

```graphql
mutation {
  addProjectV2ItemById(input: {
    projectId: "$PROJECT_ID"
    contentId: "$ISSUE_NODE_ID"
  }) {
    item {
      id
    }
  }
}
```

> **Note:** The mutation name in the API is `addProjectV2ItemById` but it
> accepts any content node ID (issue or PR).

---

## 4. Set Field Value (Single Select)

Set a single-select field on a project item.

```graphql
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: "$PROJECT_ID"
    itemId: "$ITEM_ID"
    fieldId: "$FIELD_ID"
    value: { singleSelectOptionId: "$OPTION_ID" }
  }) {
    projectV2Item {
      id
    }
  }
}
```

---

## 5. Link Repository to Project

Link a repository so issues from it appear in the project sidebar.

```graphql
mutation {
  linkProjectV2ToRepository(input: {
    projectId: "$PROJECT_ID"
    repositoryId: "$REPO_NODE_ID"
  }) {
    repository {
      nameWithOwner
    }
  }
}
```

> **Cross-owner note:** This mutation will fail if the project owner and repo
> owner differ (e.g., user project + org repo). This is expected — issues can
> still be added via mutation #3. Catch the error and continue.

---

## 6. Update Field Options (Add to Single Select)

Add new options to an existing single-select field. **WARNING:** You must
include ALL existing options plus the new ones — this mutation replaces the
full option list.

```graphql
mutation {
  updateProjectV2Field(input: {
    fieldId: "$FIELD_ID"
    singleSelectOptions: [
      { name: "$EXISTING_OPTION_1", color: $COLOR_1, description: "" },
      { name: "$EXISTING_OPTION_2", color: $COLOR_2, description: "" },
      { name: "$NEW_OPTION", color: $NEW_COLOR, description: "" }
    ]
  }) {
    projectV2Field {
      ... on ProjectV2SingleSelectField {
        options { id name color }
      }
    }
  }
}
```

> **Colors** are GraphQL enum values (unquoted): BLUE, ORANGE, GREEN, RED,
> PURPLE, PINK, GRAY, YELLOW. Not hex codes.

---

## 7. Get Repository Node ID

```graphql
query {
  repository(owner: "$OWNER", name: "$REPO") {
    id
    nameWithOwner
  }
}
```

---

## 8. Get Issue Node ID

```graphql
query {
  repository(owner: "$OWNER", name: "$REPO") {
    issue(number: $NUMBER) {
      id
      title
      state
    }
  }
}
```

---

## 9. Find Project Item by Issue Node ID

Given an issue's node ID, find its corresponding project item ID.

```graphql
query {
  node(id: "$PROJECT_ID") {
    ... on ProjectV2 {
      items(first: 100, after: "$CURSOR") {
        pageInfo { hasNextPage endCursor }
        nodes {
          id
          content {
            ... on Issue { id number }
          }
        }
      }
    }
  }
}
```

> After fetching, filter client-side for the item whose `content.id` matches
> `$ISSUE_NODE_ID`. Paginate if needed.

---

## 10. List User/Org Projects

Enumerate projects for interactive selection during `/cc connect`.

**User projects:**
```graphql
query {
  viewer {
    projectsV2(first: 20, orderBy: { field: UPDATED_AT, direction: DESC }) {
      nodes {
        id
        title
        number
        url
        shortDescription
      }
    }
  }
}
```

**Organization projects:**
```graphql
query {
  organization(login: "$ORG") {
    projectsV2(first: 20, orderBy: { field: UPDATED_AT, direction: DESC }) {
      nodes {
        id
        title
        number
        url
        shortDescription
      }
    }
  }
}
```
