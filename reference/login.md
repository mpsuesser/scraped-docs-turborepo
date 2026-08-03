---
url: https://turborepo.dev/docs/reference/login
title: "login"
description: "API reference for the `turbo login` command"
access_date: 2026-08-03T19:10:40.966Z
current_date: 2026-08-03T19:10:40.966Z
---

# login



Log in to your Remote Cache provider.

```bash title="Terminal"
turbo login
```

The default provider is [Vercel](https://vercel.com/). To specify a different provider, use the `--api` option.

## Flag options

### --api \<url>

Set the API URL of the Remote Cache provider.

```bash title="Terminal"
turbo login --api=https://acme.com/api
```

### --login \<url>

Set the URL for login requests that should dynamically generate tokens.

```bash title="Terminal"
turbo login --login=https://acme.com
```

### --sso-team \<team>

Connect to an SSO-enabled team by providing your team slug.

```bash title="Terminal"
turbo login --sso-team=slug-for-team
```

### --manual

Manually enter token instead of requesting one from a login service.


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
