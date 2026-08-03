---
url: https://turborepo.dev/docs/messages/unnecessary-package-task-syntax
title: "Unnecessary package task syntax error"
description: "How to fix errors from using `package#task` syntax in a package-level turbo.json where it is unnecessary."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Unnecessary package task syntax error



## Why this error occurred

Turborepo supports adding additional `turbo.json` files in a package directory
to override the `turbo.json` file declared at the repository root, a feature called [Workspace Configurations](/docs/crafting-your-repository/structuring-a-repository#specifying-packages-in-a-monorepo).
In those additional `turbo.json` files, you can only configure tasks for that specific
package. Therefore, only the task name should be included in the task,
not the package and task name (`package#task`).

`turbo.json` file in `apps/web` directory:

```json title="./turbo.json"
{
  "tasks": {
    "web#build": {
      "dependsOn": ["lint"]
    }
  }
}
```

Since this `turbo.json` file is inside a package directory, the `web` prefix is unnecessary.

## Solution

Remove the package prefix from the task name:

```json title="./turbo.json"
{
  "tasks": {
    "build": {
      "dependsOn": ["lint"]
    }
  }
}
```


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
