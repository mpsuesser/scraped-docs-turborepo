---
url: https://turborepo.dev/docs/reference/turbo-codemod
title: "@turbo/codemod"
description: "Reference for the `@turbo/codemod` package that automates Turborepo version migrations."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

Learn more about how Turborepo uses codemods to make version migrations easy.

Turborepo provides codemod transformations and automatic migration scripts to help upgrade your Turborepo codebase when a feature is deprecated.

Codemods are transformations that run on your codebase programmatically. This allows for a large amount of changes to be applied without having to manually manage repetitive changes.

## Usage

First, ensure that you've run your package manager's install command.

```
npx @turbo/codemod [transform] [path] [--dry] [--print]
```

- `transform` - name of transform, see available transforms below.
- `path` - files or directory to transform
- `--dry` - Do a dry-run (Code will not be edited)
- `--print` - Prints the changed output for comparison

## Upgrading Turborepo versions

In most cases, you can run:

```
npx @turbo/codemod migrate
```

All the codemods you need to upgrade will be run for you.

### Turborepo 2.x

The codemods below are used for migration paths in the second major version of Turborepo.

### Turborepo 1.x

The codemods below are used for migration paths in the first major version of Turborepo.
