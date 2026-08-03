---
url: https://turborepo.dev/docs/guides/frameworks/sveltekit
title: "SvelteKit"
description: "Add and configure SvelteKit applications in your Turborepo monorepo."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

Learn more about using SvelteKit in your monorepo.

[SvelteKit](https://kit.svelte.dev/) is a framework for rapidly developing robust, performant web applications using Svelte.

## Quickstart

To get started with SvelteKit in a Turborepo quickly, use [the `with-svelte` example](https://github.com/vercel/turborepo/tree/main/examples/with-svelte):

#### pnpm

```
pnpm dlx create-turbo@latest -e with-svelte
```

#### yarn

```
yarn dlx create-turbo@latest -e with-svelte
```

#### npm

```
npx create-turbo@latest -e with-svelte
```

#### bun

```
bunx create-turbo@latest -e with-svelte
```

## Adding a SvelteKit application to an existing repository

Use [`npm create svelte`](https://kit.svelte.dev/docs/creating-a-project) to set up a new SvelteKit application in a package. From the root of your repository, run:

#### pnpm

```
pnpm dlx sv create
```

#### yarn

```
yarn dlx sv create
```

#### npm

```
npx sv create
```

#### bun

```
bunx sv create
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

When using Svelte with [Turborepo's microfrontends](../microfrontends.md), make sure to set the `base` property for child applications. This ensures the assets like images and CSS will be routed to the correct application.

```
import { defineConfig } from "vite";

export default defineConfig({
  base: "/admin",
});
```

[PreviousNext.js](nextjs.md) [NextVite](vite.md)
