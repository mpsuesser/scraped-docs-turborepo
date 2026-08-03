---
url: https://turborepo.dev/docs/getting-started/editor-integration
title: "Editor integration"
description: "Set up JSON Schema validation, ESLint integration, and the Turborepo LSP in your editor."
access_date: 2026-08-03T19:40:59.569Z
current_date: 2026-08-03T19:40:59.569Z
---

To get the best experience with `turbo`, Turborepo provides a few utilities for integrating with your editor.

## JSON Schema for turbo.json

Turborepo uses [JSON Schema](https://json-schema.org/) to give you auto-complete in your `turbo.json` files. By including the `$schema` key in your `turbo.json`, your editor is able to provide full documentation and linting for invalid configuration.

### Sourcing from web

Starting with Turborepo 2.5.7, versioned schemas are available via subdomain, following the format `https://v<version>.turborepo.dev/schema.json`. The version uses a dash separator.

```
{
  "$schema": "https://v2-5-7.turborepo.dev/schema.json"
}
```

This has the advantage of not needing to run your package manager's install command to see in-editor validation, while also ensuring you're using the schema that matches your installed version of `turbo`.

Unversioned schema

### Sourcing from node\_modules

Starting in Turborepo 2.4, `schema.json` is available in `node_modules` once you've run your package manager's install command:

```
{
  "$schema": "./node_modules/turbo/schema.json"
}
```

node\_modules location

## Linting for environment variables

Handling environment variables is an important part of building applications in a Turborepo.

[The `eslint-config-turbo` package](../reference/eslint-config-turbo.md) extends your ESLint setup to help you make sure you've taken care of all of your environment variables.

## Turborepo LSP

Enable even more auto-complete and linting than provided by JSON Schema, with in-editor hinting for invalid globs, references to non-existent tasks or packages, and extra repository visibility tools.

Visit the [VSCode Extension Marketplace](https://marketplace.visualstudio.com/items?itemName=Vercel.turbo-vsc) to install.

Not using Visual Studio Code?
