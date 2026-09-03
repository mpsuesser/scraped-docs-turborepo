---
url: https://turborepo.dev/docs/openapi
title: "Remote Cache API"
description: "OpenAPI specification for self-hosted Remote Cache servers."
access_date: 2026-09-03T21:18:59.663Z
current_date: 2026-09-03T21:18:59.663Z
---

# Remote Cache API



# Remote Cache API

The Turborepo Remote Cache API defines the HTTP interface that any remote cache server must implement to be compatible with Turborepo.

## Overview

The remote cache stores build artifacts identified by content-addressable hashes. When Turborepo encounters a task that matches a previously cached result, it can download the artifact instead of re-executing the task.

## Authentication

All endpoints require Bearer token authentication:

```bash
Authorization: Bearer <token>
```

For self-hosted implementations, the token format and validation logic is up to the implementer.

## Endpoints

### Artifacts

| Method | Path                                                                  | Description                                |
| ------ | --------------------------------------------------------------------- | ------------------------------------------ |
| `GET`  | [`/artifacts/status`](https://turborepo.dev/repo/docs/openapi/artifacts/status)            | Check remote caching status                |
| `HEAD` | [`/artifacts/{hash}`](https://turborepo.dev/repo/docs/openapi/artifacts/artifact-exists)   | Check if artifact exists                   |
| `GET`  | [`/artifacts/{hash}`](https://turborepo.dev/repo/docs/openapi/artifacts/download-artifact) | Download a cache artifact                  |
| `PUT`  | [`/artifacts/{hash}`](https://turborepo.dev/repo/docs/openapi/artifacts/upload-artifact)   | Upload a cache artifact                    |
| `POST` | [`/artifacts`](https://turborepo.dev/repo/docs/openapi/artifacts/artifact-query)           | Query information about multiple artifacts |

### Analytics

| Method | Path                                                              | Description               |
| ------ | ----------------------------------------------------------------- | ------------------------- |
| `POST` | [`/artifacts/events`](https://turborepo.dev/repo/docs/openapi/artifacts/record-events) | Record cache usage events |

## Download the spec

The full OpenAPI specification is available at [`/api/remote-cache-spec`](https://turborepo.dev/api/remote-cache-spec).


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
