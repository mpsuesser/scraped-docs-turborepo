---
url: https://turborepo.dev/docs/reference/configuration
title: "Configuring turbo.json"
description: "Complete reference for all turbo.json configuration options and their behavior."
access_date: 2026-08-03T18:24:07.617Z
current_date: 2026-08-03T18:24:07.617Z
---

Learn how to configure Turborepo through \`turbo.json\`.

Configure the behavior of `turbo` by using a `turbo.json` file in your Workspace's root directory. You can also:

- Use [Package Configurations](package-configurations.md) for more granular control.
- Use `turbo.jsonc` to add comments to your configuration with IDE support.

## Global options

### extends

```
{
  "extends": ["//"],
}
```

Extend from the root `turbo.json` to create specific configuration for a package using [Package Configurations](package-configurations.md).

- The `extends` array must start with `["//"]` to inherit configuration from the root `turbo.json`.
- You can also extend from other packages (e.g., `["//", "shared-config"]`).
- If `extends` is used in the root `turbo.json`, it will be ignored.

### globalDependencies

```
{
  "globalDependencies": ["tsconfig.json"],
}
```

A list of globs that you want to include in all task hashes. **If any file matching these globs changes, all tasks will miss cache.** Globs are relative to the location of `turbo.json`.

By default, the root `package.json`, lockfile, and source files in internal packages that the root package depends on are included in [the global hash](../crafting-your-repository/caching.md#root-workspace-dependencies) and can't be ignored. Any added `globalDependencies` will also be included in the global hash.

### globalEnv

```
{
  "globalEnv": ["GITHUB_TOKEN", "PACKAGE_VERSION", "NODE_ENV"],
}
```

A list of environment variables that you want to impact the hash of all tasks. Any change to these environment variables will cause all tasks to miss cache.

For more on wildcard and negation syntax, [see the `env` section](#env).

### globalPassThroughEnv

```
{
  "globalPassThroughEnv": ["AWS_SECRET_KEY", "GITHUB_TOKEN"],
}
```

A list of environment variables that you want to make available to tasks. Using this key opts all tasks into [Strict Environment Variable Mode](../crafting-your-repository/using-environment-variables.md#strict-mode).

Additionally, Turborepo has a built-in set of global passthrough variables for common cases, like operating system environment variables. This includes variables like `HOME`, `PATH`, `APPDATA`, `SHELL`, `PWD`, and more. The full list can be found [in the source code](https://github.com/vercel/turborepo/blob/main/crates/turborepo-env/src/lib.rs).

Passthrough values do not contribute to hashes for caching

### ui

Default: `"stream"`

Select a terminal UI for the repository.

`"tui"` allows for viewing each log at once and interacting with the task. `"stream"` outputs logs as they come in and is not interactive.

```
{
  "ui": "tui" | "stream"
}
```

### noUpdateNotifier

Default: `false`

When set to `true`, disables the update notification that appears when a new version of `turbo` is available.

```
{
  "noUpdateNotifier": true
}
```

### concurrency

Default: `"10"`

Set/limit the maximum concurrency for task execution. Must be an integer greater than or equal to `1` or a percentage value like `50%`.

- Use `1` to force serial execution (one task at a time).
- Use `100%` to use all available logical processors.
- This option is ignored if the deprecated [`--parallel`](run.md#--parallel) flag is also passed.

```
{
  "concurrency": "1",
}
```

### dangerouslyDisablePackageManagerCheck

Default: `false`

Turborepo uses your repository's lockfile to determine caching behavior, [Package Graphs](../core-concepts/internal-packages.md), and more. Because of this, we use the package manager declaration in your root `package.json` to help you stabilize your Turborepo. We recommend `devEngines.packageManager`; the legacy top-level [`packageManager`](https://nodejs.org/api/packages.html#packagemanager) field is also supported.

To help with incremental migration or in situations where you can't use a package manager declaration, you may use `--dangerously-disable-package-manager-check` to opt out of this check and assume the risks of unstable lockfiles producing unpredictable behavior. When disabled, Turborepo will attempt a best-effort discovery of the intended package manager meant for the repository.

```
{
  "dangerouslyDisablePackageManagerCheck": true,
}
```

### cacheDir

Default: `".turbo/cache"`

Specify the filesystem cache directory.

```
{
  "cacheDir": ".turbo/cache",
}
```

### cacheMaxAge

Default: `"0"` (disabled)

Maximum age of local cache entries before automatic eviction. Entries older than this value are removed when eviction runs at the start of each `turbo run`. Eviction runs in a background thread so it does not block tasks.

```
{
  "cacheMaxAge": "7d",
}
```

Accepts a human-readable duration string with the following units:

| Unit | Example |
| --- | --- |
| `s` | `30s` |
| `m` | `5m` |
| `h` | `24h` |
| `d` | `7d` |
| `w` | `2w` |

Set to `"0"` to disable age-based eviction.

### cacheMaxSize

Default: `"0"` (disabled)

Maximum total size of the local filesystem cache. When the cache exceeds this limit, the oldest entries are evicted until the cache is within the limit. Eviction runs in a background thread at the start of each `turbo run`, so it does not block tasks.

```
{
  "cacheMaxSize": "10GB",
}
```

Accepts a human-readable size string with the following units (case-insensitive):

| Unit | Example |
| --- | --- |
| `MB` | `500MB` |
| `GB` | `10GB` |
| `TB` | `1TB` |

Fractional values like `1.5GB` are supported. Set to `"0"` to disable size-based eviction.

### daemon

**Deprecated**: The daemon is no longer used for `turbo run` and this option will be removed in version 3.0. The `--daemon` and `--no-daemon` flags are also deprecated.

The daemon is still used by `turbo watch` and the Turborepo LSP.

### envMode

Default: `"strict"`

Turborepo's Environment Modes allow you to control which environment variables are available to a task at runtime:

- `"strict"`: Filter environment variables to only those that are specified in the `env` and `globalEnv` keys in `turbo.json`.
- `"loose"`: Allow all environment variables for the process to be available.

```
{
  "envMode": "strict",
}
```

Read more about [Environment Modes](../crafting-your-repository/using-environment-variables.md#environment-modes).

### futureFlags

```
{
  "futureFlags": {
    "errorsOnlyShowHash": true,
  },
}
```

Enable experimental features that will become the default behavior in future versions of Turborepo.

#### errorsOnlyShowHash

Default: `false`

When using [`outputLogs: "errors-only"`](#outputlogs), show task hashes when tasks start and complete successfully. This provides visibility into which tasks are running without showing full output logs.

With this flag enabled, successful tasks will show messages like:

- `cache miss, executing <hash> (only logging errors)` - when a task starts execution
- `cache hit, replaying logs (no errors) <hash>` - when a task is restored from cache

```
{
  "futureFlags": {
    "errorsOnlyShowHash": true,
  },
  "tasks": {
    "build": {
      "outputLogs": "errors-only",
    },
  },
}
```

#### longerSignatureKey

Default: `false`

Enforce a minimum length of 32 bytes for the `TURBO_REMOTE_CACHE_SIGNATURE_KEY` environment variable when [`remoteCache.signature`](#signature) is enabled. HMAC-SHA256 accepts any key length, but keys shorter than 32 bytes significantly weaken the signature, making brute-force tag collision feasible.

When this flag is enabled and the key is too short, Turborepo will fail immediately at the start of the run with an error message indicating the key length and the minimum requirement.

```
{
  "futureFlags": {
    "longerSignatureKey": true,
  },
  "remoteCache": {
    "signature": true,
  },
}
```

#### affectedUsingTaskInputs

Default: `false`

Use task-level [`inputs`](#inputs) globs to determine which tasks are affected by changed files when running with [`--affected`](run.md#--affected). When enabled, only tasks whose declared `inputs` match the changed files are selected, rather than selecting all tasks in changed packages.

Without this flag, `--affected` operates at the **package level**: if any file in a package changed, all tasks in that package are selected. With this flag, `--affected` operates at the **task level**: a task is only selected if the changed files match its `inputs` configuration.

```
{
  "futureFlags": {
    "affectedUsingTaskInputs": true,
  },
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "!README.md"],
    },
  },
}
```

In this example, if only `README.md` changed in a package, `build` would not run because it excludes `README.md` from its inputs.

#### githubActionsRemoteBaseRefFallback

Default: `false`

When GitHub Actions reports a pull request's base branch but that branch is not available as a local ref, fall back to `origin/<branch>`. This supports detached checkouts created by `actions/checkout`, where the base branch may only exist as a remote-tracking ref.

Local refs continue to take precedence. This flag only affects base refs inferred from GitHub Actions. Explicit refs provided through `TURBO_SCM_BASE` and default `main` or `master` resolution are unchanged.

```
{
  "futureFlags": {
    "githubActionsRemoteBaseRefFallback": true,
  },
}
```

#### watchUsingTaskInputs

Default: `false`

Use task-level [`inputs`](#inputs) globs to determine which tasks to re-run when files change in `turbo watch`. When enabled, only tasks whose declared `inputs` match the changed files are re-executed, rather than re-running all tasks in changed packages.

Without this flag, `turbo watch` operates at the **package level**: if any file in a package changes, all tasks in that package are re-run. With this flag, `turbo watch` operates at the **task level**: a task is only re-run if the changed files match its `inputs` configuration.

```
{
  "futureFlags": {
    "watchUsingTaskInputs": true,
  },
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "!README.md"],
    },
  },
}
```

In this example, editing `README.md` in a package would not trigger a `build` re-run because it is excluded from the task's inputs. Editing `src/index.ts` would trigger it because it matches `$TURBO_DEFAULT$`.

#### pruneIncludesGlobalFiles

Default: `false`

Copy files matching [`globalDependencies`](#globaldependencies) globs into the [`turbo prune`](prune.md) output directory.

Without this flag, the `globalDependencies` entries are preserved in the pruned `turbo.json` but the actual files they reference (e.g. a root `tsconfig.json`) are not copied. This can cause tasks to behave differently in the pruned output because the files they depend on are missing.

```
{
  "globalDependencies": ["tsconfig.json"],
  "futureFlags": {
    "pruneIncludesGlobalFiles": true,
  },
}
```

With this configuration, running `turbo prune frontend` will include `tsconfig.json` in the output alongside the pruned workspaces and lockfile. Both inclusion and `!` exclusion globs are supported.

#### filterUsingTasks

Default: `false`

Resolve [`--filter`](run.md#--filter-string) at the **task level** instead of the **package level**. Git-range filters (e.g. `--filter=[main]`) match changed files against each task's [`inputs`](#inputs) globs, and the `...` dependency/dependent syntax traverses the Task Graph in addition to the Package Graph.

Without this flag, `--filter` operates on the Package Graph: a git-range selector marks entire packages as matched if any file in the package changed, and `...` follows package-level dependencies. With this flag, resolution moves to the Task Graph: only tasks whose `inputs` actually match the changed files are selected, and `...` follows task-level dependencies (e.g. `web#build -> schema#gen` is traversed even if `web` has no package-level dependency on `schema`).

```
{
  "futureFlags": {
    "filterUsingTasks": true,
  },
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "!README.md"],
    },
  },
}
```

In this example, `--filter=[main]` would not select `build` for a package if only `README.md` changed, because `README.md` is excluded from `build` 's inputs.

#### strictTaskEntrypointSelection

Default: `false`

Select requested tasks according to whether they resolve a command in the repository. When at least one package can run a requested task, packages without a command do not become entrypoints and their dependencies are not included merely because the task was requested.

```
{
  "futureFlags": {
    "strictTaskEntrypointSelection": true,
  },
  "tasks": {
    "test": {
      "dependsOn": ["build"],
    },
  },
}
```

If `web` has a `test` command and `docs` does not, `turbo run test` starts from `web#test` but not `docs#test`. Missing tasks reached from retained tasks remain in the Task Graph, preserving Transit Nodes and cache invalidation.

When no package resolves a command for a configured or implicitly registered task, it remains available for graph-only orchestration. For example, a scriptless `ci:checks` task can continue to run its configured dependencies. When any of those dependency branches reaches a runnable command, branches that never reach runnable work are removed. A fully scriptless graph is preserved when none of its branches contains a runnable command.

This flag is independent of [`filterUsingTasks`](#filterusingtasks). When both are enabled, a plain task-level filter drops a matching task without a command, while `...` can explicitly select executable dependencies or dependents through that missing task node.

#### globalConfiguration

Default: `false`

Move global configuration keys under a top-level [`global`](#global) key for clarity. When enabled, the following renames apply:

| Old key | New location |
| --- | --- |
| `globalDependencies` | `global.inputs` |
| `globalEnv` | `global.env` |
| `globalPassThroughEnv` | `global.passThroughEnv` |
| `ui` | `global.ui` |
| `envMode` | `global.envMode` |
| `cacheDir` | `global.cacheDir` |
| `cacheMaxAge` | `global.cacheMaxAge` |
| `cacheMaxSize` | `global.cacheMaxSize` |
| `concurrency` | `global.concurrency` |
| `daemon` | `global.daemon` |
| `noUpdateNotifier` | `global.noUpdateNotifier` |
| `dangerouslyDisablePackageManagerCheck` | `global.dangerouslyDisablePackageManagerCheck` |
| `remoteCache` | `global.remoteCache` |
| `experimentalObservability` | `global.experimentalObservability` |

Using any of the old top-level keys while this flag is enabled will produce a hard error with a rename hint.

```
{
  "futureFlags": {
    "globalConfiguration": true,
  },
  "global": {
    "inputs": ["tsconfig.json"],
    "env": ["NODE_ENV"],
  },
}
```

#### experimentalObservability

Default: `false`

When enabled, Turborepo will honor the `experimentalObservability.otel` configuration block (if present) to send run summaries to an OpenTelemetry Protocol (OTLP) collector.

[Visit the Experimental observability section to learn more](#experimental-observability).

```
{
  "tags": ["utils"],
}
```

Adds a tag to a package for use with [Boundaries](boundaries.md).

This key only works in [Package Configurations](package-configurations.md). Using this key in a root `turbo.json` will result in an error.

### global

```
{
  "futureFlags": {
    "globalConfiguration": true,
  },
  "global": {
    "inputs": ["tsconfig.json", ".env"],
    "env": ["NODE_ENV"],
    "passThroughEnv": ["AWS_SECRET_KEY"],
    "ui": "tui",
    "envMode": "strict",
  },
}
```

A top-level key that namespaces all global configuration options. Requires the [`globalConfiguration`](#globalconfiguration) future flag.

When enabled, existing top-level keys are moved under `global` with these renames:

| Old key | New key |
| --- | --- |
| `globalDependencies` | `global.inputs` |
| `globalEnv` | `global.env` |
| `globalPassThroughEnv` | `global.passThroughEnv` |
| `ui` | `global.ui` |
| `envMode` | `global.envMode` |
| `cacheDir` | `global.cacheDir` |
| `cacheMaxAge` | `global.cacheMaxAge` |
| `cacheMaxSize` | `global.cacheMaxSize` |
| `concurrency` | `global.concurrency` |
| `daemon` | `global.daemon` |
| `noUpdateNotifier` | `global.noUpdateNotifier` |
| `dangerouslyDisablePackageManagerCheck` | `global.dangerouslyDisablePackageManagerCheck` |
| `remoteCache` | `global.remoteCache` |
| `experimentalObservability` | `global.experimentalObservability` |

## Defining tasks

### tasks

Each key in the `tasks` object is the name of a task that can be executed by [`turbo run`](run.md). Turborepo will search the packages described in your [Workspace's configuration](../crafting-your-repository/structuring-a-repository.md#specifying-packages-in-a-monorepo) for scripts in `package.json` with the name of the task.

Using the rest of the configuration described in the task, Turborepo will run the scripts in the described order, caching logs and file outputs in [the `outputs` key](#outputs) when provided.

In the example below, we've defined three tasks under the `tasks` key: `build`, `test`, and `dev`.

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**", "!.next/cache/**", "!.next/dev/**"],
    },
    "test": {
      "outputs": ["coverage/**"],
      "dependsOn": ["build"],
    },
    "dev": {
      "cache": false,
      "persistent": true,
    },
  },
}
```

## Task options

Using the options available in the tasks you define in `tasks`, you can describe how `turbo` will run your tasks.

### extends (task-level)

Controls whether a task inherits configuration from the extends chain. This option is only available in [Package Configurations](package-configurations.md), not in the root `turbo.json`.

```
{
  "extends": ["//"],
  "tasks": {
    "lint": {
      "extends": false, // Exclude this task from the package
    },
  },
}
```

| Value | Behavior |
| --- | --- |
| `false` | Task is excluded from inheritance. If no other config is provided, the task won't exist for this package. If other config is provided, creates a fresh task definition with no inheritance. |
| `true` (default) | Task inherits configuration normally from the extends chain. |

See [Excluding tasks from inheritance](package-configurations.md#excluding-tasks-from-inheritance) for examples and more details.

### description

A human- or agent-readable description of what a task does.

```
{
  "tasks": {
    "build": {
      "description": "Compiles the application for production deployment",
    },
  },
}
```

This field is for documentation purposes only and does not affect task execution or caching behavior.

### dependsOn

A list of tasks that are required to complete before the task begins running.

There are three types of `dependsOn` relationships: [dependency relationships](#dependency-relationships), [same-package relationships](#same-package-relationships), and [arbitrary task relationships](#arbitrary-task-relationships).

#### Dependency relationships

Prefixing a string in `dependsOn` with a `^` tells `turbo` that the task must wait for tasks in the package's dependencies to complete first. For example, in the `turbo.json` below:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
    },
  },
}
```

`turbo` starts at the "bottom" of the package graph and recursively visits each package until it finds a package with no internal dependencies. It will then run the `build` task at the end of the dependency chain first, working its way back to the "top" until all `build` tasks are completed in order.

#### Same package relationships

Task names without the `^` prefix describe a task that depends on a different task within the same package. For example, in the `turbo.json` below:

```
{
  "tasks": {
    "test": {
      "dependsOn": ["lint", "build"],
    },
  },
}
```

The `test` task will only run after the `lint` and `build` tasks have completed **in the same package**.

#### Arbitrary task relationships

Specify a task dependency between specific package tasks.

```
{
  "tasks": {
    "web#lint": {
      "dependsOn": ["utils#build"]
    }
  }
}
```

In this `turbo.json`, the `web#lint` task will wait for the `utils#build` task to complete.

### env

The list of environment variables a task depends on.

```
{
  "tasks": {
    "build": {
      "env": ["DATABASE_URL"], // Impacts hash of all build tasks
    },
    "web#build": {
      "env": ["API_SERVICE_KEY"], // Impacts hash of web's build task
    },
  },
}
```

#### Wildcards

Turborepo supports wildcards for environment variables so you can easily account for all environment variables with a given prefix. For example, the `turbo.json` below include all environment variables that start with `MY_API_` into the hash:

```
{
  "tasks": {
    "build": {
      "env": ["MY_API_*"]
    }
  }
}
```

#### Negation

A leading `!` means that the entire pattern will be negated. For instance, the `turbo.json` below will ignore the `MY_API_URL` variable.

```
{
  "tasks": {
    "build": {
      "env": ["!MY_API_URL"]
    }
  }
}
```

#### Examples

| Pattern | Description |
| --- | --- |
| `"*"` | Matches every environment variable. |
| `"!*"` | Excludes every environment variable. |
| `"FOO*"` | Matches `FOO`, `FOOD`, `FOO_FIGHTERS`, etc. |
| `"FOO\*"` | Resolves to `"FOO*"` and matches `FOO`, `FOOD`, and `FOO_FIGHTERS`. |
| `"FOO\\*"` | Matches a single environment variable named `FOO*`. |
| `"!FOO*"` | Excludes all environment variables that start with `FOO`. |
| `"\!FOO"` | Resolves to `"!FOO"`, and excludes a single environment variable named `!FOO`. |
| `"\\!FOO"` | Matches a single environment variable named `!FOO`. |
| `"FOO!"` | Matches a single environment variable named `FOO!`. |

### passThroughEnv

An allowlist of environment variables that should be made available to this task's runtime, even when in [Strict Environment Mode](../crafting-your-repository/using-environment-variables.md#strict-mode).

```
{
  "tasks": {
    "build": {
      // Values will be available within \`build\` scripts
      "passThroughEnv": ["AWS_SECRET_KEY", "GITHUB_TOKEN"],
    },
  },
}
```

### outputs

A list of file glob patterns relative to the package's `package.json` to cache when the task is successfully completed.

See [`$TURBO_ROOT$`](#turbo_root) if output paths need to be relative to the repository root.

Output paths must resolve inside the repository root. Turborepo will not cache outputs that escape the repository.

```
{
  "tasks": {
    "build": {
      // Cache all files emitted to the packages's \`dist\` directory
      "outputs": ["dist/**"],
    },
  },
}
```

Omitting this key or passing an empty array tells `turbo` to cache nothing (except logs, which are always cached when caching is enabled).

### cache

Default: `true`

Defines if task outputs should be cached. Setting `cache` to false is useful for long-running development tasks and ensuring that a task always runs when it is in the task's execution graph.

```
{
  "tasks": {
    "build": {
      "outputs": [".svelte-kit/**", "dist/**"], // File outputs will be cached
    },
    "dev": {
      "cache": false, // No outputs will be cached
      "persistent": true,
    },
  },
}
```

### inputs

Default: `[]`, all files in the package that are checked into source control

A list of file glob patterns relative to the package's `package.json` to consider when determining if a package has changed. The following files are **always** considered inputs, even if you try to explicitly ignore them:

- `package.json`
- `turbo.json`
- Package manager lockfiles

Visit the [file glob specification](globs.md) for more information on globbing syntax.

```
{
  "tasks": {
    "test": {
      "inputs": ["src/**/*.ts", "src/**/*.tsx", "test/**/*.ts"],
    },
  },
}
```

#### $TURBO\_DEFAULT$

Because specifying an `inputs` key immediately opts out of the default behavior, you may use the special string `$TURBO_DEFAULT$` within the `inputs` array to restore `turbo` 's default behavior. This allows you to tweak the default behavior for more granularity.

```
{
  "tasks": {
    "check-types": {
      // Consider all default inputs except the package's README
      "inputs": ["$TURBO_DEFAULT$", "!README.md"],
    },
  },
}
```

#### $TURBO\_ROOT$

Tasks might reference a file that lies outside of their directory.

Starting a file glob with `$TURBO_ROOT$` will change the glob to be relative to the root of the repository instead of the package directory.

```
{
  "tasks": {
    "check-types": {
      // Consider all Typescript files in \`src/\` and the root tsconfig.json as inputs
      "inputs": ["$TURBO_ROOT$/tsconfig.json", "src/**/*.ts"],
    },
  },
}
```

#### Deferred hashing

`inputs` entries can be structured objects that defer file hashing until after the task's dependencies have completed. Tasks that depend on a deferred task are also deferred until upstream hashes are available.

##### mode: "jit"

Hash the files matching `globs` just before the task executes, after its dependencies have completed.

```
{
  "tasks": {
    "codegen": {
      "cache": false,
    },
    "build": {
      "dependsOn": ["codegen"],
      "inputs": [
        "$TURBO_DEFAULT$",
        "!src/generated/**",
        {
          "mode": "jit",
          "globs": ["src/generated/**"],
        },
      ],
    },
  },
}
```

##### mode: "dependencyOutputs"

Hash the intersection of a dependency's declared `outputs` and `globs`. `dependencyOutputs` does not create task graph edges; selected tasks must already be present through `dependsOn`.

```
{
  "tasks": {
    "check-types": {
      "dependsOn": ["^check-types"],
      "outputs": ["dist/**"],
      "inputs": [
        "$TURBO_DEFAULT$",
        {
          "mode": "dependencyOutputs",
          "globs": ["dist/**/*.d.ts"],
          "from": ["^check-types"],
        },
      ],
    },
  },
}
```

- `from` is only valid with `dependencyOutputs`.
- `dependencyOutputs.from` defaults to the task's direct task dependencies.
- A selected `dependencyOutputs` task must declare `outputs`.
- Tasks using `jit` or `dependencyOutputs` cannot know their final cache key until execution time, so `--dry=json` reports `hash: null` and a `hashReason`.

#### $TURBO\_EXTENDS$

When using [Package Configurations](package-configurations.md), array fields completely replace the values from the root `turbo.json` by default. The `$TURBO_EXTENDS$` microsyntax changes this behavior to **append** instead of **replace**.

This microsyntax can be used in the following array fields:

- `dependsOn`
- `env`
- `inputs`
- `outputs`
- `passThroughEnv`
- `with`

For example, if your root `turbo.json` defines:

```
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"],
    },
  },
}
```

A Package Configuration can add additional outputs while keeping the root outputs:

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      // Inherits "dist/**" from root, and adds ".next/**"
      "outputs": [
        "$TURBO_EXTENDS$",
        ".next/**",
        "!.next/cache/**",
        "!.next/dev/**",
      ],
    },
  },
}
```

Without `$TURBO_EXTENDS$`, the `outputs` array would be completely replaced with `[".next/**", "!.next/cache/**", "!.next/dev/**"]`, dropping the `"dist/**"` from the root configuration.

### outputLogs

Default: `full`

Set output logging verbosity. Can be overridden by the [`--output-logs`](run.md#--output-logs-option) CLI option.

| Option | Description |
| --- | --- |
| `full` | Displays all logs |
| `hash-only` | Only show the hashes of the tasks |
| `new-only` | Only show logs from cache misses |
| `errors-only` | Only show logs from task failures |
| `none` | Hides all task logs |

```
{
  "tasks": {
    "build": {
      "outputLogs": "new-only",
    },
  },
}
```

### persistent

Default: `false`

Label a task as `persistent` to prevent other tasks from depending on long-running processes. Persistent tasks are made [interactive](#interactive) by default.

Because a long-running process won't exit, tasks that would depend on it would never run. Once you've labeled the task as persistent, `turbo` will throw an error if other tasks depend on it.

This option is most useful for development servers or other "watch" tasks.

```
{
  "tasks": {
    "dev": {
      "persistent": true,
    },
  },
}
```

Tasks marked with `persistent` are also `interactive` by default.

### interactive

Default: `false` (Defaults to `true` for tasks marked as `persistent`)

Label a task as `interactive` to make it accept inputs from `stdin` in the terminal UI. Must be used with `persistent`.

This option is most useful for scripts that can be manipulated while they are running, like Jest or Vitest.

```
{
  "tasks": {
    "test:watch": {
      "interactive": true,
      "persistent": true,
    },
  },
}
```

### interruptible

Default: `false`

Label a `persistent` task as `interruptible` to allow it to be restarted by `turbo watch`.

`turbo watch` watches for changes to your packages and automatically restarts tasks that are affected. However, if a task is persistent, it will not be restarted by default. To enable restarting persistent tasks, set `interruptible` to `true`.

### with

A list of tasks that will be ran alongside this task. This is most useful for long-running tasks that you want to ensure always run at the same time.

```
{
  "tasks": {
    "dev": {
      "with": ["api#dev"],
      "persistent": true,
      "cache": false
    }
  }
}
```

## Boundaries

The `boundaries` tag allows you to define rules for the [`boundaries` command](boundaries.md).

```
{
  "boundaries": {}
}
```

Each key in the `tags` object is the name of a tag that can be checked with [`turbo boundaries`](boundaries.md).

In the configuration object for a tag, you can define rules for dependencies and dependents.

#### dependencies and dependents

Rules for a tag's dependencies and dependents.

You can add an allowlist and a denylist:

```
{
  "boundaries": {
    "utils": {
      "dependencies": {
        // permit only packages with the \`ui\` tag
        "allow": ["ui"],
        // and ban packages with the \`unsafe\` tag
        "deny": ["unsafe"],
      },
    },
  },
}
```

Both the allowlist and the denylist can be omitted.

```
{
  "boundaries": {
    "utils": {
      "dependencies": {
        // only packages with the \`unsafe\` tag are banned, all other packages permitted
        "deny": ["unsafe"],
      },
    },
  },
}
```

Rules can also be added for a tag's dependents, i.e. packages that import this tag.

```
{
  "boundaries": {
    "utils": {
      "dependents": {
        // only packages with the \`web\` tag can import packages with the \`utils\` tag
        "allow": ["web"],
      },
    },
  },
}
```

## Remote caching

The global `remoteCache` option has a variety of fields for configuring remote cache usage

```
{
  "remoteCache": {},
}
```

### enabled

Default: `true`

Enables remote caching.

When `false`, Turborepo will disable all remote cache operations, even if the repo has a valid token. If true, remote caching is enabled, but still requires the user to login and link their repo to a remote cache.

### signature

Default: `false`

Enables signature verification for requests to the remote cache. When `true`, Turborepo will sign every uploaded artifact using the value of the environment variable `TURBO_REMOTE_CACHE_SIGNATURE_KEY`. Turborepo will reject any downloaded artifacts that have an invalid signature or are missing a signature.

This is not a security feature. Rather, it verifies the integrity of a cached artifact. This is a defense-in-depth measure against events like partial uploads or downloads with the Remote Cache. Turborepo natively tries to handle these situations where it can, but issues can still happen with the Remote Cache server itself.

### preflight

Default: `false`

When enabled, any HTTP request will be preceded by an OPTIONS request to determine if the request is supported by the endpoint.

### timeout

Default: `30`

Sets a timeout for remote cache operations. Value is given in seconds and only whole values are accepted. If `0` is passed, then there is no timeout for any cache operations.

### uploadTimeout

Default: `60`

Sets a timeout for remote cache uploads. Value is given in seconds and only whole values are accepted. If `0` is passed, then there is no timeout for any remote cache uploads.

### apiUrl

Default: `"https://vercel.com"`

Set endpoint for API calls to the remote cache.

### loginUrl

Default: `"https://vercel.com"`

Set endpoint for requesting tokens during `turbo login`.

### teamId

The ID of the Remote Cache team. Value will be passed as `teamId` in the querystring for all Remote Cache HTTP calls. Must start with `team_` or it will not be used.

### teamSlug

The slug of the Remote Cache team. Value will be passed as `slug` in the querystring for all Remote Cache HTTP calls.

## Experimental observability

Experimental

Configure Turborepo to export metrics to observability backends like Datadog, Prometheus, or other OTLP-compatible collectors.

### experimentalObservability

The `experimentalObservability` configuration requires [`futureFlags.experimentalObservability`](#experimentalobservability) to be set to `true` in your root `turbo.json`. This applies to all configuration sources, including the `turbo.json` block, environment variables, and CLI flags. Turborepo will error if observability is configured without the future flag enabled.

```
{
  "futureFlags": {
    "experimentalObservability": true,
  },
  "experimentalObservability": {
    "otel": {
      "enabled": true,
      "protocol": "grpc",
      "endpoint": "https://api.datadoghq.com/api/v2/otlp",
      "headers": {
        "X-Custom-Header": "value",
      },
      "timeoutMs": 10000,
      "intervalMs": 15000,
      "resource": {
        "service.name": "turborepo",
      },
      "metrics": {
        "runSummary": true,
        "taskDetails": false,
        "runAttributes": {
          "id": false,
          "scmRevision": false,
        },
        "taskAttributes": {
          "id": false,
          "hashes": false,
        },
      },
      "useRemoteCacheToken": true,
    },
  },
}
```

#### experimentalObservability.otel.enabled

Default: `true` (when endpoint is provided)

Enable or disable the OpenTelemetry metrics exporter.

#### experimentalObservability.otel.protocol

Default: `"grpc"`

The OTLP protocol to use. Supported values:

- `"grpc"` - OTLP over gRPC
- `"http/protobuf"` - OTLP over HTTP with protobuf encoding

#### experimentalObservability.otel.endpoint

**Required** when using file-based configuration.

The OTLP collector endpoint URL. For example:

- Datadog: `"https://api.datadoghq.com/api/v2/otlp"`
- Custom collector: `"https://otel-collector.example.com:4317"` (gRPC) or `"https://otel-collector.example.com:4318"` (HTTP)

If the endpoint is missing or empty when OTEL is enabled, the exporter will not be initialized and metrics will be disabled. The run will continue normally.

#### experimentalObservability.otel.headers

Optional HTTP headers to include with export requests. Useful for authentication (e.g., API keys) or custom metadata.

```
{
  "experimentalObservability": {
    "otel": {
      "headers": {
        "X-Custom-Header": "value",
      },
    },
  },
}
```

#### experimentalObservability.otel.timeoutMs

Default: `10000` (10 seconds)

Timeout in milliseconds for export requests to the collector.

#### experimentalObservability.otel.intervalMs

Default: `15000` (15 seconds)

Interval in milliseconds between periodic exports to the collector. This controls how frequently metrics are batched and sent to the OTLP collector during a run. A final flush is always performed when the run completes, regardless of this setting.

#### experimentalObservability.otel.resource

Optional resource attributes to attach to all exported metrics. These help identify the source of metrics in your observability platform.

```
{
  "experimentalObservability": {
    "otel": {
      "resource": {
        "service.name": "turborepo",
        "service.namespace": "ci",
        "deployment.environment": "production",
      },
    },
  },
}
```

#### experimentalObservability.otel.metrics

Control which metric groups are exported.

##### metrics.runSummary

Default: `true`

Export run-level metrics:

- Run duration
- Tasks attempted, failed, and cached
- Exit code
- SCM branch

##### metrics.taskDetails

Default: `false`

Export per-task metrics:

- Task execution duration
- Cache hit/miss status and source
- Task identifiers (task ID, package, hash)

##### metrics.runAttributes

Control which run attributes are included in run-level metrics. These options only take effect when `runSummary` is enabled.

###### runAttributes.id

Default: `false`

Include the `turbo.run.id` attribute on run-level metrics. Each Turborepo invocation generates a unique run ID, so enabling this increases metric cardinality.

###### runAttributes.scmRevision

Default: `false`

Include the `turbo.scm.revision` attribute (full Git SHA) on run-level metrics. Each commit produces a unique value, so enabling this increases metric cardinality.

##### metrics.taskAttributes

Control which task attributes are included in per-task metrics. These options only take effect when `taskDetails` is enabled.

###### taskAttributes.id

Default: `false`

Include the `turbo.task.id` attribute (`package#task`) on per-task metrics.

###### taskAttributes.hashes

Default: `false`

Include the `turbo.task.hash` and `turbo.task.external_inputs_hash` attributes on per-task metrics. These are content hashes that change whenever task inputs change, so enabling this increases metric cardinality.

#### experimentalObservability.otel.useRemoteCacheToken

Default: `false`

When enabled, automatically adds an `Authorization: Bearer <token>` header to OTLP export requests using your existing remote cache credentials (from `turbo login` or the `TURBO_TOKEN` environment variable). Existing `Authorization` headers in the `headers` configuration are preserved and take precedence.

This is useful when your OTLP collector uses the same authentication as your remote cache.

### Multi-source configuration

When OTEL settings come from multiple sources (e.g., `turbo.json` and environment variables), Turborepo deep-merges them per-field. Higher-priority sources win for each field they set, and unset fields fall through from lower-priority sources.

Priority order (highest to lowest):

1. CLI flags
2. Environment variables
3. `turbo.json`

For example, if `turbo.json` configures `endpoint`, `enabled`, and `metrics`, and an environment variable overrides just `endpoint`, the `enabled` and `metrics` values from `turbo.json` are preserved.

### Environment variables

You can also configure observability via environment variables, which take precedence over `turbo.json` settings:

- `TURBO_EXPERIMENTAL_OTEL_ENABLED` - Enable/disable exporter (`1` or `0`)
- `TURBO_EXPERIMENTAL_OTEL_PROTOCOL` - Protocol (`grpc` or `http/protobuf`)
- `TURBO_EXPERIMENTAL_OTEL_ENDPOINT` - Collector HTTPS endpoint URL
- `TURBO_EXPERIMENTAL_OTEL_TIMEOUT_MS` - Timeout in milliseconds
- `TURBO_EXPERIMENTAL_OTEL_INTERVAL_MS` - Export interval in milliseconds
- `TURBO_EXPERIMENTAL_OTEL_HEADERS` - Comma-separated key=value pairs (e.g., `"Header=value,Other=value"`)
- `TURBO_EXPERIMENTAL_OTEL_RESOURCE` - Comma-separated key=value pairs for resource attributes
- `TURBO_EXPERIMENTAL_OTEL_METRICS_RUN_SUMMARY` - Enable run summary metrics (`1` or `0`)
- `TURBO_EXPERIMENTAL_OTEL_METRICS_TASK_DETAILS` - Enable task details metrics (`1` or `0`)
- `TURBO_EXPERIMENTAL_OTEL_METRICS_TASK_ATTRIBUTES_ID` - Include task ID attribute (`1` or `0`)
- `TURBO_EXPERIMENTAL_OTEL_METRICS_TASK_ATTRIBUTES_HASHES` - Include task hash attributes (`1` or `0`)
- `TURBO_EXPERIMENTAL_OTEL_USE_REMOTE_CACHE_TOKEN` - Use remote cache token for OTLP authentication (`1` or `0`)

### CLI flags

You can override observability settings via CLI flags:

- `--experimental-otel-enabled` - Enable/disable exporter
- `--experimental-otel-protocol` - Protocol (`grpc` or `http/protobuf`)
- `--experimental-otel-endpoint` - Collector HTTPS endpoint URL
- `--experimental-otel-timeout-ms` - Timeout in milliseconds
- `--experimental-otel-interval-ms` - Export interval in milliseconds
- `--experimental-otel-header KEY=VALUE` - Add HTTP header (can be repeated)
- `--experimental-otel-resource KEY=VALUE` - Add resource attribute (can be repeated)
- `--experimental-otel-metrics-run-summary` - Enable run summary metrics
- `--experimental-otel-metrics-task-details` - Enable task details metrics
- `--experimental-otel-use-remote-cache-token` - Use remote cache token for OTLP authentication
