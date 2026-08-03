---
url: https://turborepo.dev/docs/crafting-your-repository/structuring-a-repository
title: "Structuring a repository"
description: "Set up a multi-package workspace with the directory structure, package.json files, and lockfile that Turborepo expects."
access_date: 2026-08-03T19:10:40.966Z
current_date: 2026-08-03T19:10:40.966Z
---

Start by creating a repository using the conventions of the ecosystem.

`turbo` is built on top of [Workspaces](https://vercel.com/docs/vercel-platform/glossary#workspace), a feature of package managers in the JavaScript ecosystem that allows you to group multiple packages in one repository.

Following these conventions is important because it allows you to:

- Lean on those conventions for all your repo's tooling
- Quickly, incrementally adopt Turborepo into an existing repository

In this guide, we'll walk through setting up a multi-package workspace (monorepo) so we can set the groundwork for `turbo`.

## Getting started

Setting up a workspace's structure can be tedious to do by hand. If you're new to monorepos, we recommend [using `create-turbo` to get started](../getting-started/installation.md) with a valid workspace structure right away.

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

You can then review the repository for the characteristics described in this guide.

## Anatomy of a workspace

In JavaScript, a workspace can either be [a single package](../guides/single-package-workspaces.md) or a collection of packages. In these guides, we'll be focusing on [a multi-package workspace](https://vercel.com/docs/vercel-platform/glossary#monorepo), often called a "monorepo".

Below, the structural elements of `create-turbo` that make it a valid workspace are highlighted.

#### pnpm

package.json

pnpm-lock.yaml

pnpm-workspace.yaml

turbo.json

package.json

#### yarn

package.json

yarn.lock

turbo.json

package.json

#### npm

package.json

package-lock.json

turbo.json

package.json

#### bun

package.json

bun.lock

turbo.json

package.json

### Minimum requirements

- [Packages as described by your package manager](#specifying-packages-in-a-monorepo)
- [A package manager lockfile](#package-manager-lockfile)
- [Root `package.json`](#root-packagejson)
- [Root `turbo.json`](#root-turbojson)
- [`package.json` in each package](#packagejson-in-each-package)

### Specifying packages in a monorepo

#### Declaring directories for packages

First, your package manager needs to describe the locations of your packages. We recommend starting with splitting your packages into `apps/` for applications and services and `packages/` for everything else, like libraries and tooling.

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

Using this configuration, every directory **with a `package.json`** in the `apps` or `packages` directories will be considered a package.

#### package.json in each package

In the directory of the package, there must be a `package.json` to make the package discoverable to your package manager and `turbo`. The [requirements for the `package.json` of a package](#anatomy-of-a-package) are below.

### Root package.json

The root `package.json` is the base for your workspace. Below is a common example of what you would find in a root `package.json`:

#### pnpm

```
{
  "private": true,
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "lint": "turbo run lint"
  },
  "devDependencies": {
    "turbo": "latest"
  },
  "devEngines": {
    "packageManager": {
      "name": "pnpm",
      "version": "9.0.0"
    }
  }
}
```

#### yarn

```
{
  "private": true,
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "lint": "turbo run lint"
  },
  "devDependencies": {
    "turbo": "latest"
  },
  "devEngines": {
    "packageManager": {
      "name": "yarn",
      "version": "1.22.19"
    }
  },
  "workspaces": ["apps/*", "packages/*"]
}
```

#### npm

```
{
  "private": true,
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "lint": "turbo run lint"
  },
  "devDependencies": {
    "turbo": "latest"
  },
  "devEngines": {
    "packageManager": {
      "name": "npm",
      "version": "10.0.0"
    }
  },
  "workspaces": ["apps/*", "packages/*"]
}
```

#### bun

```
{
  "private": true,
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "lint": "turbo run lint"
  },
  "devDependencies": {
    "turbo": "latest"
  },
  "devEngines": {
    "packageManager": {
      "name": "bun",
      "version": "1.2.0"
    }
  },
  "workspaces": ["apps/*", "packages/*"]
}
```

### Root turbo.json

`turbo.json` is used to configure the behavior of `turbo`. To learn more about how to configure your tasks, visit the [Configuring tasks](configuring-tasks.md) page.

### Package manager lockfile

A lockfile is key to reproducible behavior for both your package manager and `turbo`. Additionally, Turborepo uses the lockfile to understand the dependencies between your [Internal Packages](../core-concepts/internal-packages.md) within your Workspace.

## Anatomy of a package

It's often best to start thinking about designing a package as its own unit within the Workspace. At a high-level, each package is almost like its own small "project", with its own `package.json`, tooling configuration, and source code. There are limits to this idea—but its a good mental model to *start* from.

Additionally, a package has specific entrypoints that other packages in your Workspace can use to access the package, specified by [`exports`](#exports).

### package.json for a package

#### name

[The `name` field](https://nodejs.org/api/packages.html#name) is used to identify the package. It should be unique within your workspace.

#### scripts

The `scripts` field is used to define scripts that can be run in the package's context. Turborepo will use the name of these scripts to identify what scripts to run (if any) in a package. We talk more about these scripts on the [Running Tasks](running-tasks.md) page.

#### exports

[The `exports` field](https://nodejs.org/api/packages.html#exports) is used to specify the entrypoints for other packages that want to use the package. When you want to use code from one package in another package, you'll import from that entrypoint.

For example, if you had a `@repo/math` package, you might have the following `exports` field:

```
{
  "exports": {
    ".": "./src/constants.ts",
    "./add": "./src/add.ts",
    "./subtract": "./src/subtract.ts"
  }
}
```

Note that this example uses the [Just-in-Time Package](../core-concepts/internal-packages.md#just-in-time-packages) pattern for simplicity. It exports TypeScript directly, but you might choose to use the [Compiled Package](../core-concepts/internal-packages.md#compiled-packages) pattern instead.

This would allow you to import `add` and `subtract` functions from the `@repo/math` package like so:

```
import { GRAVITATIONAL_CONSTANT, SPEED_OF_LIGHT } from "@repo/math";
import { add } from "@repo/math/add";
import { subtract } from "@repo/math/subtract";
```

Using exports this way provides three major benefits:

- **Avoiding barrel files**: Barrel files are files that re-export other files in the same package, creating one entrypoint for the entire package. While they might appear convenient, they're [difficult for compilers and bundlers to handle](https://vercel.com/blog/how-we-optimized-package-imports-in-next-js#what's-the-problem-with-barrel-files) and can quickly lead to performance problems.
- **More powerful features**: `exports` also has other powerful features compared to [the `main` field](https://nodejs.org/api/packages.html#main) like [Conditional Exports](https://nodejs.org/api/packages.html#conditional-exports). In general, we recommend using `exports` over `main` whenever possible as it is the more modern option.
- **IDE autocompletion**: By specifying the entrypoints for your package using `exports`, you can ensure that your code editor can provide auto-completion for the package's exports.

#### imports (optional)

[The `imports` field](https://nodejs.org/api/packages.html#imports) gives you a way to create subpaths to other modules within your package. You can think of these like "shortcuts" to write simpler import paths that are more resilient to refactors that move files. To learn how, visit [the TypeScript page](../guides/tools/typescript.md#use-nodejs-subpath-imports-instead-of-typescript-compiler-paths).

### Source code

Of course, you'll want some source code in your package. Packages commonly use a `src` directory to store their source code and compile to a `dist` directory (that should also be located within the package), although this is not a requirement.

## Common pitfalls

- If you're using TypeScript, you likely don't need a `tsconfig.json` in the root of your workspace. Packages should independently specify their own configurations, usually building off of a shared `tsconfig.json` from a separate package in the workspace. For more information, visit [the TypeScript guide](../guides/tools/typescript.md#you-likely-dont-need-a-tsconfigjson-file-in-the-root-of-your-project).
- You want to avoid accessing files across package boundaries as much as possible. If you ever find yourself writing `../` to get from one package to another, you likely have an opportunity to re-think your approach by installing the package where it's needed and importing it into your code.

## Next steps

With your Workspace configured, you can now use your package manager to [install dependencies into your packages](managing-dependencies.md).
