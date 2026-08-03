---
url: https://turborepo.dev/docs/guides/multi-language
title: "Multi-language support"
description: "Add unsupported languages such as Go to Turborepo by wrapping projects in package-manager workspaces."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

Integrate unsupported languages with package scripts and workspace boundaries.

Turborepo uses package-manager workspaces and `package.json` scripts to discover most packages and tasks. A script can invoke any toolchain, so you can integrate a language without native Turborepo support by giving each independently cacheable project a package boundary.

This guide uses Go as an example. Turborepo sees the package scripts, files, outputs, and package-manager dependency relationships. The Go toolchain remains responsible for resolving Go modules and compiling code; Turborepo does not infer the Go package graph.

## Add the project to the workspace

Suppose a Go service lives in `services/api`. Include that directory in your package-manager workspace:

#### pnpm

```
packages:
  - "apps/*"
  - "packages/*"
  - "services/*"
```

[→ pnpm workspace documentation](https://pnpm.io/pnpm-workspace_yaml)

#### yarn

```
{
  "workspaces": ["apps/*", "packages/*", "services/*"]
}
```

[→ Yarn workspace documentation](https://yarnpkg.com/features/workspaces#how-are-workspaces-declared)

#### npm

```
{
  "workspaces": ["apps/*", "packages/*", "services/*"]
}
```

[→ npm workspace documentation](https://docs.npmjs.com/cli/using-npm/workspaces)

#### bun

```
{
  "workspaces": ["apps/*", "packages/*", "services/*"]
}
```

[→ Bun workspace documentation](https://bun.sh/docs/install/workspaces)

## Create a package boundary

Add a `package.json` beside the Go module. Keep the actual toolchain commands in this package rather than in the repository root:

```
{
  "name": "@repo/go-api",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "build": "go build -o dist/api ./cmd/api",
    "test": "go test ./...",
    "lint": "go vet ./..."
  }
}
```

A minimal service can use a regular Go module:

```
module example.com/acme/api

go 1.24
```

```
package main

import "fmt"

func main() {
    fmt.Println("API ready")
}
```

Turborepo does not interpret `go.mod` or Go imports. It hashes files inside `services/api` as package inputs, then runs the declared scripts and lets Go resolve its own module graph.

## Declare outputs

Register the tasks in the root `turbo.json`:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    },
    "test": {},
    "lint": {}
  }
}
```

The Go build has a package-specific output, so declare it close to the package instead of applying `dist/**` to every workspace:

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

For `@repo/go-api`, `dist/**` caches `services/api/dist/api`. Go's external build cache is separate and remains Go's responsibility.

Run the service tasks like any other package task:

```
turbo run build --filter=@repo/go-api
turbo run test --filter=@repo/go-api
turbo run lint --filter=@repo/go-api
```

## Create orchestration dependencies

When another workspace package needs the compiled service artifact before its own build, declare a package-manager dependency. This dependency-free script keeps the example runnable without omitted framework dependencies:

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

Add `@repo/go-api` using the local-workspace syntax for your package manager:

#### pnpm

```
{
  "devDependencies": {
    "@repo/go-api": "workspace:*"
  }
}
```

#### yarn

```
{
  "devDependencies": {
    "@repo/go-api": "1.0.0"
  }
}
```

#### npm

```
{
  "devDependencies": {
    "@repo/go-api": "1.0.0"
  }
}
```

#### bun

```
{
  "devDependencies": {
    "@repo/go-api": "workspace:*"
  }
}
```

The Yarn and npm ranges match `@repo/go-api` 's local `1.0.0` version, so both Yarn Classic and modern Yarn, as well as npm, link the workspace package. pnpm and Bun use the workspace protocol.

With `dependsOn: ["^build"]`, `turbo run build --filter=web` builds `@repo/go-api` first. This dependency is orchestration metadata for Turborepo and the package manager; it does not make the Go module importable from JavaScript or teach Turborepo about Go package dependencies.

Use one package boundary per Go project that should be independently filtered, invalidated, or cached. If several Go modules depend on one another, mirror only the ordering relationships Turborepo needs in their `package.json` files while continuing to describe the real source dependency graph with Go modules or a Go workspace.
