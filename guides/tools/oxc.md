---
url: https://turborepo.dev/docs/guides/tools/oxc
title: "Oxc (oxlint and oxfmt)"
description: "Configure oxlint and oxfmt as root tasks for fast linting and formatting in your monorepo."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
---

Learn how to use oxlint and oxfmt in your Turborepo projects.

[Oxc](https://oxc.rs/) is a collection of high-performance JavaScript and TypeScript tools written in Rust, including [oxlint](https://oxc.rs/docs/guide/usage/linter) (a fast linter) and [oxfmt](https://oxc.rs/docs/guide/usage/formatter) (a fast formatter).

## Using Oxc tools with Turborepo

Similar to [Biome](biome.md), oxlint and oxfmt are **extraordinarily fast** tools. For this reason, we recommend using [Root Tasks](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks) rather than creating separate scripts in each of your packages.

Caching behavior

## Setting up oxlint

### Install oxlint

First, install oxlint in your repository:

#### pnpm

```
pnpm add --save-dev -w oxlint
```

#### yarn

```
yarn add --dev oxlint
```

#### npm

```
npm install --save-dev oxlint
```

#### bun

```
bun add --dev oxlint
```

### Create scripts

Add scripts to the root `package.json` of your repository:

```
{
  "scripts": {
    "lint": "oxlint .",
    "lint:fix": "oxlint --fix ."
  }
}
```

### Create root tasks

Register the scripts to Turborepo as [Root Tasks](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks):

```
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

Type-aware linting

## Setting up oxfmt

oxfmt is a fast code formatter for JavaScript and TypeScript, designed to be a drop-in replacement for Prettier.

oxfmt is experimental

### Install oxfmt

Install oxfmt as a dev dependency:

#### pnpm

```
pnpm add --save-dev -w oxfmt
```

#### yarn

```
yarn add --dev oxfmt
```

#### npm

```
npm install --save-dev oxfmt
```

#### bun

```
bun add --dev oxfmt
```

### Create scripts

Add formatting scripts to the root `package.json`:

```
{
  "scripts": {
    "format": "oxfmt --check",
    "format:fix": "oxfmt ."
  }
}
```

### Create root tasks

Register the scripts to Turborepo:

```
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

```
{
  "scripts": {
    "lint": "oxlint .",
    "lint:fix": "oxlint --fix .",
    "format": "oxfmt --check",
    "format:fix": "oxfmt ."
  }
}
```

```
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
