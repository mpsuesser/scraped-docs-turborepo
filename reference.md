---
url: https://turborepo.dev/docs/reference
title: "Turborepo API reference"
description: "Learn about Turborepo's APIs using the reference."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Turborepo's API reference is broken up into the following sections:

## Configuration

#### [Configuring turbo.json](reference/configuration.md)

Configure the behavior of \`turbo\`.

#### [Package Configurations](reference/package-configurations.md)

Create configurations specific to a package.

#### [System environment variables](reference/system-environment-variables.md)

Change \`turbo\`'s behavior using environment variables.

#### [File glob specification](reference/globs.md)

Define files for \`turbo\` to use.

## Commands

#### [run](reference/run.md)

Run tasks using \`turbo\`.

#### [watch](reference/watch.md)

Dependency-aware, single-process task watcher.

#### [prune](reference/prune.md)

Create partial monorepos for target workspaces.

#### [boundaries](reference/boundaries.md)

Enforce best practices in your monorepo.

#### [ls](reference/ls.md)

List packages in your monorepo.

#### [query](reference/query.md)

Run GraphQL queries against your monorepo.

#### [generate](reference/generate.md)

Extend your Turborepo with new apps and packages.

#### [login](reference/login.md)

Log in to your Remote Cache provider.

#### [logout](reference/logout.md)

Log out of your Remote Cache provider.

#### [link](reference/link.md)

Link your repository to Remote Cache.

#### [unlink](reference/unlink.md)

Disconnect your repository from Remote Cache.

#### [scan (Deprecated)](reference/scan.md)

Deprecated. Will be removed in a future major version.

#### [bin](reference/bin.md)

Get the path to the \`turbo\` binary.

#### [docs](reference/docs.md)

Search the Turborepo documentation.

#### [telemetry](reference/telemetry.md)

Manage telemetry for the machine.

## Packages

#### [create-turbo](reference/create-turbo.md)

Get started with Turborepo using an example.

#### [eslint-config-turbo](reference/eslint-config-turbo.md)

Find environment variables not listed in \`turbo.json\`.

#### [turbo-ignore (Deprecated)](reference/turbo-ignore.md)

Deprecated. Use \`turbo query affected\` instead.

#### [@turbo/gen](reference/turbo-gen.md)

Type definitions for Turborepo generators.

## Flag syntax

Options that require a value can be passed with an equals sign, using quotes when spaces are needed.

```bash title="Terminal"
--opt=value
--opt="value with a space"
--opt value
--opt "value with a space"
```

## Global flags

### `--color`

Forces the use of color, even in non-interactive terminals. This is useful for enabling color output in CI environments like GitHub Actions that have support for rendering color.

### `--no-color`

Suppresses color in terminal output, even in interactive terminals.

### `--no-update-notifier`

Disables the update notification. This notification will be automatically disabled when running in CI environments, but can also be disabled manually via this flag.

Alternatively, you can disable the notification using [the `TURBO_NO_UPDATE_NOTIFIER` environment variable](reference/system-environment-variables.md).

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
