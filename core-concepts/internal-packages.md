---
url: https://turborepo.dev/docs/core-concepts/internal-packages
title: "Internal Packages"
description: "Understand how internal packages work and their compilation strategies for sharing code across your monorepo."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Learn how to build Internal Packages in your monorepo.

Internal Packages are libraries whose source code is inside your Workspace. You can quickly make Internal Packages to share code within your monorepo and choose to [publish them to the npm registry](../guides/publishing-libraries.md) if you need to later.

Internal Packages are used in your repository by installing them in `package.json` similar to an external package coming from the npm registry. However, instead of marking a version to install, you can reference the package using your package manager's workspace installation syntax:

#### pnpm

```
{
  "dependencies": {
    "@repo/ui": "workspace:*"
  }
}
```

#### yarn

```
{
  "dependencies": {
    "@repo/ui": "*"
  }
}
```

#### npm

```
{
  "dependencies": {
    "@repo/ui": "*"
  }
}
```

#### bun

```
{
  "dependencies": {
    "@repo/ui": "workspace:*"
  }
}
```

In the [Creating an Internal Package guide](../crafting-your-repository/creating-an-internal-package.md), you can build an Internal Package from the beginning using [the Compiled Package strategy](#compiled-packages). On this page, we'll describe other strategies for creating Internal Packages and their tradeoffs, including [publishing the package to the npm registry](#publishable-packages) to create an External Package.

You can then import the package into your code like you're used to doing with an external package:

```
import { Button } from "@repo/ui";

export default function Page() {
  return <Button>Submit</Button>;
}
```

## Compilation Strategies

Depending on what you need from your library, you can choose one of three compilation strategies:

- [**Just-in-Time Packages**](#just-in-time-packages): Create minimal configuration for your package by allowing application bundlers to compile the package as it uses it.
- [**Compiled Packages**](#compiled-packages): With a moderate amount of configuration, compile your package using a build tool like `tsc` or a bundler.
- [**Publishable Packages**](#publishable-packages): Compile and prepare a package to publish to the npm registry. This approach requires the most configuration.

### Just-in-Time Packages

A Just-in-Time package is compiled by the application that uses it. This means you can use your TypeScript (or uncompiled JavaScript) files directly, requiring much less configuration than the other strategies on this page.

This strategy is most useful when:

- Your applications are built using a modern bundler like Turbopack, webpack, or Vite.
- You want to avoid configuration and setup steps.
- You're satisfied with your applications' build times, even when you can't hit cache for the package.

A `package.json` for a Just-in-Time package may look like this one:

```
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button.tsx",
    "./card": "./src/card.tsx"
  },
  "scripts": {
    "lint": "eslint . --max-warnings 0",
    "check-types": "tsc --noEmit"
  }
}
```

There are a few important things to notice in this `package.json`:

- **Directly exporting TypeScript**: The `exports` field marks the entrypoints for the package and, in this case, you're **referencing TypeScript files directly**. This is possible because the bundler for the application will compile the code as it uses it in its build process.
- **No `build` script**: Because this package is exporting TypeScript, it doesn't need a build step for transpiling the package. This means you don't have to configure a build tool in this package to make it work in your Workspace.

#### Limitations and tradeoffs

- **Only applicable when consumers do transpiling**: This strategy can only be used when the package is going to be used in tooling that uses a bundler or natively understands TypeScript. The consumer's bundler is responsible for transpiling the TypeScript packages to JavaScript. If your builds or other usages of the package are not able to consume TypeScript, you will need to move to the [Compiled Packages](#compiled-packages) strategy.
- **No TypeScript `paths`**: A library that is being transpiled by its consumer cannot use the `compilerOptions.paths` configuration because TypeScript assumes that source code is being transpiled in the package where it is written. If you're using TypeScript 5.4 or later, we recommend [using Node.js subpath imports](https://devblogs.microsoft.com/typescript/announcing-typescript-5-4/#auto-import-support-for-subpath-imports). To learn how, visit [our TypeScript page](../guides/tools/typescript.md#use-nodejs-subpath-imports-instead-of-typescript-compiler-paths).
- **Turborepo cannot cache a build for a Just-in-Time Package**: Because the package doesn't have its own `build` step, it can't be cached by Turborepo. This tradeoff may make sense for you if you want to keep configuration to a minimum and are okay with the build times for your applications.
- **Errors in internal dependencies will be reported**: When directly exporting TypeScript, type-checking in a dependent package will fail if code in an internal dependency has TypeScript errors. You may find this confusing or problematic in some situations.

### Compiled Packages

A Compiled Package is a package that handles its own compilation using a build tool, like [`tsc` (the TypeScript compiler)](https://www.typescriptlang.org/docs/handbook/compiler-options.html#handbook-content).

```
{
  "name": "@repo/ui",
  "exports": {
    "./button": {
      "types": "./src/button.tsx",
      "default": "./dist/button.js"
    },
    "./card": {
      "types": "./src/card.tsx",
      "default": "./dist/card.js"
    }
  },
  "scripts": {
    "build": "tsc"
  }
}
```

Compiling your library produces compiled JavaScript outputs into a directory (`dist`, `build`, etc.) that you will use for the entrypoints for your package. The build outputs will be cached by Turborepo once they're added to the [`outputs` key of the task](../reference/configuration.md#outputs), allowing you to have faster build times.

#### Limitations and tradeoffs

- **Using the TypeScript compiler**: The majority of Compiled Packages should use `tsc`. Since the package is highly likely to be consumed by an application that is using a bundler, the application's bundler will prepare the library package for distribution in the application's final bundles, handling polyfilling, downleveling, and other concerns. A bundler should only be used if you have a specific use case that requires it, like bundling static assets into your package's outputs.
- **More configuration**: Compiled Packages require deeper knowledge and configuration to create build outputs. There are [many configurations for the TypeScript compiler](https://www.typescriptlang.org/docs/handbook/compiler-options.html#compiler-options) that can be difficult to manage and understand, and further configuration to optimize for bundlers, like [the `sideEffects` key in `package.json`](https://webpack.js.org/guides/tree-shaking/#mark-the-file-as-side-effect-free). You can find some of our recommendations in [our dedicated TypeScript guide](../guides/tools/typescript.md).

### Publishable packages

Publishing a package to the npm registry comes with the most strict requirements of the packaging strategies on this page. Because you don't know anything about how your package will be used by consumers who download the package from the registry, you may find it difficult due to the numerous configurations required for a robust package.

Additionally, the process of publishing a package to the npm registry requires specialized knowledge and tooling. We recommend [`changesets`](https://github.com/changesets/changesets) for managing versioning, changelogs, and the publishing process.

For a detailed guide, visit [our Publishing packages guide](../guides/publishing-libraries.md).
