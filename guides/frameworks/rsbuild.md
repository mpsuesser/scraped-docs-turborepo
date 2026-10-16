---
url: https://turborepo.dev/docs/guides/frameworks/rsbuild
title: "Rsbuild"
description: "Learn more about using Rsbuild in your monorepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[Rsbuild](https://rsbuild.rs/) is an Rspack-based build tool that provides out-of-the-box setup for modern web applications.

## Quickstart

To get started with Rsbuild in a Turborepo quickly, use [the `with-rsbuild` example](https://github.com/vercel/turborepo/tree/main/examples/with-rsbuild):

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest -e with-rsbuild
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest -e with-rsbuild
```

#### npm

```bash title="Terminal"
npx create-turbo@latest -e with-rsbuild
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest -e with-rsbuild
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest -e with-rsbuild
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest -e with-rsbuild
```

## Adding an Rsbuild application to an existing repository

Use [`create-rsbuild`](https://rsbuild.rs/guide/start/quick-start) to set up a new Rsbuild application in a package. From the root of your repository, run:

#### pnpm

```bash title="Terminal"
pnpm dlx create-rsbuild@latest apps/my-app --template react
```

#### yarn

```bash title="Terminal"
yarn dlx create-rsbuild@latest apps/my-app --template react
```

#### npm

```bash title="Terminal"
npx -y create-rsbuild@latest apps/my-app --template react
```

#### bun

```bash title="Terminal"
bunx create-rsbuild@latest apps/my-app --template react
```

#### nub

```bash title="Terminal"
nubx create-rsbuild@latest apps/my-app --template react
```

#### aube

```bash title="Terminal"
aubx create-rsbuild@latest apps/my-app --template react
```

## Integrating with your repository

To add [Internal Packages](../../core-concepts/internal-packages.md) to your new application, install them into the app with your package manager:

#### pnpm

```diff title="./apps/my-app/package.json"
{
  "name": "my-app",
  "dependencies": {
+   "@repo/ui": "workspace:*"
  }
}
```

#### yarn

```diff title="./apps/my-app/package.json"
{
  "name": "my-app",
  "dependencies": {
+   "@repo/ui": "*"
  }
}
```

#### npm

```diff title="./apps/my-app/package.json"
{
 "name": "my-app",
  "dependencies": {
+   "@repo/ui": "*"
  }
}
```

#### bun

```diff title="./apps/my-app/package.json"
{
 "name": "my-app",
  "dependencies": {
+   "@repo/ui": "workspace:*"
  }
}
```

#### nub

```diff title="./apps/my-app/package.json"
{
 "name": "my-app",
  "dependencies": {
+   "@repo/ui": "workspace:*"
  }
}
```

#### aube

```diff title="./apps/my-app/package.json"
{
 "name": "my-app",
  "dependencies": {
+   "@repo/ui": "workspace:*"
  }
}
```

Make sure to run your package manager's install command. You also may need to update `scripts` in `package.json` to fit your use case in your repository.

### Customizing tasks

By default, the new application will use the tasks defined in the root `turbo.json`. If you'd like to configure tasks differently for the new application, use [Package Configurations](../../reference/package-configurations.md).

### Microfrontends

When using Rsbuild with [Turborepo's microfrontends](../microfrontends.md), set `server.base` for child applications. Rsbuild uses `server.base` as the default asset prefix for both development and production assets.

```ts title="./apps/my-app/rsbuild.config.ts"
import { defineConfig } from "@rsbuild/core";

export default defineConfig({
  server: {
    base: "/admin",
  },
});
```

### Module Federation

For runtime composition with Rsbuild, use [the `with-rsbuild-module-federation` example](https://github.com/vercel/turborepo/tree/main/examples/with-rsbuild-module-federation).

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest -e with-rsbuild-module-federation
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest -e with-rsbuild-module-federation
```

#### npm

```bash title="Terminal"
npx create-turbo@latest -e with-rsbuild-module-federation
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest -e with-rsbuild-module-federation
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest -e with-rsbuild-module-federation
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest -e with-rsbuild-module-federation
```

The example includes a `react-host` Rsbuild app, a `react-remote` Rsbuild app, and an `@mf-rsbuild-example/shared-ui` package. The host consumes the remote's exposed `./remote-app` module through `mf-manifest.json`, and both apps share React dependencies through [Module Federation](https://module-federation.io/).

During development, run `turbo dev` from the example root. The host uses Rsbuild's default port `3000` and the remote runs on `localhost:3001`.

```json title="./turbo.json"
{
  "tasks": {
    "dev": {
      "cache": false,
      "persistent": true,
      "dependsOn": ["^build"]
    },
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", "*.tsbuildinfo"]
    }
  }
}
```

Keep `dev` uncached and persistent for Rsbuild servers. Use `dependsOn: ["^build"]` so shared packages are built before the host and remote start.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
