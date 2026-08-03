---
url: https://turborepo.dev/docs/messages/unnecessary-package-task-syntax
title: "Unnecessary package task syntax error"
description: "Learn more about errors with unnecessary package task syntax in Turborepo."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

# Unnecessary package task syntax error



## Why this error occurred

Turborepo supports adding additional `turbo.json` files in a package directory
to override the `turbo.json` file declared at the repository root, a feature called [Workspace Configurations](../crafting-your-repository/structuring-a-repository.md#specifying-packages-in-a-monorepo).
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

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
