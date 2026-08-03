---
url: https://turborepo.dev/docs/reference/prune
title: "prune"
description: "All flags and options for the `turbo prune` command that generates a partial monorepo for a target package."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Generate a partial monorepo for a target package. The output will be placed into a directory named `out` containing the following:

- The full source code of all internal packages needed to build the target.
- A pruned lockfile containing the subset of the original lockfile needed to build the target.
- A copy of the root `package.json`.

```
turbo prune [package]
```

### Example

Starting with a repository with the following structure:

package.json

pnpm-lock.yaml

Run `turbo prune frontend` to generate a pruned workspace for the `frontend` application in an `out` directory:

package.json

pnpm-lock.yaml (partial)

### Options

#### \--docker

Defaults to `false`.

Alter the output directory to make it easier to use with [Docker best practices and layer caching](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/). For an example Dockerfile using this flag, see the [Docker guide](../guides/tools/docker.md). The directory will contain:

- A folder named `json` with the pruned workspace's `package.json` files.
- A folder named `full` with the pruned workspace's full source code for the internal packages needed to build the target.
- A pruned lockfile containing the subset of the original lockfile needed to build the target.

Using the same example from above, running `turbo prune frontend --docker` will generate the following:

pnpm-lock.yaml (partial)

package.json (from repo root)

package.json (from repo root)

package.json

package.json

package.json

#### \--out-dir <path>

Defaults to `./out`.

Customize the directory the pruned output is generated in.

#### \--use-gitignore\[=<bool>\]

Default: `true`

Respect `.gitignore` file(s) when copying files to the output directory.

#### \--production

Default: `false`

Exclude in-workspace packages listed only in `devDependencies` when selecting which packages to include in the pruned output. Only workspace packages reachable through `dependencies` (and non-optional peer workspace dependencies) are included. For Bun repositories, references to excluded workspaces are also removed from the pruned manifests and lockfile so the output can be installed with a frozen lockfile.

### Including globalDependencies

By default, `turbo prune` does not copy files referenced by [`globalDependencies`](configuration.md#globaldependencies) into the output directory. The `globalDependencies` field is preserved in the pruned `turbo.json`, but the files themselves (e.g., a root `tsconfig.json` or `.env`) are not included.

Enable the [`pruneIncludesGlobalFiles`](configuration.md#pruneincludesglobalfiles) future flag to copy these files:

```
{
  "globalDependencies": ["tsconfig.json", ".env"],
  "futureFlags": {
    "pruneIncludesGlobalFiles": true,
  },
}
```

With this flag, all files matching the `globalDependencies` globs will be included in the pruned output. In `--docker` mode, they are copied to both the `full` and `json` directories.

### Comparison to pnpm deploy

While both `turbo prune` and [`pnpm deploy`](https://pnpm.io/cli/deploy) are used to isolate packages in a monorepo, they serve different purposes and produce different outputs.

Where `turbo prune` generates a partial monorepo, `pnpm deploy` generates a directory that only contains the contents of the target package.

The `pnpm deploy` generated directory has a self-contained `node_modules` with hard linked internal dependencies. This results in a portable package that can be directly copied to a server and used without additional steps. The repository structure is not retained, as the focus is on producing a standalone deployable package.
