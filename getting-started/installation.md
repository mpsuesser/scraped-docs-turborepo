---
url: https://turborepo.dev/docs/getting-started/installation
title: "Installation"
description: "Learn how to get started with Turborepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Get started with Turborepo in a few moments using:

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest
```

#### npm

```bash title="Terminal"
npx create-turbo@latest
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest
```

The starter repository will have:

- Two deployable applications
- Three shared libraries for use in the rest of the monorepo

For more details on the starter, [visit the README for the basic starter on GitHub](https://github.com/vercel/turborepo/tree/main/examples/basic). You can also [use an example](examples.md) that more closely fits your tooling interests.

## Installing `turbo`

You can install `turbo` globally and pin a version in your repository. When a local binary is installed, the global binary defers to it.

### Global installation

A global install of `turbo` brings flexibility and speed to your local workflows.

#### macOS and Linux

```sh title="Terminal"
curl -fsSL https://turborepo.dev/install | sh
```

#### Windows x64

```powershell title="PowerShell"
irm https://turborepo.dev/install.ps1 | iex
```

> See the [support policy](../support-policy.md#platforms) for supported platforms.

> The binaries are also available directly in the [GitHub release assets](https://github.com/vercel/turborepo/releases), starting with 2.11.5. To install an older version, use your package manager.

After installing `turbo`, you can run commands from your terminal. For example:

- `turbo build`: Run `build` scripts following your repository's dependency graph
- `turbo build --filter=docs --dry`: Quickly print an outline of the `build` task for your `docs` package (without running it)
- `turbo generate`: Run [Generators](../guides/generating-code.md) to add new code to your repository
- `cd apps/docs && turbo build`: Run the `build` script in the `docs` package and its dependencies. For more, visit the [Automatic Package Scoping section](../crafting-your-repository/running-tasks.md#automatic-package-scoping).

> `turbo` is an alias for [`turbo run`](../reference/run.md). For example,
> `turbo build` and `turbo run build` will both run your `build` task.

#### Using global `turbo` in CI

To use the global binary in CI, see [Constructing CI](../crafting-your-repository/constructing-ci.md#global-turbo-in-ci).

### Repository installation

To pin the `turbo` version used in a repository, add it as a `devDependency` at the root:

#### pnpm

```bash title="Terminal"
pnpm add turbo --save-dev --ignore-workspace-root-check
```

#### yarn

```bash title="Terminal"
yarn add turbo --dev --ignore-workspace-root-check
```

#### npm

```bash title="Terminal"
npm install turbo --save-dev
```

#### bun

```bash title="Terminal"
bun install turbo --dev
```

#### nub

```bash title="Terminal"
nub add turbo --save-dev
```

#### aube

```bash title="Terminal"
aube add turbo --save-dev
```

When you invoke the global binary from this repository, it uses the installed local version instead.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
