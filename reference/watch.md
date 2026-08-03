---
url: https://turborepo.dev/docs/reference/watch
title: "watch"
description: "API reference for the `watch` command"
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

# watch



Re-run tasks in your repository, based on code changes.

```bash title="Terminal"
turbo watch [tasks]
```

`turbo watch` is dependency-aware, meaning tasks will re-run in the order [configured in `turbo.json`](configuration.md).

By default, `turbo watch` operates at the package level: if any file in a package changes, all tasks in that package are re-run. Enable [`futureFlags.watchUsingTaskInputs`](configuration.md#watchusingtaskinputs) to filter at the task level using each task's [`inputs`](configuration.md#inputs) globs instead.

If no tasks are provided, `turbo` will display what tasks are available for the packages in the repository.

```bash title="Terminal"
turbo watch
```

## Using `turbo watch` with persistent tasks

Persistent tasks are marked with [`"persistent": true`](configuration.md#persistent), meaning they won't exit. Because of this, they cannot be depended on in your task graph.

This means that persistent tasks will be ignored when using `turbo watch`, working the same way they do with [`turbo run`](run.md), allowing persistent and non-persistent tasks to be run at the same time.

### Dependency-aware persistent tasks

When your script has a built-in watcher (like `next dev`) capable of detecting changes in dependencies, you don't need to use `turbo watch`. Instead, use your script's built-in watcher and mark the task as long-running using [`"persistent": true`](configuration.md#persistent).

### Persistent tasks without dependency awareness

Some tools aren't monorepo-friendly, and do not hot-reload modules in dependencies. In those cases, you should
mark the task as [`interruptible: true`](configuration.md#interruptible) to have `turbo watch`
restart the task when relevant changes are detected.

## Limitations

### Caching

Caching tasks with Watch Mode is currently experimental, under the `--experimental-write-cache` flag.

```bash title="Terminal"
turbo watch your-tasks --experimental-write-cache
```

### Task outputs

If you have tasks that write to files checked into source control, there is a possibility that Watch Mode will run in an infinite loop. This is because Watch Mode watches your
files for changes and will re-run tasks in packages that have changed. If a task creates a change, then that will trigger the task again.

Watch Mode has some logic to prevent this from happening using file hashes, but it isn't foolproof. To avoid this issue, we recommend removing any task outputs from git.


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
