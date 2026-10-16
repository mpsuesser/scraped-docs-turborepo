---
url: https://turborepo.dev/docs/guides/tools/jest
title: "Jest"
description: "Learn how to use Jest in a Turborepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[Jest](https://jestjs.io/) is a common test runner with a vast ecosystem. Integrating with Turborepo will lead to enormous speed-ups.

## Setting up

Let's say we have a monorepo that looks like this:

- `apps/`
  - `web/`
    - `package.json`
- `packages/`
  - `ui/`
    - `package.json`

Install `jest` into the packages where you plan on having test suites. For this example, we will have tests in `web` and `@repo/ui`:

#### pnpm

```bash title="Terminal"
pnpm add jest --save-dev --filter=@repo/ui --filter=web
```

#### yarn

```bash title="Terminal"
yarn workspace web add jest --dev
yarn workspace @repo/ui add jest --dev
```

#### npm

```bash title="Terminal"
npm install jest --workspace=web --workspace=@repo/ui --save-dev
```

#### bun

```bash title="Terminal"
cd apps/web && bun install jest --dev
cd packages/ui && bun install jest --dev
```

#### nub

```bash title="Terminal"
cd apps/web && nub add jest --save-dev
cd packages/ui && nub add jest --save-dev
```

#### aube

```bash title="Terminal"
cd apps/web && aube add jest --save-dev
cd packages/ui && aube add jest --save-dev
```

Both the `apps/web` and `packages/ui` have their own test suites, so we'll add a `test` script to their `package.json`:

#### web

```json title="./apps/web/package.json"
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

```json title="./packages/ui/package.json"
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

```json title="./turbo.json"
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

```json title="./apps/web/package.json"
{
  "name": "web",
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch" // [!code highlight]
  },
  "devDependencies": {
    "jest": "latest"
  }
}
```

#### @repo/ui

```json title="./packages/ui/package.json"
{
  "name": "@repo/ui",
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch" // [!code highlight]
  },
  "devDependencies": {
    "jest": "latest"
  }
}
```

Inside the root `turbo.json`:

```json title="./turbo.json"
{
  "tasks": {
    "test": {},
    "test:watch": {
      "cache": false, // [!code highlight]
      "persistent": true // [!code highlight]
    }
  }
}
```

You can now either run this task using [global `turbo`](../../getting-started/installation.md#global-installation) as `turbo test:watch` or from a script in your root `package.json`:

#### Global turbo

```bash title="Terminal"
turbo test
```

```bash title="Terminal"
turbo test:watch
```

#### ./package.json

```json title="./package.json"
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

```json title=".vscode/settings.json"
{
  "jest.jestCommandLine": "turbo run test --log-prefix=none --"
}
```

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
