---
url: https://turborepo.dev/docs/reference
title: "Turborepo API reference"
description: "Entry point for all Turborepo API reference documentation including configuration, CLI commands, and packages."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Learn about Turborepo's APIs using the reference.

Turborepo's API reference is broken up into the following sections:

## Configuration

## Commands

## Packages

## Flag syntax

Options that require a value can be passed with an equals sign, using quotes when spaces are needed.

```
--opt=value
--opt="value with a space"
--opt value
--opt "value with a space"
```

## Global flags

### \--color

Forces the use of color, even in non-interactive terminals. This is useful for enabling color output in CI environments like GitHub Actions that have support for rendering color.

### \--no-color

Suppresses color in terminal output, even in interactive terminals.

### \--no-update-notifier

Disables the update notification. This notification will be automatically disabled when running in CI environments, but can also be disabled manually via this flag.

Alternatively, you can disable the notification using [the `TURBO_NO_UPDATE_NOTIFIER` environment variable](reference/system-environment-variables.md).

[PreviousUpgrading](crafting-your-repository/upgrading.md) [NextConfiguring turbo.json](reference/configuration.md)
