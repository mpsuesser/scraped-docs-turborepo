---
url: https://turborepo.dev/docs/getting-started/add-to-existing-repository
title: "Add to an existing repository"
description: "Using Turborepo with your existing repository"
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Turborepo can be incrementally adopted in **any repository, single or multi-package**, to speed up the developer and CI workflows of the repository.

After installing `turbo` and configuring your tasks in `turbo.json`, you'll notice how [caching](../crafting-your-repository/caching.md) helps you run tasks much faster.

## Preparing a single-package workspace

A [single-package workspace](https://vercel.com/docs/glossary#single-package-workspace) is, for example, what you get after running `npx create-next-app` or `npm create vite`. You don't need to do any extra work for Turborepo to handle your repo so you can jump to the first step below.

To learn more about Turborepo in single-package workspaces, visit [the dedicated guide](../guides/single-package-workspaces.md).

## Preparing a multi-package workspace (monorepo)

`turbo` is built on top of Workspaces, a feature of the major package managers in the JavaScript ecosystem. This makes it easy to adopt in your existing codebase.

> If you're finding that `turbo` is having issues like not being able to
> discover packages in your workspace or not following your dependency graph,
> visit our [Structuring a
> repository](../crafting-your-repository/structuring-a-repository.md) page for
> tips.

Note that you don't have to start running *all* your tasks for *all* your
packages using `turbo` right away. You can start with a single task in just a
few packages and incrementally add more tasks and packages as you get more
familiar with Turborepo.

## Adding Turborepo to your repository

### 1. Install `turbo`

Install `turbo` globally.

#### macOS and Linux

```sh title="Terminal"
curl -fsSL https://turborepo.dev/install | sh
```

#### Windows x64

```powershell title="PowerShell"
irm https://turborepo.dev/install.ps1 | iex
```

> The binaries are also available in the [GitHub release assets](https://github.com/vercel/turborepo/releases).

Then pin `turbo` in your repository's root:

#### pnpm

Ensure you have created a `pnpm-workspace.yaml` file before you begin the installation. Failure to have this file will result in an error that says: ` --workspace-root may only be used inside a workspace`.

```bash title="Terminal"
pnpm add turbo --save-dev --workspace-root
```

#### yarn

```bash title="Terminal"
yarn add turbo --dev
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

The installed local version takes precedence when you run the global binary in your repository. See [Installation](installation.md#repository-installation) for details.

### 2. Add a `turbo.json` file

In the root of your repository, create a `turbo.json` file.

We'll be using `build` and `check-types` tasks in this guide but you can replace these with other tasks that interest you, like `lint` or `test`.

#### Next.js

```json title="./turbo.json"
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

```diff title="apps/web/package.json"
{
  "scripts": {
+   "check-types": "tsc --noEmit"
  }
}
```

#### Vite

```json title="./turbo.json"
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

```json title="package.json"
{
  "scripts": {
    "build": "tsc && vite build"
  }
}
```

We recommend splitting these into a `check-types` and `build` script so that `turbo` can run them in parallel.

```json title="apps/web/package.json"
{
  "scripts": {
    "build": "vite build",
    "check-types": "tsc --noEmit"
  }
}
```

In a multi-package workspace, you may also want to add a `check-types` script
to one or more of your library packages to see how multiple scripts across
different packages run with one `turbo` command.

### 3. Edit `.gitignore`

Add `.turbo` to your `.gitignore` file. The `turbo` CLI uses these folders for persisting logs, outputs, and other functionality.

```diff title=".gitignore"
+ .turbo
```

### 4. Add `devEngines.packageManager` to root `package.json`

Turborepo optimizes your repository using information from your package manager. To declare which package manager you're using, add `devEngines.packageManager` to your root `package.json` if you don't have a package manager declaration already. The legacy top-level [`packageManager`](https://nodejs.org/api/packages.html#packagemanager) field is also supported.

#### pnpm

```diff title="package.json"
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

```diff title="package.json"
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

```diff title="package.json"
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

```diff title="package.json"
{
+  "devEngines": {
+    "packageManager": {
+      "name": "bun",
+      "version": "1.2.0"
+    }
+  }
}
```

#### nub

```diff title="package.json"
{
+  "devEngines": {
+    "packageManager": {
+      "name": "nub",
+      "version": "0.8.3"
+    }
+  }
}
```

#### aube

```diff title="package.json"
{
+  "devEngines": {
+    "packageManager": {
+      "name": "aube",
+      "version": "2.2.9"
+    }
+  }
}
```

> Depending on your repository, you may need to use the
> [`dangerouslyDisablePackageManagerCheck`](../reference/configuration.md#dangerouslydisablepackagemanagercheck)
> while migrating or in situations where you can't use a package manager
> declaration yet.

### 5. Set up package manager workspaces

For [multi-package workspaces](https://vercel.com/docs/glossary#multi-package-workspace), you'll need to configure your package manager to recognize your workspace structure.

The `workspaces` field tells your package manager which directories contain your packages. Common patterns include `apps/*` for applications and `packages/*` for shared libraries.

> If you're working with a single-package repository, you can skip this step as
> workspaces aren't needed.

#### pnpm

```json title="pnpm-workspace.yaml"
packages:
  - "apps/*"
  - "packages/*"
```

#### yarn

```json title="./package.json"
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

#### npm

```json title="./package.json"
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

#### bun

```json title="./package.json"
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

#### nub

```json title="./package.json"
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

#### aube

```yaml title="aube-workspace.yaml"
packages:
  - "apps/*"
  - "packages/*"
```

For more details on how to structure your repository, see [Structuring a Repository](../crafting-your-repository/structuring-a-repository.md#declaring-directories-for-packages).

### 6. Run tasks with `turbo`

You can now run the tasks you added to `turbo.json` earlier using Turborepo. Using the example tasks from above:

```bash title="Terminal"
turbo build check-types
```

This runs the `build` and `check-types` tasks at the same time. The dependency graph of your [Workspace](../crafting-your-repository/structuring-a-repository.md#anatomy-of-a-workspace) will be used to run tasks in the right order.

**Without making any changes to the code, try running `build` and `check-types` again:**

```bash title="Terminal"
turbo check-types build
```

You should see terminal output like this:

```bash title="Terminal"
Tasks:    2 successful, 2 total
Cached:    2 cached, 2 total
Time:    185ms >>> FULL TURBO
```

Congratulations! **You just built and type checked your code in milliseconds**.

To learn more about how `turbo` makes this possible, check out [the caching documentation](../crafting-your-repository/caching.md).

### 8. Begin developing by running `dev` with `turbo`

In a multi-package workspace, you can run `turbo dev` to start the development tasks for all your packages at once.

```bash title="Terminal"
turbo dev
```

You can also [use a filter](../crafting-your-repository/running-tasks.md#using-filters) to focus on a specific package and its dependencies.

> Note that this step doesn't provide much value in a single-package workspace since:
>
> - You don't cache the outputs for a development task.
> - There's only one development script so there's nothing to run in parallel.

## 9. Next steps

You're now up and running with Turborepo! To learn about more ways you can improve your workflow and get the most out of `turbo`, we recommend checking out the following pages:

- [Enabling Remote Caching for development machines](../crafting-your-repository/constructing-ci.md#enabling-remote-caching)
- [Enabling Remote Caching in CI](../crafting-your-repository/constructing-ci.md)
- [Handling environment variables](../crafting-your-repository/using-environment-variables.md)
- [Filtering tasks](../reference/run.md#--filter-string)

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
