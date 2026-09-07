---
url: https://turborepo.dev/docs/guides/multi-language
title: "Multi-language support"
description: "Run JavaScript packages and experimental native Go modules in one task graph."
access_date: 2026-09-07T07:30:22.241Z
current_date: 2026-09-07T07:30:22.241Z
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

The library and API use their Go module paths as stable Turborepo package identities:

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

Turborepo now discovers `web` from the package-manager workspace and `example.com/acme/api` and `example.com/acme/lib` from `go.work`. The Go modules do not need `package.json` files. A repository that contains only enabled native workspaces can also omit the root `package.json`; this mixed repository keeps one for its JavaScript workspace.

Run JavaScript scripts and built-in native Go tasks together, or filter by a Go module path:

```
turbo run build
turbo run test --filter=example.com/acme/api
turbo run lint --filter=go-workspace
```

The API's `require` declaration creates the dependency on `example.com/acme/lib`, so `dependsOn: ["^build"]` orders their Go builds. The synthetic `go-workspace` package provides workspace-wide `test`, `lint`, and `format` tasks. See the [Go guide](tools/go.md) for the complete native task and caching behavior.

For a language without native workspace support, add a `package.json` beside each independently cacheable project and invoke its toolchain from package scripts. Turborepo then uses the package-manager relationships rather than interpreting that language's dependency graph.
