---
url: https://turborepo.dev/docs/openapi/artifacts/record-events
title: "Record cache usage events"
description: "Records cache usage analytics events. The body of this request is an array of cache usage events.\n\nThis endpoint is optional but enables cache hit/miss analytics. Implementers can use this data to track cache effectiveness and optimize storage.\n\nEvent types:\n- `HIT`: A cached artifact was found and used\n- `MISS`: No cached artifact was found for the given hash\n\nSource types:\n- `LOCAL`: The cache event was on the user's local filesystem cache\n- `REMOTE`: The cache event is for the remote cache"
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

#### `POST /artifacts/events`

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
