---
url: https://turborepo.dev/docs/openapi/artifacts/record-events
title: "Record cache usage events"
description: "Records cache usage analytics events. The body of this request is an array of cache usage events."
access_date: 2026-08-27T13:03:50.723Z
current_date: 2026-08-27T13:03:50.723Z
---

Records cache usage analytics events. The body of this request is an array of cache usage events. This endpoint is optional but enables cache hit/miss analytics. Implementers can use this data to track cache effectiveness and optimize storage. Event types: - \`HIT\`: A cached artifact was found and used - \`MISS\`: No cached artifact was found for the given hash Source types: - \`LOCAL\`: The cache event was on the user's local filesystem cache - \`REMOTE\`: The cache event is for the remote cache

#### cURL

```
curl -X POST "https://localhost:3000/artifacts/events" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sessionId": "f6567dd8-e069-418e-8893-7d22fcf12459",
      "source": "LOCAL",
      "event": "HIT",
      "hash": "string"
    }
  ]'
```

#### JavaScript

#### Go

#### Python

#### Java

#### C#

#### 200

Empty

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
