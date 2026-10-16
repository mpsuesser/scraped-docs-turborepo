---
url: https://turborepo.dev/docs/reference/create-turbo
title: "create-turbo"
description: "Quickly set up a new Turborepo repository from scratch."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

The easiest way to get started with Turborepo is by using `create-turbo`. Use this CLI tool to quickly start building a new monorepo, with everything set up for you.

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest
```

#### npm

```bash title="Terminal"
npx create-turbo@latest
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest
```

## Start with an example

The community curates a set of examples to showcase ways to use common tools and libraries with Turborepo. To bootstrap your monorepo with one of the examples, use the `--example` flag:

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest --example [example-name]
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest --example [example-name]
```

#### npm

```bash title="Terminal"
npx create-turbo@latest --example [example-name]
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest --example [example-name]
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest --example [example-name]
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest --example [example-name]
```

Use any of the example's names below:

## Core-maintained examples

The following examples are maintained by the Turborepo core team. Dependencies are kept as up-to-date as possible and GitHub Issues are accepted and addressed for these examples.

## Community-maintained examples

The community curates a set of examples to showcase ways to use common tools and libraries with Turborepo. To bootstrap your monorepo with one of the examples, use the `--example` flag:

### Use a community example

You can also use a custom starter or example by using a GitHub URL. This is useful for using your own custom starters or examples from the community.

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest --example [github-url]
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest --example [github-url]
```

#### npm

```bash title="Terminal"
npx create-turbo@latest --example [github-url]
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest --example [github-url]
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest --example [github-url]
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest --example [github-url]
```

## Options

```txt title="Terminal"
-m, --package-manager to use (choices: "npm", "yarn", "pnpm", "bun", "nub", "aube")

--skip-install: Do not run a package manager install after creating the project (Default: false)

--skip-transforms: Do not run any code transformation after creating the project (Default: false)

--turbo-version <version>: Use a specific version of turbo (default: latest)

-e, --example [name]|[github-url]: An example to bootstrap the app with. You can use an example name from the official Turborepo repo or a GitHub URL. The URL can use any branch and/or subdirectory

-p, --example-path <path-to-example>: In a rare case, your GitHub URL might contain a branch name with a slash (e.g. bug/fix-1) and the path to the example (e.g. foo/bar). In this case, you must specify the path to the example separately: --example-path foo/bar

-v, --version:  Output the current version

-h, --help: Display help for command
```

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
