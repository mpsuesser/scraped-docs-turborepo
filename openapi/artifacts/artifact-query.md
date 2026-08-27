---
url: https://turborepo.dev/docs/openapi/artifacts/artifact-query
title: "Query artifact information"
description: "Query information about multiple artifacts by their hashes. Returns metadata about each artifact including size, task duration, and tag."
access_date: 2026-08-27T13:03:50.723Z
current_date: 2026-08-27T13:03:50.723Z
---

Query information about multiple artifacts by their hashes. Returns metadata about each artifact including size, task duration, and tag. This endpoint is optional for basic cache functionality but enables optimized cache fetching by allowing the client to query multiple artifacts in a single request.

#### cURL

```
curl -X POST "https://localhost:3000/artifacts" \
  -H "Content-Type: application/json" \
  -d '{
    "hashes": [
      "string"
    ]
  }'
```

#### JavaScript

#### Go

#### Python

#### Java

#### C#

#### 200

```
{
  "property1": {
    "size": 0,
    "taskDurationMs": 0,
    "tag": "string"
  },
  "property2": {
    "size": 0,
    "taskDurationMs": 0,
    "tag": "string"
  }
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
