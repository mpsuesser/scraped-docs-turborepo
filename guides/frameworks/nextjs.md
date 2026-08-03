---
url: https://turborepo.dev/docs/guides/frameworks/nextjs
title: "Next.js"
description: "Add and configure Next.js applications in your Turborepo monorepo."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Learn how to use Next.js in a monorepo.

[Next.js](https://nextjs.org/) is the React framework for the web. Used by some of the world's largest companies, Next.js enables you to create high-quality web applications with the power of React components.

## Quickstart

To get started with Next.js in a Turborepo quickly, follow the [quickstart](../../getting-started/installation.md) to create a repository with two Next.js applications:

#### pnpm

```
pnpm dlx create-turbo@latest
```

#### yarn

```
yarn dlx create-turbo@latest
```

#### npm

```
npx create-turbo@latest
```

#### bun

```
bunx create-turbo@latest
```

## Adding a Next.js application to an existing repository

Use [`create-next-app`](https://nextjs.org/docs/app/api-reference/create-next-app) to set up a new Next.js application in a package. From the root of your repository, run:

#### pnpm

```
pnpm dlx create-next-app@latest apps/my-app
```

#### yarn

```
yarn dlx create-next-app@latest apps/my-app
```

#### npm

```
npx create-next-app@latest apps/my-app
```

#### bun

```
bunx create-next-app@latest apps/my-app
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

When using Next.js with [Turborepo's microfrontends](../microfrontends.md), make sure to set the `basePath` property for child applications. This ensures the assets like images and CSS will be routed to the correct application.

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  basePath: "/docs",
};

export default nextConfig;
```
