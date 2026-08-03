---
url: https://turborepo.dev/docs/reference/generate
title: "generate"
description: "All flags and options for the `turbo generate` command used to scaffold new packages and run custom generators."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

Extend your Turborepo with new apps and packages.

```
turbo generate
```

- [`turbo generate run [generator-name]`](#run-generator-name): Run custom generators defined in your repository.
- [`turbo generate workspace [options]`](#workspace): Create a new package in your repository by copying an existing one or from the start.

For more information and practical use cases for writing custom generators, visit [the "Generating code" guide](../guides/generating-code.md).

## run \[generator-name\]

Run custom generators defined in your repository.

```
turbo gen run [generator-name]
```

### Flag options

#### \--args

Answers to pass directly to the generator's prompts.

#### \--config <path>

Generator configuration file.

Default: `turbo/generators/config.js`

#### \--root <path>

The root of your repository

Default: directory with root `turbo.json`

## workspace

Create a new workspace.

```
turbo gen workspace [options]
```

### Flag options

#### \--name <name>

The name for the new workspace to be used in the `package.json` `name` key. The `name` key is the unique identifier for the package in your repository.

#### \--empty

Creates an empty workspace. Defaults to `true`.

#### \--copy <name>/<url>

Name of local workspace within your monorepo or a fully qualified GitHub URL with any branch and/or subdirectory.

#### \--destination <path>

Where the new workspace should be created.

#### \--type <app/package>

The type of workspace to create (`app` or `package`).

#### \--root <path>

The root of your repository. Defaults to the directory of the root `turbo.json`.

#### \--show-all-dependencies

Prevent filtering dependencies by workspace type when selecting dependencies to add.

#### \--example-path <path>, -p <path>

In a rare case, your GitHub URL might contain a branch name with a slash (e.g. `bug/fix-1`) and the path to the example (e.g. `foo/bar`). In this case, you must specify the path to the example separately.
