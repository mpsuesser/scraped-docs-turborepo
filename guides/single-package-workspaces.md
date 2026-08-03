---
url: https://turborepo.dev/docs/guides/single-package-workspaces
title: "Single-package workspaces"
description: "Use Turborepo's caching and task parallelization in a standalone application without a monorepo."
access_date: 2026-08-03T18:24:07.617Z
current_date: 2026-08-03T18:24:07.617Z
---

Learn how to use Turborepo in a single-package workspace.

While Turborepo is highly effective in [multi-package workspaces](https://vercel.com/docs/vercel-platform/glossary#multi-package-workspace) (commonly referred to as monorepos), it can also be used to make [single-package workspaces](https://vercel.com/docs/vercel-platform/glossary#single-package-workspace) faster.

Turborepo's most important features work in single-package workspaces including local and [Remote Caching](../core-concepts/remote-caching.md) and task parallelization. Features that don't work are ones that don't make sense in the context of a single package, like package tasks (`app#build`).

## Installation

Install `turbo` into your application:

#### pnpm

```
pnpm add turbo --save-dev
```

#### yarn

```
yarn add turbo --dev
```

#### npm

```
npm install turbo --save-dev
```

#### bun

```
bun install turbo --dev
```

### Running a package.json script using global turbo (optional)

For even faster developer workflows, you can [install `turbo` globally](../getting-started/installation.md#global-installation), too, and run commands directly from the command line.

Once installed, you can run `turbo build` and Turborepo will run your `build` script from `package.json`. Running `turbo build` again will hit the cache.

At this point, `turbo` isn't providing much value since you're likely to only be rebuilding your application when code changes and, when your code changes, `turbo` will miss the cache. In two short steps, you can get more out of `turbo`.

## Running many scripts with one command

In many repositories, there are setup tasks or pre-build steps to run. These tasks are often run one at a time - but you can easily turn them into one script with `turbo`.

For example, let's say you have a project where we always have to set up a development environment whenever you start working. You need to:

1. Start a Docker container for a database.
2. Push a database schema to the database.
3. Seed the database with data.
4. Start the development server.

You can schedule these tasks into one command using Turborepo. First, create scripts in your `package.json`:

```
{
  "name": "@acme/my-app",
  "version": "0.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "check-types": "tsc --noEmit",
    "db:up": "docker-compose up -d",
    "db:push": "your-orm-tool schema-push",
    "db:seed": "node ./db-seed.js"
  }
}
```

Then, create tasks in `turbo.json` to run these scripts in order:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "dev": {
      "dependsOn": ["db:seed"],
      "cache": false,
      "persistent": true
    },
    "db:seed": {
      "dependsOn": ["db:push"],
      "cache": false
    },
    "db:push": {
      "dependsOn": ["db:up"],
      "cache": false
    },
    "db:up": {
      "cache": false
    }
  }
}
```

The `dependsOn` arrays in the tasks above create a sequential order for the tasks. When you run `turbo dev`, the scripts for `db:up`, then `db:push`, then `db:seed` will be run first.

## Parallelizing tasks

Using `turbo` to parallelize tasks results in speeding up tasks by running all at once, when they can be. For instance, you can run your ESLint, TypeScript, and Prettier checks at the same time. Given scripts like:

```
{
  "scripts": {
    "lint": "eslint .",
    "format": "prettier .",
    "check-types": "tsc --noEmit"
  }
}
```

You can create a configuration like this one:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "lint": {},
    "format": {},
    "check-types": {}
  }
}
```

Then, to run all tasks at the same time:

```
turbo check-types lint format
```

## Optimizing task using inputs

Because Turborepo will treat a single-package workspace as one package, it can help to optimize inputs to tasks to make sure unrelated changes don't create cache misses.

For instance, a script for checking types using `tsc --noEmit` can be configured with inputs that only include TypeScript files:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "check-types": {
      "inputs": ["**/*.{ts,tsx}"]
    }
  }
}
```
