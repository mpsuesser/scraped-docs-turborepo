---
url: https://turborepo.dev/docs/reference/turbo-ignore
title: "turbo-ignore"
description: "Reference for the `turbo-ignore` package that determines whether to skip CI tasks based on package changes."
access_date: 2026-08-03T18:24:07.617Z
current_date: 2026-08-03T18:24:07.617Z
---

Learn how to use turbo-ignore to skip tasks in CI.

Deprecated

Use `turbo` to determine if a package or its dependencies have changes. This can be useful for quickly skipping tasks in CI.

```
npx turbo-ignore [workspace] [flags...]
```

To learn more, visit:

- [The introductory guide to skipping tasks](../crafting-your-repository/constructing-ci.md#skipping-tasks-and-other-unnecessary-work)
- [The advanced guide for skipping tasks](../guides/skipping-tasks.md)
- [Documentation for `turbo-ignore` on npm](https://www.npmjs.com/package/turbo-ignore)

## turbo-ignore versioning

Because `turbo-ignore` is most often used before installing dependencies into the repository, there won't be a `turbo` binary available when you run `turbo-ignore`. Instead `turbo-ignore` will search for the correct version to use with your repository with the following strategy:

- First, check for a `turbo` entry in root `package.json#devDependencies` or `package.json#dependencies`. If a version is found there, it will be used.
- If no `entry` is found in `package.json`, `turbo.json` will be read for its schema. If [the `tasks` key](configuration.md#tasks) is found, use `turbo@^2`. If the `pipeline` from Turborepo v1 is found, use `turbo@^1`.
