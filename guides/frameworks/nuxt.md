---
url: https://turborepo.dev/docs/guides/frameworks/nuxt
title: "Nuxt"
description: "Add and configure Nuxt applications in your Turborepo monorepo."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
---

Learn more about using Nuxt in your monorepo.

[Nuxt](https://nuxt.com/) is an open source framework that makes web development intuitive and powerful.

## Quickstart

To get started with Nuxt in a Turborepo quickly, use [the `with-vue-nuxt` example](https://github.com/vercel/turborepo/tree/main/examples/with-vue-nuxt):

#### pnpm

```
pnpm dlx create-turbo@latest -e with-vue-nuxt
```

#### yarn

```
yarn dlx create-turbo@latest -e with-vue-nuxt
```

#### npm

```
npx create-turbo@latest -e with-vue-nuxt
```

#### bun

```
bunx create-turbo@latest -e with-vue-nuxt
```

## Adding a Nuxt application to an existing repository

Use [Nuxi](https://www.npmjs.com/package/nuxi), Nuxt's CLI, to set up a new Nuxt application in a package. From the root of your repository, run:

#### pnpm

```
pnpm dlx nuxi@latest init apps/my-app
```

#### yarn

```
yarn dlx nuxi@latest init apps/my-app
```

#### npm

```
npx nuxi@latest init apps/my-app
```

#### bun

```
bunx nuxi@latest init apps/my-app
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

When using Nuxt with [Turborepo's microfrontends](../microfrontends.md), make sure to set the `base` property for child applications. This ensures the assets like images and CSS will be routed to the correct application.

```
import { defineConfig } from "vite";

export default defineConfig({
  base: "/admin",
});
```
