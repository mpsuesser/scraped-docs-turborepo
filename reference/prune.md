---
url: https://turborepo.dev/docs/reference/prune
title: "prune"
description: "API reference for the `turbo prune` command"
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Generate a partial monorepo for a target package. The output will be placed into a directory named `out` containing the following:

- The full source code of all internal packages needed to build the target.
- A pruned lockfile containing the subset of the original lockfile needed to build the target.
- A copy of the root `package.json`.

```bash title="Terminal"
turbo prune [package]
```

### Task dependencies

By default, `turbo prune` follows package dependencies, not task dependencies. An explicit dependency such as `"dependsOn": ["mycrate#build:types"]` does not by itself retain `mycrate` in the pruned output.

Enable [`futureFlags.affectedUsingTaskInputs`](configuration.md#affectedusingtaskinputs) to also retain packages required by task dependencies, including dependencies across enabled toolchains. Their package dependencies and inherited Package Configurations are retained as well.

The command still accepts package names, not task names. It considers all configured tasks of retained packages, not only `build`. With `--production`, package development dependencies are excluded unless they are otherwise required, for example by an explicit task dependency.

### Example

Starting with a repository with the following structure:

- `package.json`
- `pnpm-lock.yaml`
- `apps/`
  - `admin/`
    - `package.json`
    - `next-env.d.ts`
    - `next.config.js`
    - `src/`
      - `app/`
        - `page.tsx`
    - `tsconfig.json`
  - `frontend/`
    - `package.json`
    - `next-env.d.ts`
    - `next.config.js`
    - `src/`
      - `app/`
        - `page.tsx`
    - `tsconfig.json`
- `packages/`
  - `scripts/`
    - `package.json`
    - `src/`
      - `index.tsx`
    - `tsconfig.json`
  - `shared/`
    - `package.json`
    - `src/`
      - `__tests__/`
        - `sum.test.ts`
        - `tsconfig.json`
      - `index.ts`
      - `sum.ts`
    - `tsconfig.json`
  - `ui/`
    - `package.json`
    - `src/`
      - `index.tsx`
    - `tsconfig.json`
  - `utils/`
    - `package.json`
    - `src/`
      - `index.tsx`
    - `tsconfig.json`

Run `turbo prune frontend` to generate a pruned workspace for the `frontend` application in an `out` directory:

- `package.json`
- `pnpm-lock.yaml (partial)`
- `apps/`
  - `frontend/`
    - `package.json`
    - `next-env.d.ts`
    - `next.config.js`
    - `src/`
      - `app/`
        - `page.tsx`
    - `tsconfig.json`
- `packages/`
  - `shared/`
    - `package.json`
    - `src/`
      - `__tests__/`
        - `sum.test.ts`
        - `tsconfig.json`
      - `index.ts`
      - `sum.ts`
    - `tsconfig.json`
  - `ui/`
    - `package.json`
    - `src/`
      - `index.tsx`
    - `tsconfig.json`

### Options

#### `--docker`

Defaults to `false`.

Alter the output directory to make it easier to use with [Docker best practices and layer caching](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/). For an example Dockerfile using this flag, see the [Docker guide](../guides/tools/docker.md). The directory will contain:

- A folder named `json` with the pruned workspace's `package.json` files.
- A folder named `full` with the pruned workspace's full source code for the internal packages needed to build the target.
- A pruned lockfile containing the subset of the original lockfile needed to build the target.

Using the same example from above, running `turbo prune frontend --docker` will generate the following:

- `pnpm-lock.yaml (partial)`
- `full/`
  - `package.json (from repo root)`
  - `apps/`
    - `frontend/`
      - `package.json`
      - `next-env.d.ts`
      - `next.config.js`
      - `src/`
        - `app/`
          - `page.tsx`
      - `tsconfig.json`
  - `packages/`
    - `shared/`
      - `package.json`
      - `src/`
        - `__tests__/`
          - `sum.test.ts`
          - `tsconfig.json`
        - `index.ts`
        - `sum.ts`
      - `tsconfig.json`
    - `ui/`
      - `package.json`
      - `src/`
        - `index.tsx`
      - `tsconfig.json`
- `json/`
  - `package.json (from repo root)`
  - `apps/`
    - `frontend/`
      - `package.json`
  - `packages/`
    - `ui/`
      - `package.json`
    - `shared/`
      - `package.json`

#### `--out-dir <path>`

Defaults to `./out`.

Customize the directory the pruned output is generated in.

#### `--use-gitignore[=<bool>]`

Default: `true`

Respect `.gitignore` file(s) when copying files to the output directory.

#### `--production`

Default: `false`

Exclude in-workspace packages listed only in `devDependencies` when selecting which packages to include in the pruned output. Only workspace packages reachable through `dependencies` (and non-optional peer workspace dependencies) are included. For Bun repositories, references to excluded workspaces are also removed from the pruned manifests and lockfile so the output can be installed with a frozen lockfile.

### Including `globalDependencies`

By default, `turbo prune` does not copy files referenced by [`globalDependencies`](configuration.md#globaldependencies) into the output directory. The `globalDependencies` field is preserved in the pruned `turbo.json`, but the files themselves (e.g., a root `tsconfig.json` or `.env`) are not included.

Enable the [`pruneIncludesGlobalFiles`](configuration.md#pruneincludesglobalfiles) future flag to copy these files:

```jsonc title="./turbo.json"
{
  "globalDependencies": ["tsconfig.json", ".env"],
  "futureFlags": {
    "pruneIncludesGlobalFiles": true,
  },
}
```

With this flag, all files matching the `globalDependencies` globs will be included in the pruned output. In `--docker` mode, they are copied to both the `full` and `json` directories.

### Comparison to `pnpm deploy`

While both `turbo prune` and [`pnpm deploy`](https://pnpm.io/cli/deploy) are used to isolate packages in a monorepo, they serve different purposes and produce different outputs.

Where `turbo prune` generates a partial monorepo, `pnpm deploy` generates a directory that only contains the contents of the target package.

The `pnpm deploy` generated directory has a self-contained `node_modules` with hard linked internal dependencies.
This results in a portable package that can be directly copied to a server and used without additional steps.
The repository structure is not retained, as the focus is on producing a standalone deployable package.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
