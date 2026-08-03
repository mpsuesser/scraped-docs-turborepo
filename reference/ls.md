---
url: https://turborepo.dev/docs/reference/ls
title: "ls"
description: "All flags and options for the `turbo ls` command that lists packages in your monorepo."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

List packages in your monorepo.

```
turbo ls [package(s)] [flags]
```

When scoped to the entire repository, output includes package manager, package count, and all package names and directories.

```
# List all packages in the repository
turbo ls
```

When scoped to one or more packages, output includes package name, directory, internal dependencies, and all tasks.

```
# List only two packages
turbo ls web @repo/ui [package(s)]
```

## Flags

### \--affected

Automatically filter to only packages that are affected by changes on the current branch.

When combined with `--filter`, returns the intersection: only packages that are both affected **and** match the filter.

```
turbo ls --affected
turbo ls --affected --filter=web
```

By default the changes considered are those between `main` and `HEAD`.

- You can override `main` as the default base by setting `TURBO_SCM_BASE`.
- You can override `HEAD` as the default head by setting `TURBO_SCM_HEAD`.

```
TURBO_SCM_BASE=development turbo ls --affected
```

### \--output <format> Experimental

Format to output the results. `json` or `pretty` (default)

```
turbo ls --output=json
```
