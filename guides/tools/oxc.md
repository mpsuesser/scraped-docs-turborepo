---
url: https://turborepo.dev/docs/guides/tools/oxc
title: "Oxc (oxlint and oxfmt)"
description: "Learn how to use oxlint and oxfmt in your Turborepo projects."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[Oxc](https://oxc.rs/) is a collection of high-performance JavaScript and TypeScript tools written in Rust, including [oxlint](https://oxc.rs/docs/guide/usage/linter) (a fast linter) and [oxfmt](https://oxc.rs/docs/guide/usage/formatter) (a fast formatter).

## Using Oxc tools with Turborepo

Similar to [Biome](biome.md), oxlint and oxfmt are **extraordinarily fast** tools. For this reason, we recommend using [Root Tasks](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks) rather than creating separate scripts in each of your packages.

> **Caching behavior:**
>
> Using oxlint or oxfmt at the root of the project will result in cache misses
> for all tasks when you upgrade versions or change configuration. If you prefer
> the tradeoff of higher cache hit ratios in these situations over less
> configuration, you can still run these tools in separate scripts like the
> other recommendations in our guides.

## Setting up oxlint

### Install oxlint

First, install oxlint in your repository:

#### pnpm

```bash title="Terminal"
pnpm add --save-dev -w oxlint
```

#### yarn

```bash title="Terminal"
yarn add --dev oxlint
```

#### npm

```bash title="Terminal"
npm install --save-dev oxlint
```

#### bun

```bash title="Terminal"
bun add --dev oxlint
```

#### nub

```bash title="Terminal"
nub add --save-dev oxlint
```

#### aube

```bash title="Terminal"
aube add --save-dev oxlint
```

### Create scripts

Add scripts to the root `package.json` of your repository:

```json title="./package.json"
{
  "scripts": {
    "lint": "oxlint .",
    "lint:fix": "oxlint --fix ."
  }
}
```

### Create root tasks

Register the scripts to Turborepo as [Root Tasks](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks):

```json title="./turbo.json"
{
  "tasks": {
    "//#lint": {},
    "//#lint:fix": {
      "cache": false
    }
  }
}
```

You can now run `turbo run lint` to lint your entire repository.

> **Type-aware linting:**
>
> If you enable [type-aware linting rules](https://oxc.rs/docs/guide/usage/linter/config#type-aware-linting) in oxlint, the linter needs TypeScript type information to work correctly. This means any dependencies that must be built before type-checking (such as [Compiled Packages](../../core-concepts/internal-packages.md#compiled-packages) in your monorepo) also need to be built before linting.
>
> For example, to build Compiled Packages before running formatting and linting:
>
> ```bash title="Terminal"
> turbo run build --filter=./packages/* && turbo run lint
> ```
>
> Alternatively, you can switch to per-package lint tasks with `dependsOn: ["^build"]` to let Turborepo handle the ordering for you.

## Setting up oxfmt

oxfmt is a fast code formatter for JavaScript and TypeScript, designed to be a drop-in replacement for Prettier.

> **oxfmt is experimental:**
>
> oxfmt is currently in alpha and may not have full feature parity with
> Prettier. Check the [oxfmt
> documentation](https://oxc.rs/docs/guide/usage/formatter) for the latest
> status and supported options.

### Install oxfmt

Install oxfmt as a dev dependency:

#### pnpm

```bash title="Terminal"
pnpm add --save-dev -w oxfmt
```

#### yarn

```bash title="Terminal"
yarn add --dev oxfmt
```

#### npm

```bash title="Terminal"
npm install --save-dev oxfmt
```

#### bun

```bash title="Terminal"
bun add --dev oxfmt
```

#### nub

```bash title="Terminal"
nub add --save-dev oxfmt
```

#### aube

```bash title="Terminal"
aube add --save-dev oxfmt
```

### Create scripts

Add formatting scripts to the root `package.json`:

```json title="./package.json"
{
  "scripts": {
    "format": "oxfmt --check",
    "format:fix": "oxfmt ."
  }
}
```

### Create root tasks

Register the scripts to Turborepo:

```json title="./turbo.json"
{
  "tasks": {
    "//#format": {},
    "//#format:fix": {
      "cache": false
    }
  }
}
```

You can now run `turbo run format` to check formatting and `turbo run format:fix` to format your code.

## Using oxlint and oxfmt together

For repositories using both tools, you can orchestrate them with a unified quality task:

```json title="./package.json"
{
  "scripts": {
    "lint": "oxlint .",
    "lint:fix": "oxlint --fix .",
    "format": "oxfmt --check",
    "format:fix": "oxfmt ."
  }
}
```

```json title="./turbo.json"
{
  "tasks": {
    "//#quality": {
      "dependsOn": ["//#lint", "//#format"]
    },
    "//#quality:fix": {
      "dependsOn": ["//#lint:fix", "//#format:fix"]
    },
    "//#lint": {},
    "//#lint:fix": {
      "cache": false
    },
    "//#format": {},
    "//#format:fix": {
      "dependsOn": ["//#lint:fix"],
      "cache": false
    }
  }
}
```

With this configuration:

- Run `turbo run quality` to check both linting and formatting in parallel (safe because neither modifies files)
- Run `turbo run quality:fix` to fix both linting and formatting issues, with formatting running after lint fixes to avoid file write race conditions

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
