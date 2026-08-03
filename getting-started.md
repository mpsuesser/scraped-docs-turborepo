---
url: https://turborepo.dev/docs/getting-started
title: "Getting started"
description: "Choose a learning path to start using Turborepo in a new or existing repository."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Getting started



If you're new to Turborepo, you can follow these steps to get started.

<Steps>
  <Step>
    ## Install Turborepo

    Install `turbo` globally so you can conveniently run `turbo` commands in your terminal from anywhere in your repository.

    <Tabs items={["npm", "yarn", "pnpm"]} storageKey="selected-pkg-manager">
      <Tab value="npm">
        ```bash title="Terminal"
        npm install turbo --global
        ```
      </Tab>

      <Tab value="yarn">
        ```bash title="Terminal"
        yarn global add turbo
        ```
      </Tab>

      <Tab value="pnpm">
        ```bash title="Terminal"
        pnpm add turbo --global
        ```
      </Tab>
    </Tabs>

    To learn more about installing `turbo`, see the [installation guide](/docs/getting-started/installation).
  </Step>

  <Step>
    ## Choose your learning path

    <Cards>
      <Card href="/docs/getting-started/installation" title="Use create-turbo" description="Start with a template" />

      <Card href="/docs/getting-started/examples" title="Use an example" description="Start with a framework-specific example" />

      <Card href="/docs/crafting-your-repository" title="Follow the in-depth guides" description="From zero to monorepo" />

      <Card href="/docs/getting-started/add-to-existing-repository" title="Add to an existing repository" description="Make your current repo fast" />
    </Cards>
  </Step>
</Steps>


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
