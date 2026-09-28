---
url: https://turborepo.dev/docs/getting-started/installation
title: "Installation"
description: "Install Turborepo globally and in your repository."
access_date: 2026-09-28T04:49:15.786Z
current_date: 2026-09-28T04:49:15.786Z
---

Learn how to get started with Turborepo.

Get started with Turborepo in a few moments using:

#### pnpm

```
pnpm dlx create-turbo@latest
```

#### yarn

```
yarn dlx create-turbo@latest
```

#### npm

```
npx create-turbo@latest
```

#### bun

```
bunx create-turbo@latest
```

#### nub

```
nubx create-turbo@latest
```

#### aube

```
aubx create-turbo@latest
```

The starter repository will have:

- Two deployable applications
- Three shared libraries for use in the rest of the monorepo

For more details on the starter, [visit the README for the basic starter on GitHub](https://github.com/vercel/turborepo/tree/main/examples/basic). You can also [use an example](examples.md) that more closely fits your tooling interests.

## Installing turbo

You can install `turbo` globally and pin a version in your repository. When a local binary is installed, the global binary defers to it.

### Global installation

A global install of `turbo` brings flexibility and speed to your local workflows.

#### macOS and Linux

```
curl -fsSL https://turborepo.dev/install | sh
```

#### Windows x64

```
irm https://turborepo.dev/install.ps1 | iex
```

After installing `turbo`, you can run commands from your terminal. For example:

- `turbo build`: Run `build` scripts following your repository's dependency graph
- `turbo build --filter=docs --dry`: Quickly print an outline of the `build` task for your `docs` package (without running it)
- `turbo generate`: Run [Generators](../guides/generating-code.md) to add new code to your repository
- `cd apps/docs && turbo build`: Run the `build` script in the `docs` package and its dependencies. For more, visit the [Automatic Package Scoping section](../crafting-your-repository/running-tasks.md#automatic-package-scoping).

#### Using global turbo in CI

To use the global binary in CI, see [Constructing CI](../crafting-your-repository/constructing-ci.md#global-turbo-in-ci).

### Repository installation

To pin the `turbo` version used in a repository, add it as a `devDependency` at the root:

#### pnpm

```
pnpm add turbo --save-dev --ignore-workspace-root-check
```

#### yarn

```
yarn add turbo --dev --ignore-workspace-root-check
```

#### npm

```
npm install turbo --save-dev
```

#### bun

```
bun install turbo --dev
```

#### nub

```
nub add turbo --save-dev
```

#### aube

```
aube add turbo --save-dev
```

When you invoke the global binary from this repository, it uses the installed local version instead.
