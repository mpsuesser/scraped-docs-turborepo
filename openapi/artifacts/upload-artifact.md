---
url: https://turborepo.dev/docs/openapi/artifacts/upload-artifact
title: "Upload a cache artifact"
description: "Uploads a cache artifact identified by the `hash` specified on the path. The cache artifact can then be downloaded with the provided `hash`."
access_date: 2026-08-27T13:03:50.723Z
current_date: 2026-08-27T13:03:50.723Z
---

Uploads a cache artifact identified by the \`hash\` specified on the path. The cache artifact can then be downloaded with the provided \`hash\`. The request body should contain a gzip-compressed tarball of the task outputs. The server should store this data and make it available for subsequent downloads.

## Authorization

`bearerToken `

AuthorizationBearer <token>

## Path Parameters

hash\*string

## Query Parameters

teamId?string

slug?string

## Header Parameters

Content-Length\*integer

x-artifact-duration?integer

x-artifact-tag?string

x-artifact-sha?string

x-artifact-dirty-hash?string

x-artifact-client-ci?string

x-artifact-client-interactive?integer

## Request Body

`application/octet-stream`

The artifact data as a gzip-compressed tarball

body\*unknown

## Response Body

#### cURL

```
curl -X PUT "https://localhost:3000/artifacts/string" \
  -H "Content-Length: 0"
```

#### JavaScript

#### Go

#### Python

#### Java

#### C#

#### 200

```
{
  "urls": [
    "http://example.com"
  ]
}
```

#### 202

```
{
  "urls": [
    "http://example.com"
  ]
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
