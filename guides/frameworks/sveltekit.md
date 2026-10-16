---
url: https://turborepo.dev/docs/guides/frameworks/sveltekit
title: "SvelteKit"
description: "Learn more about using SvelteKit in your monorepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[SvelteKit](https://kit.svelte.dev/) is a framework for rapidly developing robust, performant web applications using Svelte.

## Quickstart

To get started with SvelteKit in a Turborepo quickly, use [the `with-svelte` example](https://github.com/vercel/turborepo/tree/main/examples/with-svelte):

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest -e with-svelte
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest -e with-svelte
```

#### npm

```bash title="Terminal"
npx create-turbo@latest -e with-svelte
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest -e with-svelte
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest -e with-svelte
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest -e with-svelte
```

## Adding a SvelteKit application to an existing repository

Use [`npm create svelte`](https://kit.svelte.dev/docs/creating-a-project) to set up a new SvelteKit application in a package. From the root of your repository, run:

#### pnpm

```bash title="Terminal"
pnpm dlx sv create
```

#### yarn

```bash title="Terminal"
yarn dlx sv create
```

#### npm

```bash title="Terminal"
npx sv create
```

#### bun

```bash title="Terminal"
bunx sv create
```

#### nub

```bash title="Terminal"
nubx sv create
```

#### aube

```bash title="Terminal"
aubx sv create
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

When using Svelte with [Turborepo's microfrontends](../microfrontends.md), make sure to set the `base` property for child applications. This ensures the assets like images and CSS will be routed to the correct application.

```ts title="./apps/my-app/vite.config.ts"
import { defineConfig } from "vite";

export default defineConfig({
  base: "/admin",
});
```

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
