---
url: https://turborepo.dev/docs/crafting-your-repository/caching
title: "Caching"
description: "Configure task caching to avoid repeating work, using fingerprinting for inputs and restoring outputs from cache."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

Turborepo uses caching to speed up builds, ensuring you **never do the same work twice**. When your task is cacheable, Turborepo will restore the results of your task from cache using a fingerprint from the first time the task ran.

![12 tasks are being ran in 3 packages, resulting in a ">>> FULL TURBO" cache hit. The total time it takes to restore these tasks from cache is 80 milliseconds.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fwhy-turborepo-solution.2xt1tkfstms2z.png&w=3840&q=75&dpl=dpl_GvvaU1dSxMmdhnstDgQWwX3gUQJv)

Turborepo's caching results in significant time savings when working locally - and is even more powerful when [Remote Caching](../core-concepts/remote-caching.md) is enabled, sharing a cache among your entire team and CI.

On this page, you'll learn:

- [How to hit your first Turborepo cache](#hit-your-first-turborepo-cache)
- [How to enable Remote Caching](../core-concepts/remote-caching.md)
- [What Turborepo uses for the inputs and outputs to a hash](#task-inputs)
- [How to troubleshoot caching issues](#troubleshooting)

## Hit your first Turborepo cache

You can try out Turborepo's caching behavior in three steps:

### Create a new Turborepo project

Use `npx create-turbo@latest` and follow the prompts to create a new Turborepo.

```
npx create-turbo@latest
```

### Run a build for the first time

If you have [`turbo` installed globally](../getting-started/installation.md#global-installation), run `turbo build` in your repository.

Alternatively, you can run the `build` script in `package.json` using your package manager.

#### pnpm

```
pnpm run build
```

#### yarn

```
yarn build
```

#### npm

```
npm run build
```

#### bun

```
bun run build
```

This will result in a cache miss, since you've never ran `turbo` before with this [set of inputs](#task-inputs) in this repository. The inputs are turned into a hash to check for in your local filesystem cache or in [the Remote Cache](../core-concepts/remote-caching.md).

### Hit the cache

Run `turbo build` again. You will see a message like this:

![A terminal window showing two tasks that have been ran through turbo. They successfully complete in 116 milliseconds.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Ffull-turbo.2ot08zh_9yksr.png&w=3840&q=75&dpl=dpl_GvvaU1dSxMmdhnstDgQWwX3gUQJv)

Because the inputs' fingerprint is already in the cache, there's no reason to rebuild your applications from zero again. You can restore the results of the previous build from cache, saving resources and time.

## Remote Caching

Turborepo stores the results of tasks in the `.turbo/cache` directory on your machine. However, you can make your entire organization even faster by sharing this cache with your teammates and CI.

To learn more about Remote Caching and its benefits, visit the [Remote Caching page](../core-concepts/remote-caching.md).

### Enabling Remote Cache

First, authenticate with your Remote Cache provider:

```
npx turbo login
```

Then, link the repository on your machine to Remote Cache:

```
npx turbo link
```

Now, when you run a task, Turborepo will automatically send the outputs of the task to Remote Cache. If you run the same task on a different machine that is also authenticated to your Remote Cache, it will hit cache the first time it runs the task.

For information on how to connect your CI machines to Remote Cache, visit [the Constructing CI guide](constructing-ci.md#enabling-remote-caching).

## Git Worktree Cache Sharing

When working with [Git worktrees](https://git-scm.com/docs/git-worktree), Turborepo automatically shares the local filesystem cache between the main worktree and any linked worktrees. This enables:

- **Cache hits across branches**: Work done on one branch is available when you switch to another branch in a different worktree
- **Reduced disk usage**: Avoids duplicate cache entries across worktrees
- **Faster iteration**: Switching between feature branches benefits from existing cache

### How it works

Git worktrees allow you to have multiple working directories attached to the same repository, each checked out to a different branch. When you create a linked worktree with `git worktree add`, Turborepo detects this configuration and automatically redirects the cache to the main worktree's `.turbo/cache` directory.

```
# Create a linked worktree for a feature branch
git worktree add ../my-feature feature-branch

# Run turbo in the linked worktree - cache is shared with main worktree
cd ../my-feature
turbo build
```

When worktree cache sharing is active, you'll see a message in the output:

```
• Remote caching enabled, using shared worktree cache
```

### With Remote Caching

Git worktree cache sharing works alongside [Remote Caching](../core-concepts/remote-caching.md). When both are enabled:

- **Local cache** is shared between worktrees on the same machine
- **Remote cache** is shared across all machines and CI

This means a task built in one worktree can be restored from the shared local cache in another worktree instantly, without a network request. If the local cache doesn't have the artifact, Turborepo will fall back to the Remote Cache as usual.

## What gets cached?

Turborepo caches two types of outputs: Task outputs and Logs.

### Task outputs

Turborepo caches the file outputs of a task that are defined in [the `outputs` key](../reference/configuration.md#outputs) of `turbo.json`. When there's a cache hit, Turborepo will restore the files from the cache.

The `outputs` key is optional, see [the API reference](../reference/configuration.md#outputs) for how Turborepo behaves in this case.

Providing file outputs

### Logs

Turborepo always captures the terminal outputs of your tasks, restoring those logs to your terminal from the first time that the task ran.

You can configure the verbosity of the replayed logs using [the `--output-logs` flag](../reference/run.md#--output-logs-option) or [`outputLogs` configuration option](../reference/configuration.md#outputlogs).

## Task inputs

Inputs are hashed by Turborepo, creating a "fingerprint" for the task run. When "fingerprints" match, running the task will hit the cache.

Under the hood, Turborepo creates two hashes: a global hash and a task hash. If either of the hashes change, the task will miss cache.

### Global hash inputs

| Input | Example |
| --- | --- |
| Resolved task definition from root `turbo.json` and package `turbo.json` | Changing [`outputs`](../reference/configuration.md#outputs) in either root `turbo.json` or [Package Configuration](../reference/package-configurations.md) |
| Lockfile changes that affect the Workspace root | Updating dependencies in root `package.json` will cause **all** tasks to miss cache |
| Source files in internal packages used by the Workspace root | If the root `package.json` depends on `@repo/tooling`, changing `packages/tooling/src/index.ts` will cause **all** tasks to miss cache |
| [`globalDependencies`](../reference/configuration.md#globaldependencies) file contents | Changing `./.env` when it is listed in `globalDependencies` will cause **all** tasks to miss cache |
| Values of variables listed in [`globalEnv`](../reference/configuration.md#globalenv) | Changing the value of `GITHUB_TOKEN` when it is listed in `globalEnv` |
| Flag values that affect task runtime | Using behavior-changing flags like `--cache-dir`, `--framework-inference`, or `--env-mode` |
| Arbitrary passthrough arguments | `turbo build -- --arg=value` will cause **all** tasks to miss cache when compared to either `turbo build` or `turbo build -- --arg=diff` (including dependencies of `build` that did not receive `--arg=value`) |

#### Root workspace dependencies

Turborepo includes the source files of every internal workspace package that the root `package.json` depends on, directly or transitively, in the global hash. This keeps cache entries correct when root-level scripts or tooling use those packages.

For example, if the root package depends on `@repo/tooling`, changing a source file in `@repo/tooling` causes every cacheable task to miss cache. A change to an internal package outside this root dependency closure only changes the relevant package and task hashes.

When comparing Git revisions, `turbo query affected --packages` reports this repository-wide result with the `RootInternalDepChanged` reason. See [Understanding affected-package reasons](../reference/query.md#understanding-affected-package-reasons) for the diagnostic reason types.

### Package hash inputs

| Input | Example |
| --- | --- |
| [Package Configuration](../reference/package-configurations.md) changes | Changing a package's `turbo.json` |
| Lockfile changes that affect the package | Updating dependencies in a package's `package.json` |
| Package's `package.json` changes | Updating the `name` field in a package's `package.json` |
| File changes | Defaults to all source-controlled files in the package directory. Configurable with [`inputs`](../reference/configuration.md#inputs) |

## Troubleshooting

### Using dry runs

Turborepo has a [`--dry` flag](../reference/run.md#--dry----dry-run) that can be used to see what would happen if you ran a task without actually running it. This can be useful for debugging caching issues when you're not sure which tasks you're running.

For more details, visit the [`--dry` API reference](../reference/run.md#--dry----dry-run).

### Using Run Summaries

Turborepo has a [`--summarize` flag](../reference/run.md#--summarize) that can be used to get an overview of all of a task's inputs, outputs, and more. Comparing two summaries will show why two task's hashes are different. This can be useful for:

- Debugging inputs: There are many inputs to a task in Turborepo. If a task is missing cache when you expect it to hit, you can use a Run Summary to check which inputs are different that you weren't expecting.
- Debugging outputs: If cache hits aren't restoring the files you're expecting, a Run Summary can help you understand what outputs are being restored from cache.

Summaries viewer

### Turning off caching

Sometimes, you may not want to write the output of tasks to the cache. This can be set permanently for a task using [`"cache": false`](../reference/configuration.md#cache) or for a whole run using [the `--cache <options>` flag](../reference/run.md#--no-cache).

### Overwriting a cache

If you want to force `turbo` to re-execute a task that has been cached, use [the `--force` flag](../reference/run.md#--force). Note that this disables **reading** the cache, **not writing**.

### Caching a task is slower than executing the task

It's possible to create scenarios where caching ends up being slower than not caching. These cases are rare, but a few examples include:

- **Tasks that execute extremely fast**: If a task executes faster than a network round-trip to the [Remote Cache](../core-concepts/remote-caching.md), you should consider not caching the task.
- **Tasks whose output assets are enormous**: It's possible to create an artifact that is so big that the time to upload or download it exceeds the time to regenerate it, like a complete Docker Container. In these cases, you should consider not caching the task.
- **Scripts that have their own caching**: Some tasks have their own internal caching behavior. In these cases, configuration can quickly become complicated to make Turborepo's cache and the application cache work together.

While these situations are rare, be sure to test the behavior of your projects to determine if disabling caching in specific places provides a performance benefit.

## Next steps

Now that you've seen how Turborepo's caching makes your repository faster, let's take a look at how to develop applications and libraries in your Turborepo.
