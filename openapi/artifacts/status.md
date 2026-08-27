---
url: https://turborepo.dev/docs/openapi/artifacts/status
title: "Get remote caching status"
description: "Check the status of Remote Caching for the authenticated user or team. Returns a JSON-encoded status indicating if Remote Caching is enabled, disabled, or disabled due to usage limits."
access_date: 2026-08-27T13:03:50.723Z
current_date: 2026-08-27T13:03:50.723Z
---

Check the status of Remote Caching for the authenticated user or team. Returns a JSON-encoded status indicating if Remote Caching is enabled, disabled, or disabled due to usage limits. Implementers should return \`enabled\` if the authenticated principal is allowed to use the cache, or \`disabled\` if not. The \`over\_limit\` and \`paused\` statuses are optional and can be used to indicate usage limits or temporary suspension.

#### cURL

```
curl -X GET "https://localhost:3000/artifacts/status"
```

#### JavaScript

#### Go

#### Python

#### Java

#### C#

#### 200

```
{
  "status": "disabled"
}
```

#### 400

```
{
  "code": "string",
  "message": "string"
}
```

#### 401

```
{
  "code": "string",
  "message": "string"
}
```

#### 403

```
{
  "code": "string",
  "message": "string"
}
```
