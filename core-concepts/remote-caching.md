---
url: https://turborepo.dev/docs/core-concepts/remote-caching
title: "Remote Caching"
description: "Share a single Turborepo cache across your team and CI to avoid duplicated work."
access_date: 2026-08-27T13:03:50.723Z
current_date: 2026-08-27T13:03:50.723Z
---

Share cache artifacts across machines for even faster builds.

Turborepo's [task cache](../crafting-your-repository/caching.md) saves time by never doing the same work twice.

But there's a problem: **the cache is local to your machine**. When you're working with a Continuous Integration system, this can result in a lot of duplicated work:

Since Turborepo only caches to the local filesystem by default, the same task (`turbo run build`) must be **re-executed on each machine** (by you, by your teammates, by your CI, by your PaaS, etc.) even when all of the task inputs are identical — which **wastes time and resources**.

## A single, shared cache

What if you could share a single Turborepo cache across your entire team (and even your CI)?

Turborepo can securely communicate with a remote cache - a cloud server that stores the results of your tasks. This can save enormous amounts of time by **preventing duplicated work across your entire organization**.

Remote Caching is free and can be used with both [managed providers](remote-caching.md#managed-remote-cache-with-vercel) or as a [self-hosted cache](remote-caching.md#self-hosting).

## Vercel

[Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching) is free to use on all plans, even if you do not host your applications on Vercel. Follow the steps below to enable Remote Caching for your repository.

### For Local Development

To link your local Turborepo to your Remote Cache, authenticate the Turborepo CLI with your Vercel account:

```
turbo login
```

You can also use your package manager if you do not have [global `turbo`](../getting-started/installation.md#global-installation) installed:

#### pnpm

```
pnpm dlx turbo login
```

#### yarn

```
yarn dlx turbo login
```

#### npm

```
npx turbo login
```

#### bun

```
bunx turbo login
```

Now, link your Turborepo to your Remote Cache:

```
turbo link
```

Once enabled, make some changes to a package you are currently caching and run tasks against it with `turbo run`. Your cache artifacts will now be stored locally *and* in your Remote Cache.

To verify, delete your local Turborepo cache with:

#### UNIX

```
rm -rf ./.turbo/cache
```

#### Windows

```
rd /s /q "./.turbo/cache"
```

Then, run the same build again. If things are working properly, `turbo` should not execute tasks locally. Instead, it will download the logs and artifacts from your Remote Cache and replay them back to you.

### Remote Caching on Vercel

If you are building and hosting your apps on Vercel, Remote Caching will be automatically set up on your behalf once you use `turbo`. Refer to the [Vercel documentation](https://vercel.com/docs/monorepos/remote-caching?utm_source=turborepo.dev&utm_medium=referral&utm_campaign=docs-link) for more information.

### Artifact Integrity and Authenticity Verification

Turborepo can sign artifacts with a secret key before uploading them to the Remote Cache. Turborepo uses `HMAC-SHA256` signatures on artifacts using a secret key you provide. Turborepo will verify the Remote Cache artifacts' integrity and authenticity when they're downloaded. Any artifacts that fail to verify will be ignored and treated as a cache miss by Turborepo.

To enable this feature, set the `remoteCache` options on your `turbo.json` config to include `signature: true`. Then specify your secret key by declaring the `TURBO_REMOTE_CACHE_SIGNATURE_KEY` environment variable.

```
{
  "remoteCache": {
    "signature": true,
  },
}
```

## Remote Cache API

A Remote Cache can be implemented by any HTTP server that meets Turborepo's Remote Caching API specification.

### Managed Remote Cache with Vercel

[Vercel](https://vercel.com/), the creators and maintainers of Turborepo, provide a managed Remote Cache that is fully compatible with Turborepo.

Using [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching) is zero-configuration and automatically integrates with [Vercel deployments](https://vercel.com/docs/deployments) through the open-source [Vercel Remote Cache SDK](https://github.com/vercel/remote-cache).

Learn more about [Turborepo on Vercel](https://vercel.com/docs/monorepos/turborepo) or [deploy a template for free](https://vercel.com/templates?search=turborepo) to try it out.

### Self-hosting

You can also self-host your own Remote Cache and log into it using the `--manual` flag to provide API URL, team, and token information.

```
turbo login --manual
```

#### OpenAPI specification

- [Human-readable viewer](../openapi.md)
- [JSON](https://turborepo.dev/api/remote-cache-spec)

At this time, all versions of `turbo` are compatible with the `v8` endpoints.

#### Community implementations

The Turborepo community has created open-source implementations of the Remote Cache.
