---
url: https://turborepo.dev/docs/guides/ci-vendors
title: "Continuous Integration"
description: "Browse CI provider recipes for using Turborepo with Remote Caching in your pipelines."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Recipes for using Turborepo with Vercel, GitHub Actions, and other continuous integration providers.

Turborepo not only speeds up builds, but also the rest of your Continuous Integration pipeline by using [Remote Caching](../core-concepts/remote-caching.md). Below are a few platform recipes to use Turborepo with your CI providers.

## General Setup

To enable Remote Caching for your CI:

1. Setup the environment variables for Turborepo in your CI to access your Remote Cache.
	| Variable | Description |
	| --- | --- |
	| `TURBO_TOKEN` | The Bearer token to access the Remote Cache |
	| `TURBO_TEAM` | The account name associated with your repository. When using [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching#vercel-remote-cache), this is [your team's slug](https://vercel.com/d?to=%2F%5Bteam%5D%2F%7E%2Fsettings&title=Get+team+slug). |
2. Clone your repository.
3. Install your dependencies through your package manager.
4. Run your tasks through `turbo`.
