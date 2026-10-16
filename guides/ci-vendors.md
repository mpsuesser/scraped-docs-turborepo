---
url: https://turborepo.dev/docs/guides/ci-vendors
title: "Continuous Integration"
description: "Recipes for using Turborepo with Vercel, GitHub Actions, and other continuous integration providers."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Turborepo not only speeds up builds, but also the rest of your Continuous Integration pipeline by using [Remote Caching](../core-concepts/remote-caching.md). Below are a few platform recipes to use Turborepo with your CI providers.

#### [Vercel (Zero configuration)](ci-vendors/vercel.md)

#### [GitHub Actions](ci-vendors/github-actions.md)

#### [CircleCI](ci-vendors/circleci.md)

#### [GitLab CI](ci-vendors/gitlab-ci.md)

#### [Travis CI](ci-vendors/travis-ci.md)

#### [Buildkite](ci-vendors/buildkite.md)

## General Setup

To enable Remote Caching for your CI:

1. Setup the environment variables for Turborepo in your CI to access your Remote Cache.
   | Variable      | Description                                                                                                                                                                                                                                                                                                             |
   | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | `TURBO_TOKEN` | The Bearer token to access the Remote Cache                                                                                                                                                                                                                                                                             |
   | `TURBO_TEAM`  | The account name associated with your repository. When using <a href="https://vercel.com/docs/monorepos/remote-caching#vercel-remote-cache" rel="noreferrer noopener" target="_blank">Vercel Remote Cache</a>, this is [your team's slug](https://vercel.com/d?to=%2F%5Bteam%5D%2F%7E%2Fsettings\&title=Get+team+slug). |
2. Clone your repository.
3. Install your dependencies through your package manager.
4. Run your tasks through `turbo`.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
