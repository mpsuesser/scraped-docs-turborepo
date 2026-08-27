---
url: https://turborepo.dev/docs/openapi/artifacts/download-artifact
title: "Download a cache artifact"
description: "Downloads a cache artifact identified by its `hash` specified on the request path. The artifact is downloaded as an octet-stream (binary data)."
access_date: 2026-08-27T13:03:50.723Z
current_date: 2026-08-27T13:03:50.723Z
---

Downloads a cache artifact identified by its \`hash\` specified on the request path. The artifact is downloaded as an octet-stream (binary data). The artifact data is a gzip-compressed tarball containing the cached task outputs. The client will verify the content-length header against the response body size.

#### cURL

```
curl -X GET "https://localhost:3000/artifacts/string"
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

#### 404

```
{
  "code": "string",
  "message": "string"
}
```
