---
url: https://turborepo.dev/docs/reference/docs
title: "docs"
description: "API reference for the `turbo docs` command"
access_date: 2026-08-03T19:10:40.966Z
current_date: 2026-08-03T19:10:40.966Z
---

# docs



Search the Turborepo documentation directly from the command line.

```bash title="Terminal"
turbo docs <query>
```

## Usage

The `docs` command searches the Turborepo documentation and returns matching pages.

```bash title="Terminal"
turbo docs "caching"
```

Example output:

```txt title="Terminal"
Found 5 results for 'caching':

1. Caching: https://v2-7-5.turborepo.dev/docs/core-concepts/caching

2. Remote Caching: https://v2-7-5.turborepo.dev/docs/core-concepts/remote-caching

3. Caching Tasks: https://v2-7-5.turborepo.dev/docs/crafting-your-repository/caching

4. Local Caching: https://v2-7-5.turborepo.dev/docs/core-concepts/local-caching

5. Cache Troubleshooting: https://v2-7-5.turborepo.dev/docs/troubleshooting/cache-issues
```

Results link to the versioned documentation site that matches your installed version of `turbo`.

## Options

### `--docs-version`

Override the documentation version to search. By default, `turbo docs` uses the version of `turbo` you have installed.

```bash title="Terminal"
turbo docs "task dependencies" --docs-version 2.8.0
```

## Minimum version

The minimum supported version is **2.7.5**.


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
