---
url: https://turborepo.dev/docs/guides/ci-vendors/vercel
title: "Vercel"
description: "Deploy your Turborepo on Vercel with zero-config Remote Caching."
access_date: 2026-08-03T23:32:48.885Z
current_date: 2026-08-03T23:32:48.885Z
---

Learn how to use Turborepo on Vercel.

![](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fturborepo-x-vercel.3ei-d2654h24u.png&w=3840&q=75&dpl=dpl_2gHs6x7AmLDbozVYgS8nTZ4zEmmp)

Vercel's zero-config integration with Turborepo automatically understands your monorepo.

To deploy your Turborepo on Vercel, [create a new project](https://vercel.com/new) and import your code. Your projects will be pre-configured with the correct settings to use the [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching).

For more information about deploying your Turborepo to Vercel, [visit the Vercel documentation](https://vercel.com/docs/concepts/monorepos/turborepo).

## Filtered installs

You can speed up installs by only installing the dependencies for the application being deployed and its Workspace dependencies. Set a custom Install Command in the application's `vercel.json` or [in your project's Build and Deployment settings](https://vercel.com/d?to=%2F%5Bteam%5D%2F%5Bproject%5D%2Fsettings%2Fbuild-and-deployment%23framework-settings&title=Build+and+Deployment+settings):

#### pnpm

```
{
  "installCommand": "pnpm install --filter web..."
}
```

#### yarn

```
{
  "installCommand": "yarn workspaces focus web"
}
```

#### npm

```
{
  "installCommand": "npm install --workspace=web"
}
```

#### bun

```
{
  "installCommand": "bun install --filter web"
}
```
