---
url: https://turborepo.dev/docs/guides/tools/prisma
title: "Prisma"
description: "Integrate Prisma as a shared database package in your Turborepo monorepo."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Prisma



import { CreateTurboCallout } from "./create-turbo-callout.tsx";

[Prisma](https://www.prisma.io/) unlocks a new level of developer experience when working with databases thanks to its intuitive data model, automated migrations, type-safety & auto-completion.

[Their official guide](https://www.prisma.io/docs/guides/turborepo) describes how to integrate Prisma into a Turborepo, including:

* Prisma client initialization
* Packaging the client as an [Internal Package](/docs/core-concepts/internal-packages)
* Performing migrations
* Working on your applications locally
* Deploying

## Example

To get started with our community-supported Prisma example, run:

```bash title="Terminal"
npx create-turbo@latest -e with-prisma
```


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
