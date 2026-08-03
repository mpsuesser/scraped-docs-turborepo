---
url: https://turborepo.dev/docs/messages/missing-root-task-in-turbo-json
title: "Missing root task in turbo.json"
description: "Learn more about errors for missing root tasks in turbo.json in Turborepo."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

# Missing root task in turbo.json



## Why this error occurred

Root tasks are the scripts defined in the monorepo's root `package.json`. These tasks often call `turbo`. For example:

```json title="./package.json"
{
  "scripts": {
    "build": "turbo run build"
  }
}
```

This creates a problem when we declare [topological dependencies](../reference/configuration.md#dependson). Topological
dependencies specify that your package's dependencies should execute their tasks before your package executes its own task.

```json title="./turbo.json"
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

Because the root package is a dependency for all packages inside your workspace, its task would get executed first.
But since its task calls `turbo`, this would cause an infinite loop.

## Solution

As long as the root task does *not* call `turbo`, you can add it to the `tasks` field in `turbo.json`:

```json title="./turbo.json"
{
  "tasks": {
    "//#build": {}
  }
}
```

This will permit tasks to depend on `//#build`.

However, if the root task does call `turbo`, this can cause infinite recursion. In this case, we don't recommend depending
on the root task. Instead, you can determine the tasks that this root task depends on, and depend on those directly.
For instance, if `//#build` depends on `app#lint` and `docs#lint`, then you can declare those as dependencies.


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
