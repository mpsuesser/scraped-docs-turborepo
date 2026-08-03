---
url: https://turborepo.dev/docs/guides/tools/biome
title: "Biome"
description: "Configure Biome as a root task for fast formatting and linting across your monorepo."
access_date: 2026-08-03T19:40:59.569Z
current_date: 2026-08-03T19:40:59.569Z
---

Learn how to use Biome in your Turborepo projects.

[Biome](https://biomejs.dev/) is a fast formatter for JavaScript, TypeScript, JSX, and JSON that saves CI and developer time.

## Using Biome with Turborepo

Biome is a rare exception to most tools that are used with Turborepo because it is **so extraordinarily fast**. For this reason, we recommend using a [Root Task](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks) rather than creating separate scripts in each of your packages.

Caching behavior

### Initialize Biome

First, [follow the installation documentation to set up Biome](https://biomejs.dev/guides/getting-started/) in your repository. You'll then be able to create a script to use Biome in the root of your repository:

```
{
  "scripts": {
    "format-and-lint": "biome check .",
    "format-and-lint:fix": "biome check . --write"
  }
}
```

### Create a root task

In practice, Biome is unlikely to be a bottleneck in the iteration speed of your repository. For this reason, we can have less configuration to manage in our repository by using Biome in a [Root Task](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks).

To create a [Root Task](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks), register the scripts to Turborepo:

```
{
  "tasks": {
    "//#format-and-lint": {},
    "//#format-and-lint:fix": {
      "cache": false
    }
  }
}
```

You'll now be able to run these scripts using `turbo run format-and-lint` and `turbo run format-and-lint:fix`.
