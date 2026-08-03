---
url: https://turborepo.dev/docs/getting-started/add-to-existing-repository
title: "Add to an existing repository"
description: "Incrementally adopt Turborepo in an existing single-package or multi-package repository."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

Turborepo can be incrementally adopted in **any repository, single or multi-package**, to speed up the developer and CI workflows of the repository.

After installing `turbo` and configuring your tasks in `turbo.json`, you'll notice how [caching](../crafting-your-repository/caching.md) helps you run tasks much faster.

## Preparing a single-package workspace

A [single-package workspace](https://vercel.com/docs/vercel-platform/glossary#single-package-workspace) is, for example, what you get after running `npx create-next-app` or `npm create vite`. You don't need to do any extra work for Turborepo to handle your repo so you can jump to the first step below.

To learn more about Turborepo in single-package workspaces, visit [the dedicated guide](../guides/single-package-workspaces.md).

## Preparing a multi-package workspace (monorepo)

`turbo` is built on top of Workspaces, a feature of the major package managers in the JavaScript ecosystem. This makes it easy to adopt in your existing codebase.

Note that you don't have to start running *all* your tasks for *all* your packages using `turbo` right away. You can start with a single task in just a few packages and incrementally add more tasks and packages as you get more familiar with Turborepo.

## Adding Turborepo to your repository

### Install turbo

We recommend you install `turbo` both globally and into your repository's root for the best developer experience.

#### pnpm

Ensure you have created a `pnpm-workspace.yaml` file before you begin the installation. Failure to have this file will result in an error that says: ` --workspace-root may only be used inside a workspace`.

```
# Global install
pnpm add turbo --global
# Install in repository
pnpm add turbo --save-dev --workspace-root
```

#### yarn

```
# Global install
yarn global add turbo
# Install in repository
yarn add turbo --dev
```

#### npm

```
# Global install
npm install turbo --global
# Install in repository
npm install turbo --save-dev
```

#### bun

```
# Global install
bun install turbo --global
# Install in repository
bun install turbo --dev
```

To learn more about why we recommend both installations, visit the [Installation page](installation.md).

### Add a turbo.json file

In the root of your repository, create a `turbo.json` file.

We'll be using `build` and `check-types` tasks in this guide but you can replace these with other tasks that interest you, like `lint` or `test`.

#### Next.js

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"]
    },
    "check-types": {
      "dependsOn": ["^check-types"]
    },
    "dev": {
      "persistent": true,
      "cache": false
    }
  }
}
```

For more information on configuring your `turbo.json`, see the [Configuration Options](../reference/configuration.md) documentation.

In your Next.js application, make sure you have a `check-types` script for `turbo` to run.

```
{
  "scripts": {
+   "check-types": "tsc --noEmit"
  }
}
```

#### Vite

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    },
    "check-types": {
      "dependsOn": ["^check-types"]
    },
    "dev": {
      "persistent": true,
      "cache": false
    }
  }
}
```

Some Vite starters ship with a `package.json` that looks like this:

```
{
  "scripts": {
    "build": "tsc && vite build"
  }
}
```

We recommend splitting these into a `check-types` and `build` script so that `turbo` can run them in parallel.

```
{
  "scripts": {
    "build": "vite build",
    "check-types": "tsc --noEmit"
  }
}
```

In a multi-package workspace, you may also want to add a `check-types` script to one or more of your library packages to see how multiple scripts across different packages run with one `turbo` command.

### Edit.gitignore

Add `.turbo` to your `.gitignore` file. The `turbo` CLI uses these folders for persisting logs, outputs, and other functionality.

```
+ .turbo
```

### Add devEngines.packageManager to root package.json

Turborepo optimizes your repository using information from your package manager. To declare which package manager you're using, add `devEngines.packageManager` to your root `package.json` if you don't have a package manager declaration already. The legacy top-level [`packageManager`](https://nodejs.org/api/packages.html#packagemanager) field is also supported.

#### pnpm

```
{
+  "devEngines": {
+    "packageManager": {
+      "name": "pnpm",
+      "version": "10.0.0"
+    }
+  }
}
```

#### yarn

```
{
+  "devEngines": {
+    "packageManager": {
+      "name": "yarn",
+      "version": "1.22.19"
+    }
+  }
}
```

#### npm

```
{
+  "devEngines": {
+    "packageManager": {
+      "name": "npm",
+      "version": "8.5.0"
+    }
+  }
}
```

#### bun

```
{
+  "devEngines": {
+    "packageManager": {
+      "name": "bun",
+      "version": "1.2.0"
+    }
+  }
}
```

### Set up package manager workspaces

For [multi-package workspaces](https://vercel.com/docs/glossary#multi-package-workspace), you'll need to configure your package manager to recognize your workspace structure.

The `workspaces` field tells your package manager which directories contain your packages. Common patterns include `apps/*` for applications and `packages/*` for shared libraries.

#### pnpm

```
packages:
  - "apps/*"
  - "packages/*"
```

[→ pnpm workspace documentation](https://pnpm.io/pnpm-workspace_yaml)

#### yarn

```
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

[→ yarn workspace documentation](https://yarnpkg.com/features/workspaces#how-are-workspaces-declared)

#### npm

```
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

[→ npm workspace documentation](https://docs.npmjs.com/cli/v7/using-npm/workspaces#defining-workspaces)

#### bun

```
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

[→ bun workspace documentation](https://bun.sh/docs/install/workspaces)

For more details on how to structure your repository, see [Structuring a Repository](../crafting-your-repository/structuring-a-repository.md#declaring-directories-for-packages).

### Run tasks with turbo

You can now run the tasks you added to `turbo.json` earlier using Turborepo. Using the example tasks from above:

```
turbo build check-types
```

This runs the `build` and `check-types` tasks at the same time. The dependency graph of your [Workspace](../crafting-your-repository/structuring-a-repository.md#anatomy-of-a-workspace) will be used to run tasks in the right order.

**Without making any changes to the code, try running `build` and `check-types` again:**

```
turbo check-types build
```

You should see terminal output like this:

```
Tasks:    2 successful, 2 total
Cached:    2 cached, 2 total
Time:    185ms >>> FULL TURBO
```

Congratulations! **You just built and type checked your code in milliseconds**.

To learn more about how `turbo` makes this possible, check out [the caching documentation](../crafting-your-repository/caching.md).

### Begin developing by running dev with turbo

In a multi-package workspace, you can run `turbo dev` to start the development tasks for all your packages at once.

```
turbo dev
```

You can also [use a filter](../crafting-your-repository/running-tasks.md#using-filters) to focus on a specific package and its dependencies.

## Next steps

You're now up and running with Turborepo! To learn about more ways you can improve your workflow and get the most out of `turbo`, we recommend checking out the following pages:

- [Enabling Remote Caching for development machines](../crafting-your-repository/constructing-ci.md#enabling-remote-caching)
- [Enabling Remote Caching in CI](../crafting-your-repository/constructing-ci.md)
- [Handling environment variables](../crafting-your-repository/using-environment-variables.md)
- [Filtering tasks](../reference/run.md#--filter-string)
