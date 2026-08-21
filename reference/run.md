---
url: https://turborepo.dev/docs/reference/run
title: "run"
description: "All flags and options for the `turbo run` command used to execute tasks."
access_date: 2026-08-21T13:25:13.609Z
current_date: 2026-08-21T13:25:13.609Z
---

Run tasks specified in `turbo.json`.

```
turbo run [tasks] [options] [-- [args passed to tasks]]
```

- **\[tasks\]**: Turborepo can run one or many tasks at the same time. To run a task through `turbo`, it must be specified in `turbo.json`.
- **\[options\]**: Options are used to control the behavior of the `turbo run` command. Available flag options are described below.
- **\[-- \[args passed to tasks\]\]**: You may also pass arguments to the underlying scripts. Note that all arguments will be passed to all tasks that are named in the run command.

If no tasks are provided, `turbo` will display what tasks are available for the packages in the repository.

```
turbo run
```

## Options

### \--affected

Filter to only packages that are affected by changes on the current branch.

```
turbo run build lint test --affected
```

When combined with `--filter`, only packages matching **both** constraints are selected. This is useful for narrowing a CI run to affected packages within a specific scope:

```
# Only run build for "web" if it's affected by changes
turbo run build --affected --filter=web

# Run affected packages, excluding "docs"
turbo run build --affected --filter=!docs
```

By default, the flag is equivalent to `--filter=...[main...HEAD]`. This considers changes between `main` and `HEAD` from Git's perspective.

You can override the default base and head with their respective [System Environment Variables](system-environment-variables.md).

```
# Override Git comparison base
TURBO_SCM_BASE=development turbo run build --affected

# Override Git comparison head
TURBO_SCM_HEAD=your-branch turbo run build --affected
```

By default, `--affected` operates at the package level: if any file in a package changed, all of its tasks are selected. Enable [`futureFlags.affectedUsingTaskInputs`](configuration.md#affectedusingtaskinputs) to filter at the task level using each task's [`inputs`](configuration.md#inputs) globs instead.

### \--cache <options>

Default: `local:rw,remote:rw`

Specify caching sources for the run. Accepts a comma-separated list of options:

- `local`: Use the local filesystem cache
- `remote`: Use the Remote Cache

When a caching source is omitted, reading and writing are both disabled.

Cache sources use the following values:

- `rw`: Read and write
- `r`: Read only
- `w`: Write only
- None (`local:`): Does not use cache. Equivalent to omitting the cache source option.

```
# Read from and write to local cache. Only read from Remote Cache.
turbo run build --cache=local:rw,remote:r

# Only read from local cache. Read from and write to Remote Cache.
turbo run build --cache=local:r,remote:rw

# Read from and write to local cache. No Remote Cache activity.
turbo run build --cache=local:rw

# Do not use local cache. Only read from Remote Cache.
turbo run build --cache=local:,remote:r
```

### \--cache-dir <path>

Default: `.turbo/cache`

Specify the filesystem cache directory.

```
turbo run build --cache-dir="./my-cache"
```

The same behavior can also be set via the `TURBO_CACHE_DIR=example/path` system variable.

### \--concurrency <number | percentage>

Default: `10`

Set/limit the maximum concurrency for task execution. Must be an integer greater than or equal to `1` or a percentage value like `50%`.

- Use `1` to force serial execution (one task at a time).
- Use `100%` to use all available logical processors.
- This option is ignored if the [`--parallel`](#--parallel) flag is also passed.

```
turbo run build --concurrency=50%
turbo run test --concurrency=5
```

### \--continue\[=<option>\]

Default: `never`

Specify how `turbo` should handle current and pending tasks in the presence of an error (e.g. non-zero exit code from a task).

- When `--continue=never` and an error occurs, `turbo` will cancel all tasks.
- When `--continue=dependencies-successful` and an error occurs, `turbo` will cancel dependent tasks. Tasks whose dependencies have succeeded will continue to run.
- When `--continue=always` and an error occurs, `turbo` will continue running all tasks, even those whose dependencies have failed.
- When `--continue` is specified without a value, it will default to `always`.

In all cases, `turbo` will exit with the highest exit code value encountered during execution.

```
turbo run build --continue
```

### \--cwd <path>

Default: Directory of root `turbo.json`

Set the working directory of the command.

```
turbo run build --cwd=./somewhere/else/in/your/repo
```

### \--dangerously-disable-package-manager-check

Turborepo uses your repository's lockfile to determine caching behavior, [Package Graphs](../core-concepts/internal-packages.md), and more. Because of this, we use the package manager declaration in your root `package.json` to help you stabilize your Turborepo. We recommend `devEngines.packageManager`; the legacy top-level [`packageManager`](https://nodejs.org/api/packages.html#packagemanager) field is also supported.

To help with incremental migration or in situations where you cannot use a package manager declaration, you may use `--dangerously-disable-package-manager-check` to opt out of this check and assume the risks of unstable lockfiles producing unpredictable behavior. When disabled, Turborepo will attempt a best-effort discovery of the intended package manager meant for the repository.

### \--dry / --dry-run

Instead of executing tasks, display details about the packages and tasks that would be run.

Specify `--dry=json` to get the output in JSON format.

Task details include useful information like (list is non-exhaustive):

| Field | Description |
| --- | --- |
| `taskId` | ID for the task, in the format of `package-name#task-name` |
| `task` | The name of the task to be executed |
| `package` | The package in which to run the task |
| `hash` | The hash of the task (used for caching) |
| `hashOfExternalDependencies` | The global hash |
| `command` | The command used to run the task |
| `inputs` | List of file inputs considered for hashing |
| `outputs` | List of file outputs that were cached |
| `dependencies` | Tasks that must run **before** this task |
| `dependents` | Tasks that must run **after** this task |
| `environmentVariables` | Lists of environment variables specified in `env` and `passThroughEnv` |

### \--env-mode <option>

`type: string`

Controls the available environment variables in the task's runtime.

| option | description |
| --- | --- |
| [strict](#strict) (Default) | Only allow explicitly listed environment variables to be available |
| [loose](#loose) | Allow **all** environment variables to be available |

```
turbo run build --env-mode=loose
```

The same behavior can also be set via the `TURBO_ENV_MODE=strict` system variable.

#### strict

Only environment variables specified in the following keys are available to the task:

- [`env`](configuration.md#env)
- [`passThroughEnv`](configuration.md#passthroughenv)
- [`globalEnv`](configuration.md#globalenv)
- [`globalPassThroughEnv`](configuration.md#globalpassthroughenv)

If Strict Mode is specified or inferred, **all** tasks are run in `strict` mode, regardless of their configuration.

#### loose

All environment variables on the machine are made available to the task's runtime.

### \--filter <string>

Specify targets to execute from your repository's graph. Multiple filters can be combined to select distinct sets of targets.

Filters can be combined to create combinations of packages, directories, and git commits.

| Target type | Description | Example |
| --- | --- | --- |
| Package | Select a package by its name in `package.json`. | `turbo run build --filter=ui` |
| Directory | Specify directories to capture a list of packages to run tasks. **When used with other filters, must be wrapped in `{}`**. | `turbo run build --filter=./apps/*` |
| Git commits | Using Git specifiers, specify packages with source control changes. **Must be wrapped in `[]`**. | `turbo run build --filter=[HEAD^1]` |

#### Microsyntaxes for filtering

- `!`: Negate targets from the selection.
- `...` using packages: Select all packages in the [Package Graph](../core-concepts/package-and-task-graph.md#package-graph) relative to the target. Using `...` **before** the package name will select **dependents** of the target while using `...` **after** the package name will select **dependencies** of the target.
- `...` using Git commits: Select a range using `[<from commit>]...[<to commit>]`.
- `^`: Omit the target from the selection when using `...`.

For in-depth discussion and practical use cases of filtering, visit [the Running Tasks page](../crafting-your-repository/running-tasks.md).

#### Using a task identifier

You can also run a specific task for a specific package in the format of `package-name#task-name`.

```
turbo run web#lint
```

#### Advanced filtering examples

You can combine multiple filters to further refine your targets. Multiple filters are combined as a union, with negated filters removing packages from the result of the union.

```
# Any packages in \`apps\` subdirectories that have changed since the last commit
turbo run build --filter={.apps/*}[HEAD^1]

# Any packages in \`apps\` subdirectories except ./apps/admin
turbo run build --filter=./apps/* --filter=!./apps/admin

# Run the build task for the docs and web packages
turbo run build --filter=docs --filter=web

# Build everything that depends on changes in branch 'my-feature'
turbo run build --filter=...[origin/my-feature]

# Build everything that depends on changes between two Git SHAs
turbo run build --filter=[a1b2c3d...e4f5g6h]

# Build '@acme/ui' if:
# - It or any of its dependencies have changed since the previous commit
turbo run build --filter=@acme/ui...[HEAD^1]

# Test each package that is:
# - In the '@acme' scope
# - Or, in the 'packages' directory
# - Or, changed since the previous commit
turbo run test --filter=@acme/*{./packages/*}[HEAD^1]
```

### \--force

Ignore existing cached artifacts and re-execute all tasks.

```
turbo run build --force
```

The same behavior can also be set via [the `TURBO_FORCE` environment variable](system-environment-variables.md).

### \--framework-inference

Default: `true`

Specify whether or not to do [Framework Inference](../crafting-your-repository/using-environment-variables.md#framework-inference) for tasks.

When `false`, [automatic environment variable inclusion](../crafting-your-repository/using-environment-variables.md#framework-inference) is disabled.

```
turbo run build --framework-inference=false
```

### \--global-deps <file glob>

Specify glob of global filesystem dependencies to be hashed. Useful for `.env` and files in the root directory that impact multiple packages.

```
turbo run build --global-deps=".env"
turbo run build --global-deps=".env.*" --global-deps=".eslintrc" --global-deps="jest.config.ts"
```

### \--graph <file name>

Default: `dot`

This command can output a graph file: `svg`, `html`, `mermaid`, or `dot`.

If no filename is provided, this command prints the dot graph to `stdout`.

```
turbo run build --graph
turbo run build test lint --graph=my-graph.svg
```

Deprecated formats

### \--json Experimental

Output machine-readable [NDJSON](https://github.com/ndjson/ndjson-spec) to stdout instead of human-readable text. Disables the TUI and forces stream mode.

```
turbo run build --json
```

Each line is a JSON object with the following shape:

```
{"timestamp":1710000000000,"source":"web#build","level":"stdout","text":"Compiled successfully"}
```

| Field | Description |
| --- | --- |
| `timestamp` | Unix epoch milliseconds |
| `source` | `"turbo"` for Turborepo messages, or `"<package>#<task>"` for task output |
| `level` | `"info"`, `"warn"`, `"error"` for Turborepo messages; `"stdout"`, `"stderr"` for task output |
| `text` | The message content, with ANSI escape sequences stripped |

Can be combined with `--log-file` to write to both stdout and a file simultaneously.

### \--log-file \[path\] Experimental

Write structured JSON logs to a file. If no path is given, writes to `.turbo/logs/<timestamp>.json`.

```
# Default location
turbo run build --log-file

# Custom path
turbo run build --log-file=build-log.json
```

The file is a valid JSON array at all times, even if the process is interrupted. Log files are created with owner-only permissions (`0600`) on Unix, and the path is restricted to the repository root.

Can also be set with the [`TURBO_LOG_FILE`](system-environment-variables.md#turbo_log_file) environment variable. The CLI flag takes precedence.

### \--log-order <option>

Default: `auto`

Set the ordering for log output.

By default, `turbo` will use `grouped` logs in CI environments and `stream` logs everywhere else. This flag is not applicable when using [the terminal UI](configuration.md#ui).

```
turbo run build --log-order=stream
```

| Option | Description |
| --- | --- |
| `stream` | Show output as soon as it is available |
| `grouped` | Group output by task |
| `auto` | `turbo` decides based on its own heuristics |

### \--log-prefix <option>

Default: `auto`

Control the `<package>:<task>:` prefix for log lines produced when running tasks.

```
turbo run dev --log-prefix=none
```

| Option | Description |
| --- | --- |
| `task` | Force prepending the `<package>:<task>:` prefix to logs |
| `none` | No prefixes |
| `auto` | `turbo` decides based on its own heuristics |

### \--no-cache

Deprecated

Default `false`

Do not cache results of the task.

```
turbo run dev --no-cache
```

### \--daemon and --no-daemon

**Deprecated**: The daemon is no longer used for `turbo run`. These flags are ignored and will be removed in version 3.0.

The daemon is still used by `turbo watch` and the Turborepo LSP.

### \--output-logs <option>

Default: `full`

Set type of output logging, overriding [`outputLogs`](configuration.md#outputlogs) if it's defined in `turbo.json`.

```
turbo run build --output-logs=errors-only
```

| Option | Description |
| --- | --- |
| `full` | Displays all logs |
| `hash-only` | Only show the hashes of the tasks |
| `new-only` | Only show logs from cache misses |
| `errors-only` | Only show logs from task failures |
| `none` | Hides all task logs |

### \--only

Default: `false`

Restricts execution to include specified tasks only.

#### Example

Given this `turbo.json`:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    },
    "test": {
      "dependsOn": ["^build"]
    }
  }
}
```

```
turbo run test --only
```

The command will *only* execute the `test` tasks in each package. It will not run `build`.

Additionally, `--only` will only run tasks in specified packages, excluding dependencies. For example, `turbo run build --filter=web --only`, will **only** run the `build` script in the `web` package.

### \--parallel

Deprecated

Default: `false`

Run commands in parallel across packages, ignoring the task dependency graph.

```
turbo run lint --parallel
turbo run dev --parallel
```

#### Migrating off --parallel

There are several alternatives depending on your use case:

**Long-running dev tasks**: Use [`persistent`](configuration.md#persistent) and [`with`](configuration.md#with) in your task definitions.

```
{
  "tasks": {
    "dev": {
      "persistent": true,
      "cache": false
    }
  }
}
```

If you need to ensure certain tasks always run together (e.g. a frontend and an API), use `with` in a [Package Configuration](package-configurations.md):

```
{
  "extends": ["//"],
  "tasks": {
    "dev": {
      "with": ["api#dev"],
      "persistent": true,
      "cache": false
    }
  }
}
```

**High concurrency**: If you were using `--parallel` to remove the concurrency limit, use [`--concurrency`](run.md#--concurrency-number--percentage) instead (e.g. `--concurrency=100%`).

### \--preflight

Only applicable when Remote Caching is configured. Enables sending a preflight request before every cache artifact and analytics request. The follow-up upload and download will follow redirects.

```
turbo run build --preflight
```

The same behavior can also be set via the `TURBO_PREFLIGHT=true` system variable.

### \--profile

Produces two traces of the run for analyzing performance.

- Chrome Tracing format: Visually inspect a trace in a tool like [Perfetto](https://ui.perfetto.dev/)
- Markdown format: Output a Markdown file to simplify consumption for LLMs

```
# Produces a file with a timestamp for name
turbo run build --profile

# Produces a file with provided name
turbo run build --profile=my-profile
```

### \--anon-profile

This flag works the same as `--profile`, but attempts to redacts sensitive information. This is useful for sharing your profiles.

Recommended: Make sure to check that `--anon-profile` has redacted all information that you would like to keep secret, like file paths, environment variables, or any identifying information.

### \--remote-cache-timeout

Default: `30`

Set the timeout for Remote Cache operations in seconds.

```
turbo run build --remote-cache-timeout=60
```

### \--remote-only

Deprecated

Default: `false`

Ignore the local filesystem cache for all tasks, using Remote Cache for reading and caching task outputs.

```
turbo run build --remote-only
```

### \--summarize

Generates a JSON file in `.turbo/runs` containing metadata about the run, including:

- Affected packages
- Executed tasks (including their timings and hashes)
- All the files included in the cached artifact

```
turbo run build --summarize
```

This flag can be helpful for debugging to determine things like:

- How `turbo` interpreted your glob syntax for `inputs` and `outputs`
- What inputs changed between two task runs to produce a cache miss
- How task timings changed over time

Summaries viewer

### \--token

A bearer token for Remote Caching. Useful for running in non-interactive shells in combination with the `--team` flag.

```
turbo run build --team=my-team --token=xxxxxxxxxxxxxxxxx
```

This value can also be set using [the `TURBO_TOKEN` system variable](system-environment-variables.md). If both are present, the flag value will override the system variable.

### \--team

The slug of the Remote Cache team. Useful for running in non-interactive shells in combination with the `--token` flag.

```
turbo run build --team=my-team
turbo run build --team=my-team --token=xxxxxxxxxxxxxxxxx
```

This value can also be set using [the `TURBO_TEAM` system variable](system-environment-variables.md). If both are present, the flag value will override the system variable.

### \--ui

Specify the UI to use for output. Accepts `stream` or `tui`.

### \--verbosity

To specify log level, use `--verbosity=<num>` or `-v, -vv, -vvv`.

| Level | Flag value | Shorthand |
| --- | --- | --- |
| Info | `--verbosity=1` | `-v` |
| Debug | `--verbosity=2` | `-vv` |
| Trace | `--verbosity=3` | `-vvv` |

```
turbo run build --verbosity=2
turbo run build -vvv
```
