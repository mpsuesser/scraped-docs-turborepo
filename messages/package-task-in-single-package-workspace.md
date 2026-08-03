---
url: https://turborepo.dev/docs/messages/package-task-in-single-package-workspace
title: "Package task in single-package workspace error"
description: "Learn more about errors with package tasks in single-package workspaces."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
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

Alternatively, if you would like to have multiple packages, you can [specify the workspaces in your repository](../getting-started/add-to-existing-repository.md).


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
