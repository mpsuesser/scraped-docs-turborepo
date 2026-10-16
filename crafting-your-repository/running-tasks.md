---
url: https://turborepo.dev/docs/crafting-your-repository/running-tasks
title: "Running tasks"
description: "Learn how to run tasks in your repository through the `turbo` CLI."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Turborepo optimizes the developer workflows in your repository by automatically parallelizing and caching tasks. Once a task is [registered in `turbo.json`](configuring-tasks.md), you have a powerful new toolset for running the scripts in your repository:

- [Use `scripts` in `package.json` for tasks you need to run often](#using-scripts-in-packagejson)
- [Use global `turbo` to quickly run custom tasks on-demand](#using-global-turbo)
- [Filter tasks by directories, package names, source control changes, and more](#using-filters)

Running tasks through `turbo` is powerful because you get one model for executing workflows throughout your repository in development and in your CI pipelines.

## Using `scripts` in `package.json`

For tasks that you run frequently, you can write your `turbo` commands directly into your root `package.json`.

```jsonc title="./package.json"
{
  "scripts": {
    "dev": "turbo run dev",
    "build": "turbo run build",
    "test": "turbo run test",
    "lint": "turbo run lint"
  }
}
```

> `turbo` is an alias for `turbo run` - but we recommend using `turbo run` in
> `package.json` and CI workflows to avoid potential collisions with possible
> `turbo` subcommands that could be added in the future.

These scripts can then be run using your package manager.

#### pnpm

```bash title="Terminal"
pnpm dev
```

#### yarn

```bash title="Terminal"
yarn dev
```

#### npm

```bash title="Terminal"
npm run dev
```

#### bun

```bash title="Terminal"
bun run dev
```

#### nub

```bash title="Terminal"
nub run dev
```

#### aube

```bash title="Terminal"
aube run dev
```

> You only want to write `turbo` commands in your root `package.json`. Writing `turbo` commands into the `package.json` of packages can lead to recursively
> calling `turbo`.

## Using global `turbo`

[Installing `turbo` globally](../getting-started/installation.md#global-installation) lets you run commands directly from your terminal. This improves your local development experience since it makes it easier to run exactly what you need, when you need it.

Additionally, global `turbo` is useful in your CI pipelines, giving you maximum control of exactly which tasks to run at each point in your pipeline.

### Automatic Package Scoping

When you're in a package's directory, `turbo` will automatically scope commands to the [Package Graph](../core-concepts/package-and-task-graph.md#package-graph) for that package. This means you can quickly write commands without having to [write filters](../reference/run.md#--filter-string) for the package.

```bash title="Terminal"
cd apps/docs
turbo build
```

In the example above, the `turbo build` command will run the `build` task for the `docs` package using the `build` task registered in `turbo.json`.

> [Using a filter](#using-filters) will override Automatic Package Scoping.

### Customizing behavior

In [the documentation for the `run` subcommand](../reference/run.md), you'll find many useful flags to tailor the behavior of `turbo run` for what you need. When running global `turbo`, you can go faster using workflows like:

- **Variations of your most common commands**: The `build` script in `package.json` has the most utility when it is `turbo build` - but you might only be interested in a specific package at the moment. You can quickly filter for the specific package you're interested in using `turbo build --filter=@repo/ui`.
- **One-off commands**: Commands like `turbo build --dry` aren't needed often so you likely won't create a script in your `package.json` for it. Instead, you can run it directly in your terminal whenever you need it.
- **Overriding `turbo.json` configuration**: Some CLI flags have an equivalent in `turbo.json` that you can override. For instance, you may have a `turbo build` command configured to use [`"outputLogs": "full"` in `turbo.json`](../reference/configuration.md#outputlogs) - but you're only interested in seeing errors at the moment. Using global `turbo`, you can use `turbo lint --output-logs=errors-only` to only show errors.

## Running multiple tasks

`turbo` is able to run multiple tasks, parallelizing whenever possible.

```bash title="Terminal"
turbo run build test lint check-types
```

This command will run all of the tasks, automatically detecting where it can run a script as early as possible, according to your task definitions.

> **Ordering of tasks:**
>
> `turbo test lint` will run tasks exactly the same as `turbo lint test`.
>
> If you want to ensure that one task blocks the execution of another, express that relationship in your [task configurations](configuring-tasks.md#defining-tasks).

## Using filters

While [caching](running-tasks.md) ensures you stay fast by never doing the same work twice, you can also filter tasks to run only a subset of [the Task Graph](../core-concepts/package-and-task-graph.md#task-graph).

There are many advanced use cases for filtering in [the `--filter` API reference](../reference/run.md#--filter-string) but the most common use cases are discussed below.

### Filtering by package

Filtering by package is a simple way to only run tasks for the packages you're currently working on.

```bash title="Terminal"
turbo build --filter=@acme/web
```

You can also filter to a specific task for the package directly in your CLI command without needing to use `--filter`:

```bash title="Terminal"
# Run the `build` task for the `web` package
turbo run web#build

# Run the `build` task for the `web` package, and the `lint` task for the `docs` package
turbo run web#build docs#lint
```

### Filtering by directory

Your repository might have a directory structure where related packages are grouped together. In this case, you can capture the glob for that directory to focus `turbo` on those packages.

```bash title="Terminal"
turbo lint --filter="./packages/utilities/*"
```

### Filtering to include dependents

When you're working on a specific package, you might want to run tasks for the package and its dependents. The `...` microsyntax is useful when you're making changes to a package and want to ensure that the changes don't break any of its dependents.

```bash title="Terminal"
turbo build --filter=...ui
```

### Filtering to include dependencies

To limit the scope to a package and its dependencies, append `...` to the package name. This runs the task for the specified package and all packages it depends on.

```bash title="Terminal"
turbo dev --filter=web...
```

### Filtering by source control changes

Using filters to run tasks based on changes in source control is a great way to run tasks only for the packages that are affected by your changes. **Source control filters must be wrapped in `[]`**.

- **Comparing to the previous commit**: `turbo build --filter=[HEAD^1]`
- **Comparing to the main branch**: `turbo build --filter=[main...my-feature]`
- **Comparing specific commits using SHAs**: `turbo build --filter=[a1b2c3d...e4f5g6h]`
- **Comparing specific commits using branch names**: `turbo build --filter=[your-feature...my-feature]`

> In general, you can rely on caching to keep your repository fast. When you're
> using [Remote Caching](../core-concepts/remote-caching.md), you can count on
> hitting cache for unchanged packages.

### Combining filters

For even more specificity, you can combine filters to further refine the entrypoints into your [Task Graph](../core-concepts/package-and-task-graph.md#task-graph).

```bash title="Terminal"
turbo build --filter=...ui --filter={./packages/*} --filter=[HEAD^1]
```

Multiple filters are combined as a **union**, meaning that the [Task Graph](../core-concepts/package-and-task-graph.md#task-graph) will include tasks that match any of the filters. For more information on advanced usage of filters, see [the `--filter` API reference](../reference/run.md#--filter-string).

## Next steps

When you start running tasks in your repository, you might start noticing that your tasks get faster. Next, you'll explore [caching](caching.md) and how `turbo` makes it so you never do the same work twice.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
