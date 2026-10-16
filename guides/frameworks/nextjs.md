---
url: https://turborepo.dev/docs/guides/frameworks/nextjs
title: "Next.js"
description: "Learn how to use Next.js in a monorepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[Next.js](https://nextjs.org) is the React framework for the web. Used by some of the world's largest companies, Next.js enables you to create high-quality web applications with the power of React components.

## Quickstart

To get started with Next.js in a Turborepo quickly, follow the [quickstart](../../getting-started/installation.md) to create a repository with two Next.js applications:

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest
```

#### npm

```bash title="Terminal"
npx create-turbo@latest
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest
```

## Adding a Next.js application to an existing repository

Use [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app) to set up a new Next.js application in a package. From the root of your repository, run:

#### pnpm

```bash title="Terminal"
pnpm dlx create-next-app@latest apps/my-app
```

#### yarn

```bash title="Terminal"
yarn dlx create-next-app@latest apps/my-app
```

#### npm

```bash title="Terminal"
npx create-next-app@latest apps/my-app
```

#### bun

```bash title="Terminal"
bunx create-next-app@latest apps/my-app
```

#### nub

```bash title="Terminal"
nubx create-next-app@latest apps/my-app
```

#### aube

```bash title="Terminal"
aubx create-next-app@latest apps/my-app
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

When using Next.js with [Turborepo's microfrontends](../microfrontends.md), make sure to set the `basePath` property for child applications. This ensures the assets like images and CSS will be routed to the correct application.

```ts title="./apps/my-app/next.config.ts"
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  basePath: "/docs",
};

export default nextConfig;
```

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
