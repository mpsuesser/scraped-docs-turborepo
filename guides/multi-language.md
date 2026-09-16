---
url: https://turborepo.dev/docs/guides/multi-language
title: "Multi-language support"
description: "Run JavaScript packages and experimental native Go modules in one task graph."
access_date: 2026-09-16T16:46:06.902Z
current_date: 2026-09-16T16:46:06.902Z
---

Turborepo can combine package-manager workspaces with enabled native workspaces. Each workspace contributes its own package identities, dependency relationships, and tasks to the same Package Graph and Task Graph.

This example combines a JavaScript application with two Go modules:

```
.
├── apps/api/
│   ├── go.mod
│   └── main.go
├── packages/
│   ├── lib/
│   │   ├── go.mod
│   │   └── lib.go
│   └── web/
│       └── package.json
├── go.work
├── package.json
└── turbo.json
```

## Add the JavaScript workspace

Include `packages/web` in the package-manager workspace:

#### pnpm

```
packages:
  - "packages/*"
```

[→ pnpm workspace documentation](https://pnpm.io/pnpm-workspace_yaml)

#### yarn

```
{
  "workspaces": ["packages/*"]
}
```

[→ Yarn workspace documentation](https://yarnpkg.com/features/workspaces#how-are-workspaces-declared)

#### npm

```
{
  "workspaces": ["packages/*"]
}
```

[→ npm workspace documentation](https://docs.npmjs.com/cli/using-npm/workspaces)

#### bun

```
{
  "workspaces": ["packages/*"]
}
```

[→ Bun workspace documentation](https://bun.sh/docs/install/workspaces)

The JavaScript application has a normal package name and script:

```
{
  "name": "web",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "build": "node -e \"console.log('web build')\""
  }
}
```

## Add the Go workspace

At the repository root, create a `go.work` that lists each Go module:

```
go 1.22

use (
  ./apps/api
  ./packages/lib
)
```

Declare the full Go module paths in `go.mod`. Turborepo derives the package names `lib` and `api` from these paths:

```
module example.com/acme/lib

go 1.22
```

```
module example.com/acme/api

go 1.22

require example.com/acme/lib v0.0.0
```

For example, the API can import and call the library:

```
package lib

func Message() string {
    return "API ready"
}
```

```
package main

import (
    "fmt"

    "example.com/acme/lib"
)

func main() {
    fmt.Println(lib.Message())
}
```

## Enable native Go tasks

Enable native Go workspace support in the root `turbo.json`:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "futureFlags": {
    "experimentalGoWorkspaces": true
  },
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    },
    "test": {},
    "lint": {}
  }
}
```

Turborepo now discovers `web` from the package-manager workspace and `api` and `lib` from `go.work`. The Go modules do not need `package.json` files. A repository that contains only enabled native workspaces can also omit the root `package.json`; this mixed repository keeps one for its JavaScript workspace.

Go package names use the last module-path component and preserve a trailing major-version suffix: `example.com/api` becomes `api`, while `example.com/api/v2` becomes `api/v2`. Names must be unique across the entire Package Graph: another Go module ending in `/api`, or a JavaScript package named `api`, would collide with this API. Full paths are not aliases, and package-name overrides are not supported. See [Go package names](tools/go.md#package-names) for collision handling.

Run JavaScript scripts and built-in native Go tasks together, or select a Go package by its derived name:

```
turbo run build
turbo run test --filter=api
turbo run api#test
turbo run lint
```

The API's `require example.com/acme/lib` declaration resolves the dependency on the package named `lib`, so `dependsOn: ["^build"]` orders their Go builds. Keep the full module paths in `go.mod` and Go imports; those paths remain dependency-resolution metadata rather than Turborepo graph identities. The synthetic `go-workspace` package depends on every Go member but does not provide built-in `test`, `lint`, or `format` tasks. Run those tasks without a filter to execute them in each member module. See the [Go guide](tools/go.md) for the complete native task and caching behavior.

For a language without native workspace support, add a `package.json` beside each independently cacheable project and invoke its toolchain from package scripts. Turborepo then uses the package-manager relationships rather than interpreting that language's dependency graph.
