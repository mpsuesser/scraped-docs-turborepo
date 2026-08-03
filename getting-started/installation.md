---
url: https://turborepo.dev/docs/getting-started/installation
title: "Installation"
description: "Install Turborepo globally and in your repository using your preferred package manager."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
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

The starter repository will have:

- Two deployable applications
- Three shared libraries for use in the rest of the monorepo

For more details on the starter, [visit the README for the basic starter on GitHub](https://github.com/vercel/turborepo/tree/main/examples/basic). You can also [use an example](examples.md) that more closely fits your tooling interests.

## Installing turbo

`turbo` can be installed both globally **and** in your repository. We highly recommend installing both ways so you can take advantage of fast, convenient workflows *and* a stable version of `turbo` for all developers working in your repository.

### Global installation

A global install of `turbo` brings flexibility and speed to your local workflows.

#### pnpm

```
pnpm add turbo --global
```

#### yarn

```
yarn global add turbo
```

#### npm

```
npm install turbo --global
```

#### bun

```
bun install turbo --global
```

Once installed globally, you can run your scripts through `turbo` from your terminal, quickly running one-off commands to use within your repository. For example:

- `turbo build`: Run `build` scripts following your repository's dependency graph
- `turbo build --filter=docs --dry`: Quickly print an outline of the `build` task for your `docs` package (without running it)
- `turbo generate`: Run [Generators](../guides/generating-code.md) to add new code to your repository
- `cd apps/docs && turbo build`: Run the `build` script in the `docs` package and its dependencies. For more, visit the [Automatic Package Scoping section](../crafting-your-repository/running-tasks.md#automatic-package-scoping).

Avoid multiple global installations

#### Using global turbo in CI

You can also take advantage of global `turbo` when creating your CI pipelines. Visit the [Constructing CI](../crafting-your-repository/constructing-ci.md#global-turbo-in-ci) guide for more information.

### Repository installation

When collaborating with other developers in a repository, it's a good idea to pin versions of dependencies. You can do this with `turbo` by adding it as a `devDependency` in the root of your repository:

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

You can continue to use your global installation of `turbo` to run commands. Global `turbo` will defer to the local version of your repository if it exists.

This lets you to get the best of both installations: easily run commands in your terminal while maintaining a pinned version for consistent usage for all developers in the repository.
