---
url: https://turborepo.dev/docs/guides/ci-vendors/github-actions
title: "GitHub Actions"
description: "Configure GitHub Actions workflows to run Turborepo tasks with Remote Caching."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Learn how to use GitHub Actions with Turborepo.

The following example shows how to use Turborepo with [GitHub Actions](https://github.com/features/actions).

For a given root `package.json`:

```
{
  "name": "my-turborepo",
  "scripts": {
    "build": "turbo run build",
    "test": "turbo run test"
  },
  "devDependencies": {
    "turbo": "latest"
  }
}
```

And a `turbo.json`:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**", "other-output-dirs/**"],
      "dependsOn": ["^build"]
    },
    "test": {
      "dependsOn": ["^build"]
    }
  }
}
```

Create a file called `.github/workflows/ci.yml` in your repository with the following contents:

#### pnpm

```
name: CI

on:
  push:
    branches: ["main"]
  pull_request:
    types: [opened, synchronize]

jobs:
  build:
    name: Build and Test
    timeout-minutes: 15
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v4
        with:
          fetch-depth: 2

      - uses: pnpm/action-setup@v3
        with:
          version: 8

      - name: Setup Node.js environment
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install

      - name: Build
        run: pnpm build

      - name: Test
        run: pnpm test
```

#### yarn

```
name: CI

on:
  push:
    branches: ["main"]
  pull_request:
    types: [opened, synchronize]

jobs:
  build:
    name: Build and Test
    timeout-minutes: 15
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v4
        with:
          fetch-depth: 2

      - name: Setup Node.js environment
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'yarn'

      - name: Install dependencies
        run: yarn

      - name: Build
        run: yarn build

      - name: Test
        run: yarn test
```

#### npm

```
name: CI

on:
  push:
    branches: ["main"]
  pull_request:
    types: [opened, synchronize]

jobs:
  build:
    name: Build and Test
    timeout-minutes: 15
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v4
        with:
          fetch-depth: 2

      - name: Setup Node.js environment
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build

      - name: Test
        run: npm run test
```

#### bun

```
name: CI

on:
  push:
    branches: ["main"]
  pull_request:
    types: [opened, synchronize]

jobs:
  build:
    name: Build and Test
    timeout-minutes: 15
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v4
        with:
          fetch-depth: 2

      - uses: oven-sh/setup-bun@v2

      - name: Setup Node.js environment
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: bun install

      - name: Build
        run: bun run build

      - name: Test
        run: bun run test
```

## Remote Caching with Vercel Remote Cache

There are two ways to authenticate to [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching) from GitHub Actions:

- **OpenID Connect (OIDC)**: configure a policy that allows exchanging the CI/CD provider's OIDC tokens for short-lived Turborepo access tokens that grant access to Remote Cache (recommended).
- **Personal Access Token (PAT)**: configure a long-lived, team-scoped PAT as a secret in your CI/CD provider (use when OIDC is not an option).

For the full setup, see [Use Remote Caching from external CI/CD](https://vercel.com/docs/monorepos/remote-caching/external-ci-cd). The GitHub Actions workflow configuration is shown below.

### Using OpenID Connect (recommended)

Follow [these instructions](https://vercel.com/docs/monorepos/remote-caching/external-ci-cd#openid-connect-oidc) to create an OIDC policy on your Vercel team and configure your `TURBO_TEAM` variable. Then add [`vercel/setup-turborepo-remote-cache-action`](https://github.com/vercel/setup-turborepo-remote-cache-action) before any step that runs `turbo`. It requests a GitHub OIDC token, exchanges it for a short-lived Turborepo access token, and sets `TURBO_TOKEN` and `TURBO_TEAM` environment variables for later steps that run `turbo`:

```
# ...
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write
    steps:
      - uses: actions/checkout@v4
      - name: Set up Turborepo Remote Cache
        uses: vercel/setup-turborepo-remote-cache-action@v1.0.0
        with:
          team: ${{ vars.TURBO_TEAM }}
    # ...
```

### Using a Personal Access Token

Follow [these instructions](https://vercel.com/docs/monorepos/remote-caching/external-ci-cd#personal-access-token-pat) to create a Personal Access Token, configure your `TURBO_TOKEN` secret, and configure your `TURBO_TEAM` variable. Then provide them to jobs that run `turbo`:

```
# ...
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
      TURBO_TEAM: ${{ vars.TURBO_TEAM }}
    steps:
      - uses: actions/checkout@v4
    # ...
```

## Using Remote Caching with Reusable Workflows

For information on passing secrets to reusable workflows, see [GitHub's documentation on passing secrets to nested workflows](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows#passing-secrets-to-nested-workflows).

## Remote Caching with GitHub actions/cache

The following steps show how you could use [actions/cache](https://github.com/actions/cache) to cache your monorepo artifacts on GitHub.

Supply a package.json script that will run tasks using Turborepo.

Example `package.json` with a `build` script:

```
{
  "name": "my-turborepo",
  "scripts": {
    "build": "turbo run build"
  },
  "devDependencies": {
    "turbo": "1.2.5"
  }
}
```

Configure your GitHub pipeline with a step which uses the `actions/cache@v4` action before the build steps of your CI file.

- Make sure that the `path` attribute set within the `actions/cache` action matches the output location above. In the example below, `path` was set to `.turbo`.
- State the cache key for the current run under the `key` attribute. In the example below, we used a combination of the runner OS and GitHub SHA as the cache key.
- State the desired cache prefix pattern under the `restore-keys` attribute. Make sure this pattern will remain valid for future CI runs. In the example below, we used the `${{ runner.os }}-turbo-` as the cache key prefix pattern to search against. This allows us to hit the cache on any subsequent CI runs despite `github.sha` changing.

Example `ci` yaml with `.turbo` as chosen cache folder:

```
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4

      - name: Cache turbo build setup
        uses: actions/cache@v4
        with:
          path: .turbo
          key: ${{ runner.os }}-turbo-${{ github.sha }}
          restore-keys: |
            ${{ runner.os }}-turbo-

      - name: Setup Node.js environment
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build
```
