---
url: https://turborepo.dev/docs/messages/package-task-in-single-package-workspace
title: "Package task in single-package workspace error"
description: "How to fix errors from using package-scoped task syntax in a single-package workspace."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Package task in single-package workspace error



## Why this error occurred

In single package mode, there cannot be multiple packages in your repository. Therefore, declaring a task in the
`turbo.json` with a specified package name is not permitted.

```json title="./turbo.json"
{
  "tasks": {
    "app#build": {
      "cache": true
    }
  }
}
```

## Solution

Remove the package name from the task declaration.

```json title="./turbo.json"
{
  "tasks": {
    "build": {
      "cache": true
    }
  }
}
```

Alternatively, if you would like to have multiple packages, you can [specify the workspaces in your repository](/docs/getting-started/add-to-existing-repository).


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
