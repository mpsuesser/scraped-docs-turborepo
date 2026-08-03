---
url: https://turborepo.dev/docs/reference/link
title: "link"
description: "All flags and options for the `turbo link` command that connects your repository to Remote Cache."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# link



Link the repository to a Remote Cache provider.

```bash title="Terminal"
turbo link
```

The selected owner (either a user or an organization) will be able to share [cache artifacts](/docs/core-concepts/remote-caching) through [Remote Caching](/docs/core-concepts/remote-caching).

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

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
