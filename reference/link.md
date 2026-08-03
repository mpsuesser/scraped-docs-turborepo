---
url: https://turborepo.dev/docs/reference/link
title: "link"
description: "API reference for the `turbo link` command"
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

# link



Link the repository to a Remote Cache provider.

```bash title="Terminal"
turbo link
```

The selected owner (either a user or an organization) will be able to share [cache artifacts](../core-concepts/remote-caching.md) through [Remote Caching](../core-concepts/remote-caching.md).

## Flag options

### `--api <url>`

Specifies the URL of your Remote Cache provider.

```bash title="Terminal"
turbo link --api=https://example.com
```

### `--yes`

Answer yes to all prompts

```bash title="Terminal"
turbo link --yes
```

### `--scope <scope>`

The scope to which you are linking. For example, when using Vercel, this is your Vercel team's slug.

```bash title="Terminal"
turbo link --scope=your-team
```


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
