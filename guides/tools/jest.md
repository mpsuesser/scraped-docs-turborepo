---
url: https://turborepo.dev/docs/guides/tools/jest
title: "Jest"
description: "Set up Jest test suites across monorepo packages with Turborepo caching and parallelization."
access_date: 2026-08-03T18:24:07.617Z
current_date: 2026-08-03T18:24:07.617Z
---

Learn how to use Jest in a Turborepo.

[Jest](https://jestjs.io/) is a common test runner with a vast ecosystem. Integrating with Turborepo will lead to enormous speed-ups.

## Setting up

Let's say we have a monorepo that looks like this:

package.json

package.json

Install `jest` into the packages where you plan on having test suites. For this example, we will have tests in `web` and `@repo/ui`:

#### pnpm

```
pnpm add jest --save-dev --filter=@repo/ui --filter=web
```

#### yarn

```
yarn workspace web add jest --dev
yarn workspace @repo/ui add jest --dev
```

#### npm

```
npm install jest --workspace=web --workspace=@repo/ui --save-dev
```

#### bun

```
cd apps/web && bun install jest --dev
cd packages/ui && bun install jest --dev
```

Both the `apps/web` and `packages/ui` have their own test suites, so we'll add a `test` script to their `package.json`:

#### web

```
{
  "name": "web",
  "scripts": {
    "test": "jest"
  },
  "devDependencies": {
    "jest": "latest"
  }
}
```

#### @repo/ui

```
{
  "name": "@repo/ui",
  "scripts": {
    "test": "jest"
  },
  "devDependencies": {
    "jest": "latest"
  }
}
```

Inside the root `turbo.json`, create a `test` task:

```
{
  "tasks": {
    "test": {}
  }
}
```

Now, `turbo test` can parallelize and cache all of the test suites from each package, only testing code that has changed.

## Running tests in watch mode

When you run your test suite normally, it completes and outputs to `stdout`. This means you can [cache it](../../crafting-your-repository/caching.md) with Turborepo.

But when you run your tests in a watched mode, the process never exits. This makes a watch task more like a [development task](../../crafting-your-repository/developing-applications.md).

Because of this difference, we recommend specifying **two separate Turborepo tasks**: one for running your tests, and one for running them in Jest's watch mode. Inside your each `package.json` file for each workspace:

#### web

```
{
  "name": "web",
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  },
  "devDependencies": {
    "jest": "latest"
  }
}
```

#### @repo/ui

```
{
  "name": "@repo/ui",
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  },
  "devDependencies": {
    "jest": "latest"
  }
}
```

Inside the root `turbo.json`:

```
{
  "tasks": {
    "test": {},
    "test:watch": {
      "cache": false,
      "persistent": true
    }
  }
}
```

You can now either run this task using [global `turbo`](../../getting-started/installation.md#global-installation) as `turbo test:watch` or from a script in your root `package.json`:

#### Global turbo

```
turbo test
```

```
turbo test:watch
```

#### ./package.json

```
{
  "scripts": {
    "test": "turbo run test",
    "test:watch": "turbo run test:watch"
  }
}
```

## Using with the VS Code Jest extension

The [Jest extension for VS Code](https://github.com/jest-community/vscode-jest) parses JSON output from Jest to discover and display tests. By default, Turborepo prepends a `<package>:<task>:` prefix to log lines, which breaks the extension's ability to parse this output.

To fix this, use the [`--log-prefix=none`](../../reference/run.md#--log-prefix-option) flag in your VS Code settings:

```
{
  "jest.jestCommandLine": "turbo run test --log-prefix=none --"
}
```
