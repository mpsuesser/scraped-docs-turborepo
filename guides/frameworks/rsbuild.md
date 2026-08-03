---
url: https://turborepo.dev/docs/guides/frameworks/rsbuild
title: "Rsbuild"
description: "Add and configure Rsbuild applications in your Turborepo monorepo."
access_date: 2026-08-03T19:40:59.569Z
current_date: 2026-08-03T19:40:59.569Z
---

Learn more about using Rsbuild in your monorepo.

[Rsbuild](https://rsbuild.rs/) is an Rspack-based build tool that provides out-of-the-box setup for modern web applications.

## Quickstart

To get started with Rsbuild in a Turborepo quickly, use [the `with-rsbuild` example](https://github.com/vercel/turborepo/tree/main/examples/with-rsbuild):

#### pnpm

```
pnpm dlx create-turbo@latest -e with-rsbuild
```

#### yarn

```
yarn dlx create-turbo@latest -e with-rsbuild
```

#### npm

```
npx create-turbo@latest -e with-rsbuild
```

#### bun

```
bunx create-turbo@latest -e with-rsbuild
```

## Adding an Rsbuild application to an existing repository

Use [`create-rsbuild`](https://rsbuild.rs/guide/start/quick-start) to set up a new Rsbuild application in a package. From the root of your repository, run:

#### pnpm

```
pnpm dlx create-rsbuild@latest apps/my-app --template react
```

#### yarn

```
yarn dlx create-rsbuild@latest apps/my-app --template react
```

#### npm

```
npx -y create-rsbuild@latest apps/my-app --template react
```

#### bun

```
bunx create-rsbuild@latest apps/my-app --template react
```

## Integrating with your repository

To add [Internal Packages](../../core-concepts/internal-packages.md) to your new application, install them into the app with your package manager:

#### pnpm

```
{
  "name": "my-app",
  "dependencies": {
+   "@repo/ui": "workspace:*"
  }
}
```

#### yarn

```
{
  "name": "my-app",
  "dependencies": {
+   "@repo/ui": "*"
  }
}
```

#### npm

```
{
 "name": "my-app",
  "dependencies": {
+   "@repo/ui": "*"
  }
}
```

#### bun

```
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

```
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

```
pnpm dlx create-turbo@latest -e with-rsbuild-module-federation
```

#### yarn

```
yarn dlx create-turbo@latest -e with-rsbuild-module-federation
```

#### npm

```
npx create-turbo@latest -e with-rsbuild-module-federation
```

#### bun

```
bunx create-turbo@latest -e with-rsbuild-module-federation
```

The example includes a `react-host` Rsbuild app, a `react-remote` Rsbuild app, and an `@mf-rsbuild-example/shared-ui` package. The host consumes the remote's exposed `./remote-app` module through `mf-manifest.json`, and both apps share React dependencies through [Module Federation](https://module-federation.io/).

During development, run `turbo dev` from the example root. The host uses Rsbuild's default port `3000` and the remote runs on `localhost:3001`.

```
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
