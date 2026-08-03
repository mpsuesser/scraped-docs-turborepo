---
url: https://turborepo.dev/docs/crafting-your-repository/running-tasks
title: "Running tasks"
description: "Execute tasks using the turbo CLI with scripts, global turbo, filters, and multi-task parallelization."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

Learn how to run tasks in your repository through the \`turbo\` CLI.

Turborepo optimizes the developer workflows in your repository by automatically parallelizing and caching tasks. Once a task is [registered in `turbo.json`](configuring-tasks.md), you have a powerful new toolset for running the scripts in your repository:

- [Use `scripts` in `package.json` for tasks you need to run often](#using-scripts-in-packagejson)
- [Use global `turbo` to quickly run custom tasks on-demand](#using-global-turbo)
- [Filter tasks by directories, package names, source control changes, and more](#using-filters)

Running tasks through `turbo` is powerful because you get one model for executing workflows throughout your repository in development and in your CI pipelines.

## Using scripts in package.json

For tasks that you run frequently, you can write your `turbo` commands directly into your root `package.json`.

```
{
  "scripts": {
    "dev": "turbo run dev",
    "build": "turbo run build",
    "test": "turbo run test",
    "lint": "turbo run lint"
  }
}
```

These scripts can then be run using your package manager.

#### pnpm

```
pnpm dev
```

#### yarn

```
yarn dev
```

#### npm

```
npm run dev
```

#### bun

```
bun run dev
```

## Using global turbo

[Installing `turbo` globally](../getting-started/installation.md#global-installation) lets you run commands directly from your terminal. This improves your local development experience since it makes it easier to run exactly what you need, when you need it.

Additionally, global `turbo` is useful in your CI pipelines, giving you maximum control of exactly which tasks to run at each point in your pipeline.

### Automatic Package Scoping

When you're in a package's directory, `turbo` will automatically scope commands to the [Package Graph](../core-concepts/package-and-task-graph.md#package-graph) for that package. This means you can quickly write commands without having to [write filters](../reference/run.md#--filter-string) for the package.

```
cd apps/docs
turbo build
```

In the example above, the `turbo build` command will run the `build` task for the `docs` package using the `build` task registered in `turbo.json`.

### Customizing behavior

In [the documentation for the `run` subcommand](../reference/run.md), you'll find many useful flags to tailor the behavior of `turbo run` for what you need. When running global `turbo`, you can go faster using workflows like:

- **Variations of your most common commands**: The `build` script in `package.json` has the most utility when it is `turbo build` - but you might only be interested in a specific package at the moment. You can quickly filter for the specific package you're interested in using `turbo build --filter=@repo/ui`.
- **One-off commands**: Commands like `turbo build --dry` aren't needed often so you likely won't create a script in your `package.json` for it. Instead, you can run it directly in your terminal whenever you need it.
- **Overriding `turbo.json` configuration**: Some CLI flags have an equivalent in `turbo.json` that you can override. For instance, you may have a `turbo build` command configured to use [`"outputLogs": "full"` in `turbo.json`](../reference/configuration.md#outputlogs) - but you're only interested in seeing errors at the moment. Using global `turbo`, you can use `turbo lint --output-logs=errors-only` to only show errors.

## Running multiple tasks

`turbo` is able to run multiple tasks, parallelizing whenever possible.

```
turbo run build test lint check-types
```

This command will run all of the tasks, automatically detecting where it can run a script as early as possible, according to your task definitions.

Ordering of tasks

## Using filters

While [caching](running-tasks.md) ensures you stay fast by never doing the same work twice, you can also filter tasks to run only a subset of [the Task Graph](../core-concepts/package-and-task-graph.md#task-graph).

There are many advanced use cases for filtering in [the `--filter` API reference](../reference/run.md#--filter-string) but the most common use cases are discussed below.

### Filtering by package

Filtering by package is a simple way to only run tasks for the packages you're currently working on.

```
turbo build --filter=@acme/web
```

You can also filter to a specific task for the package directly in your CLI command without needing to use `--filter`:

```
# Run the \`build\` task for the \`web\` package
turbo run web#build

# Run the \`build\` task for the \`web\` package, and the \`lint\` task for the \`docs\` package
turbo run web#build docs#lint
```

### Filtering by directory

Your repository might have a directory structure where related packages are grouped together. In this case, you can capture the glob for that directory to focus `turbo` on those packages.

```
turbo lint --filter="./packages/utilities/*"
```

### Filtering to include dependents

When you're working on a specific package, you might want to run tasks for the package and its dependents. The `...` microsyntax is useful when you're making changes to a package and want to ensure that the changes don't break any of its dependents.

```
turbo build --filter=...ui
```

### Filtering to include dependencies

To limit the scope to a package and its dependencies, append `...` to the package name. This runs the task for the specified package and all packages it depends on.

```
turbo dev --filter=web...
```

### Filtering by source control changes

Using filters to run tasks based on changes in source control is a great way to run tasks only for the packages that are affected by your changes. **Source control filters must be wrapped in `[]`**.

- **Comparing to the previous commit**: `turbo build --filter=[HEAD^1]`
- **Comparing to the main branch**: `turbo build --filter=[main...my-feature]`
- **Comparing specific commits using SHAs**: `turbo build --filter=[a1b2c3d...e4f5g6h]`
- **Comparing specific commits using branch names**: `turbo build --filter=[your-feature...my-feature]`

### Combining filters

For even more specificity, you can combine filters to further refine the entrypoints into your [Task Graph](../core-concepts/package-and-task-graph.md#task-graph).

```
turbo build --filter=...ui --filter={./packages/*} --filter=[HEAD^1]
```

Multiple filters are combined as a **union**, meaning that the [Task Graph](../core-concepts/package-and-task-graph.md#task-graph) will include tasks that match any of the filters. For more information on advanced usage of filters, see [the `--filter` API reference](../reference/run.md#--filter-string).

## Next steps

When you start running tasks in your repository, you might start noticing that your tasks get faster. Next, you'll explore [caching](caching.md) and how `turbo` makes it so you never do the same work twice.
