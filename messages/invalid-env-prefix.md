---
url: https://turborepo.dev/docs/messages/invalid-env-prefix
title: "Invalid environment variable prefix"
description: "How to fix the deprecated `$` prefix syntax when declaring environment variables in turbo.json."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Invalid environment variable prefix



## Why this error occurred

When declaring environment variables in your `turbo.json`, you cannot prefix them with `$`. This
was an old syntax for declaring a dependency on an environment variable that was deprecated in Turborepo 1.5.

```json title="./turbo.json"
{
  "globalEnv": ["$MY_ENV_VAR"]
}
```

The environment variable declared above has the `$` prefix.

## Solution

Remove the `$` prefix from your environment variable declaration.

```json title="./turbo.json"
{
  "globalEnv": ["MY_ENV_VAR"]
}
```

You can migrate to the `env` and `globalEnv` keys using `npx @turbo/codemod migrate-env-var-dependencies`.
Check out [the codemod's documentation for more details](/docs/reference/turbo-codemod#turborepo-1x).


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
