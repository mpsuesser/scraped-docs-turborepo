---
url: https://turborepo.dev/docs/crafting-your-repository/creating-an-internal-package
title: "Creating an Internal Package"
description: "Build a new internal package from scratch to share code across your monorepo."
access_date: 2026-08-04T15:46:32.475Z
current_date: 2026-08-04T15:46:32.475Z
---

Learn how to create an Internal Package for your monorepo.

[Internal Packages](../core-concepts/internal-packages.md) are the building blocks of your workspace, giving you a powerful way to share code and functionality across your repo. Turborepo automatically understands the relationships between Internal Packages using the dependencies in `package.json`, creating a [Package Graph](../core-concepts/package-and-task-graph.md#package-graph) under the hood to optimize your repository's workflows.

![Visual representation of a Package Graph in a Turborepo.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fimmutable%2Fmedia%2Fpackage-graph.29crs36iw_l25.png&w=3840&q=75)

Let's create your first Internal Package to share math utilities in your repo using the guidance in the [Anatomy of a package](structuring-a-repository.md#anatomy-of-a-package) section and the [Compiled Packages](../core-concepts/internal-packages.md#compiled-packages) pattern. In the steps below, we assume you've [created a new repository using `create-turbo`](../getting-started/installation.md) or are using a similarly structured repository.

### Create an empty directory

You'll need a directory to put the package in. Let's create one at `./packages/math`.

package.json

turbo.json

### Add a package.json

Next, create the `package.json` for the package. By adding this file, you'll fulfill [the two requirements for an Internal Package](structuring-a-repository.md#specifying-packages-in-a-monorepo), making it discoverable to Turborepo and the rest of your Workspace.

#### pnpm

```
{
  "name": "@repo/math",
  "type": "module",
  "scripts": {
    "dev": "tsc --watch",
    "build": "tsc"
  },
  "exports": {
    "./add": {
      "types": "./src/add.ts",
      "default": "./dist/add.js"
    },
    "./subtract": {
      "types": "./src/subtract.ts",
      "default": "./dist/subtract.js"
    }
  },
  "devDependencies": {
    "@repo/typescript-config": "workspace:*",
    "typescript": "latest"
  }
}
```

#### yarn

```
{
  "name": "@repo/math",
  "type": "module",
  "scripts": {
    "dev": "tsc --watch",
    "build": "tsc"
  },
  "exports": {
    "./add": {
      "types": "./src/add.ts",
      "default": "./dist/add.js"
    },
    "./subtract": {
      "types": "./src/subtract.ts",
      "default": "./dist/subtract.js"
    }
  },
  "devDependencies": {
    "@repo/typescript-config": "workspace:*",
    "typescript": "latest"
  }
}
```

#### npm

```
{
  "name": "@repo/math",
  "type": "module",
  "scripts": {
    "dev": "tsc --watch",
    "build": "tsc"
  },
  "exports": {
    "./add": {
      "types": "./src/add.ts",
      "default": "./dist/add.js"
    },
    "./subtract": {
      "types": "./src/subtract.ts",
      "default": "./dist/subtract.js"
    }
  },
  "devDependencies": {
    "@repo/typescript-config": "*",
    "typescript": "latest"
  }
}
```

#### bun

```
{
  "name": "@repo/math",
  "type": "module",
  "scripts": {
    "dev": "tsc --watch",
    "build": "tsc"
  },
  "exports": {
    "./add": {
      "types": "./src/add.ts",
      "default": "./dist/add.js"
    },
    "./subtract": {
      "types": "./src/subtract.ts",
      "default": "./dist/subtract.js"
    }
  },
  "devDependencies": {
    "@repo/typescript-config": "workspace:*",
    "typescript": "latest"
  }
}
```

Let's break down this `package.json` piece-by-piece:

- **`name`**: This is the most critical field for package discoverability. The value `@repo/math` becomes the exact identifier used in import statements throughout your workspace. If you change this name, you must update all import statements accordingly.
- **`scripts`**: The `dev` and `build` script compile the package using [the TypeScript compiler](https://www.typescriptlang.org/docs/handbook/compiler-options.html). The `dev` script will watch for changes to source code and automatically recompile the package.
- **`devDependencies`**: `typescript` and `@repo/typescript-config` are `devDependencies` so you can use those packages in the `@repo/math` package. In a real-world package, you will likely have more `devDependencies` and `dependencies` - but we can keep it simple for now.
- **`exports`**: Defines multiple entrypoints for the package so it can be used in other packages (`import { add } from '@repo/math/add'`).

Notably, this `package.json` declares an Internal Package, `@repo/typescript-config`, as a dependency. Turborepo will recognize `@repo/math` as a dependent of `@repo/typescript-config` for ordering your tasks.

### Add a tsconfig.json

Specify the TypeScript configuration for this package by adding a `tsconfig.json` file to **the root of the package**. TypeScript has [an `extends` key](https://www.typescriptlang.org/tsconfig#extends), allowing you to use a base configuration throughout your repository and overwrite with different options as needed.

```
{
  "extends": "@repo/typescript-config/base.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

You've done four important things here:

- The `@repo/typescript-config/base.json` configuration that lives in `./packages/typescript-config` has all the configuration you need so you extend from it.
- [The `outDir` key](https://www.typescriptlang.org/tsconfig/#outDir) in `compilerOptions` tells TypeScript where to put the compiled output. It matches the directory specified in your `exports` in `package.json`.
- [The `rootDir` key in `compilerOptions`](https://www.typescriptlang.org/tsconfig/#rootDir) ensures that the output in `outDir` uses the same structure as the `src` directory.
- The [`include`](https://www.typescriptlang.org/tsconfig/#include) and [`exclude`](https://www.typescriptlang.org/tsconfig/#exclude) keys are not inherited from the base configuration, [according to the TypeScript specification](https://www.typescriptlang.org/tsconfig#include), so you've included them here.

### Add a src directory with source code

You can now write some code for your package. Create two files inside a `src` directory:

#### add.ts

```
export const add = (a: number, b: number) => a + b;
```

#### subtract.ts

```
export const subtract = (a: number, b: number) => a - b;
```

These files map to the outputs that will be created by `tsc` when you run `turbo build` in a moment.

### Add the package to an application

You're ready to use your new package in an application. Let's add it to the `web` application.

#### pnpm

```
"dependencies": {
+   "@repo/math": "workspace:*",
    "next": "latest",
    "react": "latest",
    "react-dom": "latest"
  },
```

#### yarn

```
"dependencies": {
+   "@repo/math": "*",
    "next": "latest",
    "react": "latest",
    "react-dom": "latest"
  },
```

#### npm

```
"dependencies": {
+   "@repo/math": "*",
    "next": "latest",
    "react": "latest",
    "react-dom": "latest"
  },
```

#### bun

```
"dependencies": {
+   "@repo/math": "workspace:*",
    "next": "latest",
    "react": "latest",
    "react-dom": "latest"
  },
```

`@repo/math` is now available in the `web` application, you can use it in your code:

```
import { add } from "@repo/math/add";

function Page() {
  return <div>{add(1, 2)}</div>;
}

export default Page;
```

### Edit turbo.json

Add the artifacts for the new `@repo/math` library to the `outputs` for the `build` task in `turbo.json`. This ensures that its build outputs will be cached by Turborepo, so they can be restored instantly when you start running builds.

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**", "dist/**"]
    }
  }
}
```

### Run turbo build

If you've [installed `turbo` globally](../getting-started/installation.md#global-installation), run `turbo build` in your terminal at the root of your Workspace. You can also run the `build` script from `package.json` with your package manager, which will use `turbo run build`.

The `@repo/math` package built before the `web` application built so that the runtime code in `./packages/math/dist` is available to the `web` application when it bundles.

## Best practices for Internal Packages

### One "purpose" per package

When you're creating Internal Packages, it's recommended to create packages that have a single "purpose". This isn't a strict science or rule, but a best practice depending on your repository, your scale, your organization, what your teams need, and more. This strategy has several advantages:

- **Easier to understand**: As a repository scales, developers working in the repository will more easily be able to find the code they need.
- **Reducing dependencies per package**: Using fewer dependencies per package makes it so Turborepo can more effectively [prune the dependencies of your package graph](../reference/prune.md).

Some examples include:

- **`@repo/ui`**: A package containing all of your shared UI components
- **`@repo/tool-specific-config`**: A package for managing configuration of a specific tool
- **`@repo/graphs`**: A domain-specific library for creating and manipulating graphical data

### Application Packages do not contain shared code

When you're creating [Application Packages](../core-concepts/package-types.md#application-packages), it's best to avoid putting shared code in those packages. Instead, you should create a separate package for the shared code and have the application packages depend on that package.

Additionally, Application Packages are not meant to be installed into other packages. Instead, they should be thought of as an entrypoint to your [Package Graph](../core-concepts/package-and-task-graph.md#package-graph).

## Next steps

With a new Internal Package in place, you can start [configuring tasks](configuring-tasks.md).
