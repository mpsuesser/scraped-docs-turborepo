---
url: https://turborepo.dev/docs/openapi/artifacts/artifact-exists
title: "Check if artifact exists"
description: "Check that a cache artifact with the given `hash` exists. This request returns response headers only and is equivalent to a `GET` request to this endpoint where the response contains no body."
access_date: 2026-09-03T21:18:59.663Z
current_date: 2026-09-03T21:18:59.663Z
---

Check that a cache artifact with the given \`hash\` exists. This request returns response headers only and is equivalent to a \`GET\` request to this endpoint where the response contains no body. Implementers should return 200 if the artifact exists, or 404 if it does not.

#### cURL

```
curl -X HEAD "https://localhost:3000/artifacts/string"
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
