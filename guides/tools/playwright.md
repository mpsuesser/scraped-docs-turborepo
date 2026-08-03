---
url: https://turborepo.dev/docs/guides/tools/playwright
title: "Playwright"
description: "Set up Playwright end-to-end test suites with proper caching and dependency graph configuration."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
---

Learn how to use Playwright in a Turborepo.

[Playwright](https://playwright.dev/) enables reliable end-to-end testing for modern web apps.

We recommend creating a Playwright package for each test suite that you'd like to run in your monorepo. This may mean suites broken up per-application, per-domain, or some other scheme, according to your needs. If you are unsure, start with creating a Playwright package per-application.

## Handling Playwright's environment variables

Playwright requires several environment variables to run correctly. To ensure that these variables are available within your tasks in [Strict Mode](../../crafting-your-repository/using-environment-variables.md#strict-mode), you'll want to add them to your pass through variables using [`passThroughEnv`](../../reference/configuration.md#passthroughenv) in your end-to-end task or [`globalPassThroughEnv`](../../reference/configuration.md#globalpassthroughenv) globally, depending on your scoping preferences.

#### Only in end-to-end task

The configuration below using `passThroughEnv` will allow environment variables that start with `PLAYWRIGHT_` into **the `e2e` task** and **will not affect hashing**.

```
{
  "tasks": {
    "e2e": {
      "passThroughEnv": ["PLAYWRIGHT_*"]
    }
  }
}
```

#### Globally

The configuration below using `globalPassThroughEnv` will allow environment variables that start with `PLAYWRIGHT_` into **all tasks** and **will not affect hashing**.

```
{
  "globalPassThroughEnv": ["PLAYWRIGHT_*"]
}
```

Note that pass through variables are used since we don't want to miss cache in situations where these Playwright-internal variables change. For example,`PLAYWRIGHT_BROWSERS_PATH` is used to locate browser binaries used by Playwright, and we don't want to miss cache if this location changes.

## Designing the task graph

We want to ensure the proper caching behavior for our end-to-end suites. Specifically, we want to make sure of cache misses in a few key situations:

- If test suites change, cache gets missed.
- If code tested by the suites changes, cache gets missed.

The first requirement will be met naturally since the hash for the task will change when test code is changed. However, the second requirement means you need to ensure end-to-end tests depend on changes in application source code.

This relationship can be expressed in `turbo.json` and the end-to-end suite's `package.json`.

#### turbo.json

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    },
    "e2e": {
      "dependsOn": ["^build"]
    }
  }
}
```

#### package.json

```
{
  "name": "@repo/playwright-web",
  "dependencies": {
    "web": "workspace:*"
  }
}
```

Later on, when you want to run your end-to-end tests, use [the `--only` flag](../../reference/run.md#--only) to run end-to-end tests without running the application's build first. As an example, your command may look like `turbo run e2e --filter=@repo/playwright-myapp --only`.

## Sharing Playwright utilities

You can also create a common package for shared utilities that you need in your end-to-end test suites. We recommend using `peerDependencies` in this shared package so that you can get access to Playwright used in consumers without having to install Playwright into the shared package itself.

#### pnpm

```
{
  "name": "@repo/playwright-utilities",
  "peerDependencies": {
    "playwright": "workspace:*"
}
}
```

#### yarn

```
{
  "name": "@repo/playwright-utilities",
  "peerDependencies": {
    "playwright": "*"
}
}
```

#### npm

```
{
  "name": "@repo/playwright-utilities",
  "peerDependencies": {
    "playwright": "*"
  }
}
```

#### bun

```
{
  "name": "@repo/playwright-utilities",
  "peerDependencies": {
    "playwright": "workspace:*"
  }
}
```
