---
url: https://turborepo.dev/docs/reference/query
title: "query"
description: "All flags and options for the `turbo query` command that runs GraphQL queries against your monorepo."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

Run GraphQL queries against your monorepo.

```
turbo query [query] [flags]
```

To quickly get the GraphQL schema, use the `--schema` flag.

```
turbo query --schema
```

When no arguments are passed, the command will open a GraphiQL playground to run queries.

```
turbo query
```

When passed a query string, the command will run the query and output the results.

```
turbo query "query { packages { items { name } } }"
```

When passed a file path, the command will read the file and run the query.

```
turbo query query.gql
```

## Shorthands

Shorthands generate GraphQL queries for common operations so you don't need to write them by hand. The JSON output is identical to what you'd get from a raw query.

### ls

List packages in your monorepo. This is a shorthand for a `packages` query, equivalent to `turbo ls`.

```
turbo query ls [packages] [flags]
```

With no arguments, lists all packages:

```
turbo query ls
```

```
{
  "packageManager": "npm",
  "packages": {
    "count": 3,
    "items": [
      { "name": "docs", "path": "apps/docs" },
      { "name": "ui", "path": "packages/ui" },
      { "name": "web", "path": "apps/web" }
    ]
  }
}
```

When passed package names, returns detailed information including tasks, dependencies, and dependents:

```
turbo query ls web
```

#### \--filter (-F)

Use pnpm-style package selectors to narrow the package list. Same syntax as [`turbo run --filter`](run.md#--filter-string).

```
turbo query ls --filter=web...
turbo query ls -F my-app...
```

#### \--affected

Show only packages affected by changes between the current branch and `main`.

When combined with `--filter`, returns the intersection: only packages that are both affected **and** match the filter.

```
turbo query ls --affected
turbo query ls --affected --filter=web
```

#### \--output

Control the output format. Defaults to `pretty` (human-readable).

```
turbo query ls --output json
turbo query ls --output pretty
```

### affected

Check which packages or tasks are affected by changes between two git refs.

```
turbo query affected [flags]
```

With no flags, returns all affected tasks:

```
turbo query affected
```

```
{
  "data": {
    "affectedTasks": {
      "items": [
        {
          "name": "build",
          "fullName": "web#build",
          "package": { "name": "web" },
          "reason": { "__typename": "TaskFileChanged" }
        }
      ],
      "length": 1
    }
  }
}
```

Task-level detection is more precise than package-level. A task is only reported as affected if its configured [`inputs`](configuration.md#inputs) match a changed file, or if an upstream task dependency is affected.

#### \--tasks

Filter to specific task names. With no values, returns all affected tasks (same as bare `turbo query affected`).

```
turbo query affected --tasks
turbo query affected --tasks build
turbo query affected --tasks build test
```

#### \--packages

Without `--tasks`, returns affected packages instead of tasks. With no values, returns all affected packages. With values, filters to the named packages.

When combined with `--tasks`, both filters apply (intersection) — only tasks matching the task name **and** belonging to the named packages are returned. This lets you check whether a specific task in a specific package changed:

```
turbo query affected --tasks build --packages web
```

```
turbo query affected --packages
turbo query affected --packages web
turbo query affected --packages web docs
```

```
{
  "data": {
    "affectedPackages": {
      "items": [
        {
          "name": "web",
          "path": "apps/web",
          "reason": { "__typename": "FileChanged" }
        }
      ],
      "length": 1
    }
  }
}
```

#### Understanding affected-package reasons

The `reason` object explains why a package is included. Its `__typename` is diagnostic output, not a `turbo.json` configuration value.

Most results are package-specific: a source file or a package's resolved dependencies changed. Some changes must be handled repository-wide because they change a shared input or Turborepo cannot safely determine a narrower scope. A repository-wide reason includes every package in the result.

This is related to, but distinct from, cache behavior. For example, `RootInternalDepChanged` means that a workspace package used by the root package changed. It selects every package when comparing Git revisions and also changes the global hash, so every cacheable task misses cache. For the complete cache model, see [Caching](../crafting-your-repository/caching.md#root-workspace-dependencies).

Use the reason to diagnose why a package was selected:

```
turbo query affected --packages --base main --head HEAD
```

##### Change-detection reason types

| `reason.__typename` | Meaning |
| --- | --- |
| `FileChanged` | A file in this package changed. |
| `LockfileChanged` | The lockfile comparison found added or removed external dependencies for this package. |
| `ConservativeRootLockfileChanged` | The root package is included after a lockfile change because it may depend on a workspace package. |
| `RootInternalDepChanged` | A direct or transitive internal workspace dependency of the root package changed. All packages are selected and the global hash changes. |
| `GlobalDepsChanged` | A file configured in `globalDependencies` changed. All packages are selected. |
| `DefaultGlobalFileChanged` | `turbo.json` or `turbo.jsonc` changed. All packages are selected. |
| `LockfileChangeDetectionFailed` | Turborepo could not safely compare the lockfile versions, so all packages are selected. |
| `LockfileChangedWithoutDetails` | The lockfile changed but its previous content was unavailable, so all packages are selected. |
| `GitRefNotFound` | The requested Git range could not be resolved, so all packages are selected. |
| `ScmError` | Source-control change detection failed, so all packages are selected. |

##### Package-selection reason types

These types describe graph or filter expansion rather than a direct cache invalidation:

| `reason.__typename` | Meaning |
| --- | --- |
| `DependencyChanged` | This package was included because one of its dependencies changed. |
| `DependentChanged` | This package was included because an included package depends on it. |
| `InFilteredDirectory` | The package was selected by a directory filter. |
| `IncludedByFilter` | The package was selected explicitly by a filter or package-qualified task. |
| `RootTask` | A root task was included in the run. |

Run `turbo query --schema` to inspect the exact GraphQL schema supported by your installed Turborepo version.

#### \--base

Base git ref for comparison. Defaults to the auto-detected base (e.g. `GITHUB_BASE_REF` on GitHub Actions, or the merge-base with `main`).

Can also be set with the `TURBO_SCM_BASE` environment variable. When both are provided, `--base` takes precedence.

```
turbo query affected --base main
```

#### \--head

Head git ref for comparison. Defaults to `HEAD`.

Can also be set with the `TURBO_SCM_HEAD` environment variable. When both are provided, `--head` takes precedence.

```
turbo query affected --head my-branch
```

#### \--exit-code

Exit with code `1` when affected packages or tasks are found, `0` when none are found, or `2` on errors. JSON output is still printed to stdout.

We recommend parsing the JSON output directly for most use cases since it gives you the reason for each change and lets you make more nuanced decisions. `--exit-code` is available as a shorthand for simple cases.

```
turbo query affected --packages my-app --exit-code
```

| Condition | Exit code |
| --- | --- |
| Nothing affected | `0` |
| Affected packages or tasks found | `1` |
| Query error | `2` |

### Migrating from turbo-ignore

`turbo-ignore` is deprecated. `turbo query affected` is its replacement, with more precise task-level change detection that respects your [`inputs`](configuration.md#inputs) configuration.

#### Flag mapping

| `turbo-ignore` | `turbo query affected` |
| --- | --- |
| `npx turbo-ignore my-app` | `turbo query affected --packages my-app` |
| `--task build` | `--tasks build` |
| `--fallback main` | `--base main` |

#### Key differences

- **More precise detection**: `turbo-ignore` operates at the package level. `turbo query affected` operates at the task input level, so a `.md` change won't trigger a rebuild if your task excludes `*.md` files via `inputs`.
- **Structured output**: The JSON output includes the reason each package or task is affected, which is useful for debugging and automation.

#### CI example

```
affected=$(turbo query affected --packages my-app)
count=$(echo "$affected" | jq '.data.affectedPackages.length')

if [ "$count" -gt 0 ]; then
  echo "my-app is affected, proceeding with build"
else
  echo "my-app is not affected, skipping"
  exit 0
fi
```

## Flags

### \--schema

Output the GraphQL introspection schema. Cannot be used with a query argument.

```
turbo query --schema
```

### \--variables (-V)

Path to a JSON file containing query variables. Requires a query argument.

```
turbo query query.gql --variables vars.json
```
