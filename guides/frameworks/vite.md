---
url: https://turborepo.dev/docs/guides/frameworks/vite
title: "Vite"
description: "Learn more about using Vite in your monorepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[Vite](https://vitejs.dev/) is a build tool that aims to provide a faster and leaner development experience for modern web projects.

## Quickstart

To get started with Vite in a Turborepo quickly, use [the `with-vite` example](https://github.com/vercel/turborepo/tree/main/examples/with-vite):

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest -e with-vite
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest -e with-vite
```

#### npm

```bash title="Terminal"
npx create-turbo@latest -e with-vite
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest -e with-vite
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest -e with-vite
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest -e with-vite
```

## Adding a Vite application to an existing repository

Use [`npm create vite`](https://vitejs.dev/guide/#scaffolding-your-first-vite-project) to set up a new Vite application in a package. From the root of your repository, run:

#### pnpm

```bash title="Terminal"
pnpm create vite@latest apps/my-app
```

#### yarn

```bash title="Terminal"
yarn create vite@latest apps/my-app
```

#### npm

```bash title="Terminal"
npm create vite@latest apps/my-app
```

#### bun

```bash title="Terminal"
bun create vite@latest apps/my-app
```

#### nub

```bash title="Terminal"
nubx create-vite@latest apps/my-app
```

#### aube

```bash title="Terminal"
aubx create-vite@latest apps/my-app
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

When using Vite with [Turborepo's microfrontends](../microfrontends.md), make sure to set the `base` property for child applications. This ensures the assets like images and CSS will be routed to the correct application.

```ts title="./apps/my-app/vite.config.ts"
import { defineConfig } from "vite";

export default defineConfig({
  base: "/admin",
});
```

### Module Federation

For runtime composition with Vite, use [the `with-vite-module-federation` example](https://github.com/vercel/turborepo/tree/main/examples/with-vite-module-federation). Module Federation works with many frameworks and libraries, including React, Vue, Svelte, Solid, Preact, and many others. See the [Module Federation Vite working implementations](https://github.com/module-federation/vite/#working-implementations) for more examples.

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest -e with-vite-module-federation
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest -e with-vite-module-federation
```

#### npm

```bash title="Terminal"
npx create-turbo@latest -e with-vite-module-federation
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest -e with-vite-module-federation
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest -e with-vite-module-federation
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest -e with-vite-module-federation
```

The example includes a `react-host` Vite app, a `react-remote` Vite app, and an `@mf-vite-example/shared-ui` package. The host consumes the remote's exposed `./remote-app` module and both apps share React dependencies through [Module Federation](https://module-federation.io/).

During development, run `turbo dev` from the example root. The host runs on `localhost:4173` and the remote runs on `localhost:4174`.

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

Keep `dev` uncached and persistent for Vite servers. Use `dependsOn: ["^build"]` so shared packages are built before the host and remote start.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
