---
url: https://turborepo.dev/docs/reference/ls
title: "ls"
description: "API reference for the `turbo ls` command"
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

# ls



import { ExperimentalBadge } from "@/components/geistdocs/experimental-badge";

List packages in your monorepo.

```bash title="Terminal"
turbo ls [package(s)] [flags]
```

When scoped to the entire repository, output includes package manager, package count, and all package names and directories.

```bash title="Terminal"
# List all packages in the repository
turbo ls
```

When scoped to one or more packages, output includes package name, directory, internal dependencies, and all tasks.

```bash title="Terminal"
# List only two packages
turbo ls web @repo/ui [package(s)]
```

## Flags

### `--affected`

Automatically filter to only packages that are affected by changes on the current branch.

When combined with `--filter`, returns the intersection: only packages that are both affected **and** match the filter.

```bash title="Terminal"
turbo ls --affected
turbo ls --affected --filter=web
```

By default the changes considered are those between `main` and `HEAD`.

* You can override `main` as the default base by setting `TURBO_SCM_BASE`.
* You can override `HEAD` as the default head by setting `TURBO_SCM_HEAD`.

```bash title="Terminal"
TURBO_SCM_BASE=development turbo ls --affected
```

### `--output <format>` <ExperimentalBadge />

Format to output the results. `json` or `pretty` (default)

```bash title="Terminal"
turbo ls --output=json
```


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
