---
url: https://turborepo.dev/docs/crafting-your-repository/upgrading
title: "Upgrading"
description: "Learn how to upgrade `turbo` to get the latest improvements to your repository."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

## Upgrading to 2.0

### 1. Update `turbo.json`

Get started upgrading from 1.x to 2.0 by running:

#### pnpm

```bash title="Terminal"
pnpm dlx @turbo/codemod migrate
```

#### yarn

```bash title="Terminal"
yarn dlx @turbo/codemod migrate
```

#### npm

```bash title="Terminal"
npx @turbo/codemod migrate
```

#### bun

```bash title="Terminal"
bunx @turbo/codemod migrate
```

#### nub

```bash title="Terminal"
nubx @turbo/codemod migrate
```

#### aube

```bash title="Terminal"
aubx @turbo/codemod migrate
```

This will update your `turbo.json`(s) for many of the breaking changes from 1.x to 2.0.

Additionally, a `name` field will be added to any `package.json` in the Workspace that doesn't have one.

> You may also manually run each codemod individually. Visit [the codemods
> page](../reference/turbo-codemod.md#turborepo-2x) for more information.

### 2. Add `devEngines.packageManager` to root `package.json`

The `devEngines.packageManager` field declares which package manager is expected to be used in the Workspace. The legacy top-level [`packageManager`](https://nodejs.org/api/packages.html#packagemanager) field is also supported for backwards compatibility.

Turborepo 2.0 requires that your Workspace define a package manager declaration as a way to improve the stability and behavioral predictability of your codebase. If you do not have one already, add this field to your root `package.json`:

#### pnpm

```diff title="./package.json"
{
+ "devEngines": {
+   "packageManager": {
+     "name": "pnpm",
+     "version": "9.2.0"
+   }
+ }
}
```

#### yarn

```diff title="./package.json"
{
+ "devEngines": {
+   "packageManager": {
+     "name": "yarn",
+     "version": "1.22.19"
+   }
+ }
}
```

#### npm

```diff title="./package.json"
{
+ "devEngines": {
+   "packageManager": {
+     "name": "npm",
+     "version": "10.8.1"
+   }
+ }
}
```

#### bun

```diff title="./package.json"
{
+ "devEngines": {
+   "packageManager": {
+     "name": "bun",
+     "version": "1.2.0"
+   }
+ }
}
```

#### nub

```diff title="./package.json"
{
+ "devEngines": {
+   "packageManager": {
+     "name": "nub",
+     "version": "0.8.3"
+   }
+ }
}
```

#### aube

```diff title="./package.json"
{
+ "devEngines": {
+   "packageManager": {
+     "name": "aube",
+     "version": "2.2.9"
+   }
+ }
}
```

### 3. Update `eslint-config-turbo`

[`eslint-config-turbo`](../reference/eslint-config-turbo.md) helps identify environment variables that need to be added to the [`env`](../reference/configuration.md#env) key for caching. If you're using it, make sure you update it to match your major version.

### 4. Update `turbo run` commands

Turborepo 2.0 includes behavioral and correctness improvements with behavior of `turbo run` commands. Listed below is the summary of changes, which may or may not have an affect on your codebase:

- Strict Mode for environment variables is now the default, moving from Loose Mode ([PR](https://github.com/vercel/turborepo/pull/8182))
  - If it appears that the scripts in your tasks are missing environment variables, you can opt back out of this behavior using [the `--env-mode` option](../reference/run.md#--env-mode-option) on a per-command basis to incrementally migrate. We encourage you to update [the `env` key](../reference/configuration.md#env) in your task to account for all of its environment variables so you can drop the `--env-mode` option as soon as possible.
  - If you'd like to set the default for the repository back to Loose Mode, you can do so [using the `envMode` configuration](../reference/configuration.md#envmode).
- Workspace root directory is now an implicit dependency of all packages ([PR](https://github.com/vercel/turborepo/pull/8202))
  - The repository should have as little code in the root as possible, since changes to the root can affect all tasks in your repository. Additionally, if you're using [Internal Packages](../core-concepts/internal-packages.md) in the Workspace root, changes to those dependencies will also cause cache misses for all tasks. In both cases, consider moving the code out of the root and [into a package](structuring-a-repository.md).
- `--ignore` removed in favor of `--filter` and graph correctness changes below ([PR](https://github.com/vercel/turborepo/pull/8201))
- Removed `--scope` flag (deprecated since 1.2) ([PR](https://github.com/vercel/turborepo/pull/7970))
- `engines` field in root `package.json` is now used in hashing ([PR](https://github.com/vercel/turborepo/pull/8173))
- `--filter` no longer infers namespaces for package names ([PR](https://github.com/vercel/turborepo/pull/8137))
- `--filter` now errors when no package names or directories are matched ([PR](https://github.com/vercel/turborepo/pull/8142))
- `--only` restricts task dependencies instead of package dependencies ([PR](https://github.com/vercel/turborepo/pull/8163))

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
