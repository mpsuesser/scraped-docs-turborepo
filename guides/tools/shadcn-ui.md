---
url: https://turborepo.dev/docs/guides/tools/shadcn-ui
title: "shadcn/ui"
description: "Initialize and use shadcn/ui components in your Turborepo monorepo."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# shadcn/ui



[shadcn/ui](https://ui.shadcn.com/docs/monorepo) is an open-source set of beautifully designed components made with Tailwind CSS that you can copy and paste into your apps.

To get started with shadcn/ui in a new monorepo, run:

<PackageManagerTabs>
  <Tab value="pnpm">
    ```bash title="Terminal"
    pnpm dlx shadcn@canary init
    ```
  </Tab>

  <Tab value="yarn">
    ```bash title="Terminal"
    npx shadcn@canary init
    ```
  </Tab>

  <Tab value="npm">
    ```bash title="Terminal"
    npx shadcn@canary init
    ```
  </Tab>

  <Tab value="bun">
    ```bash title="Terminal"
    bunx shadcn@canary init
    ```
  </Tab>
</PackageManagerTabs>

When prompted, select the option for monorepos.

To add a component, run:

<PackageManagerTabs>
  <Tab value="pnpm">
    ```bash title="Terminal"
    pnpm dlx shadcn@canary add [COMPONENT]
    ```
  </Tab>

  <Tab value="yarn">
    ```bash title="Terminal"
    npx shadcn@canary add [COMPONENT]
    ```
  </Tab>

  <Tab value="npm">
    ```bash title="Terminal"
    npx shadcn@canary add [COMPONENT]
    ```
  </Tab>

  <Tab value="bun">
    ```bash title="Terminal"
    bunx shadcn@canary add [COMPONENT]
    ```
  </Tab>
</PackageManagerTabs>

## More information

To learn more about using shadcn/ui in Turborepo, [visit the docs for shadcn/ui](https://ui.shadcn.com/docs/monorepo).


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
