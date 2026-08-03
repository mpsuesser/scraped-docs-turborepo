---
url: https://turborepo.dev/docs/getting-started/examples
title: "Start with an example"
description: "Browse core-maintained and community examples to bootstrap a Turborepo with your favorite tools."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Start with an example



Use `create-turbo` to bootstrap an example with your favorite tooling.

<PackageManagerTabs>
  <Tab value="pnpm">
    ```bash title="Terminal"
    # Use an example listed below
    pnpm dlx create-turbo@latest --example [example-name]

    # Use a GitHub repository from the community
    pnpm dlx create-turbo@latest --example [github-url]
    ```
  </Tab>

  <Tab value="yarn">
    ```bash title="Terminal"
    # Use an example listed below
    yarn dlx create-turbo@latest --example [example-name]

    # Use a GitHub repository from the community
    yarn dlx create-turbo@latest --example [github-url]
    ```
  </Tab>

  <Tab value="npm">
    ```bash title="Terminal"
    # Use an example listed below
    npx create-turbo@latest --example [example-name]

    # Use a GitHub repository from the community
    npx create-turbo@latest --example [github-url]
    ```
  </Tab>

  <Tab value="bun">
    ```bash title="Terminal"
    # Use an example listed below
    bunx create-turbo@latest --example [example-name]

    # Use a GitHub repository from the community
    bunx create-turbo@latest --example [github-url]
    ```
  </Tab>
</PackageManagerTabs>

## Core-maintained examples

The following examples are maintained by the Turborepo core team. Dependencies are kept as up-to-date as possible and GitHub Issues are accepted and addressed for these examples.

<ExamplesTable coreMaintained />

## Community-maintained examples

The community curates a set of examples to showcase ways to use common tools and libraries with Turborepo. To bootstrap your monorepo with one of the examples, use the `--example` flag:

<Callout type="info">
  GitHub Issues for these examples will be closed. If you find problems, please
  submit a pull request with fixes.
</Callout>

<ExamplesTable />


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
