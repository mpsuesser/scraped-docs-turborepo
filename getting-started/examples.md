---
url: https://turborepo.dev/docs/getting-started/examples
title: "Start with an example"
description: "Browse core-maintained and community examples to bootstrap a Turborepo with your favorite tools."
access_date: 2026-08-03T19:40:59.569Z
current_date: 2026-08-03T19:40:59.569Z
---

Use `create-turbo` to bootstrap an example with your favorite tooling.

#### pnpm

```
# Use an example listed below
pnpm dlx create-turbo@latest --example [example-name]

# Use a GitHub repository from the community
pnpm dlx create-turbo@latest --example [github-url]
```

#### yarn

```
# Use an example listed below
yarn dlx create-turbo@latest --example [example-name]

# Use a GitHub repository from the community
yarn dlx create-turbo@latest --example [github-url]
```

#### npm

```
# Use an example listed below
npx create-turbo@latest --example [example-name]

# Use a GitHub repository from the community
npx create-turbo@latest --example [github-url]
```

#### bun

```
# Use an example listed below
bunx create-turbo@latest --example [example-name]

# Use a GitHub repository from the community
bunx create-turbo@latest --example [github-url]
```

## Core-maintained examples

The following examples are maintained by the Turborepo core team. Dependencies are kept as up-to-date as possible and GitHub Issues are accepted and addressed for these examples.

| Name | Description |
| --- | --- |
| [Basic](https://github.com/vercel/turborepo/tree/main/examples/basic) | Basic monorepo example with two Next.js applications |
| [Kitchen sink](https://github.com/vercel/turborepo/tree/main/examples/kitchen-sink) | Multiple frameworks, both frontend and backend |
| [Non-monorepo](https://github.com/vercel/turborepo/tree/main/examples/non-monorepo) | A standalone application using Turborepo |
| [OpenTelemetry](https://github.com/vercel/turborepo/tree/main/examples/with-otel) | Monorepo with a local OpenTelemetry Collector, Prometheus, and Grafana for metrics visualization |
| [Shell commands](https://github.com/vercel/turborepo/tree/main/examples/with-shell-commands) | A nearly empty Turborepo - useful for creating reproductions for GitHub Issues |
| [SvelteKit](https://github.com/vercel/turborepo/tree/main/examples/with-svelte) | Monorepo with multiple SvelteKit apps sharing a UI Library |
| [TailwindCSS](https://github.com/vercel/turborepo/tree/main/examples/with-tailwind) | Monorepo with multiple Next.js apps sharing a UI library built with TailwindCSS |

## Community-maintained examples

The community curates a set of examples to showcase ways to use common tools and libraries with Turborepo. To bootstrap your monorepo with one of the examples, use the `--example` flag:

| Name | Description |
| --- | --- |
| [Design System](https://github.com/vercel/turborepo/tree/main/examples/design-system) | Unify your site's look and feel by sharing a design system across multiple apps |
| [Angular](https://github.com/vercel/turborepo/tree/main/examples/with-angular) | Minimal Turborepo example for learning the fundamentals |
| [Yarn Berry](https://github.com/vercel/turborepo/tree/main/examples/with-berry) | Monorepo example using Yarn Berry (Yarn 3) |
| [Biome](https://github.com/vercel/turborepo/tree/main/examples/with-biome) | Basic monorepo example with two Next.js applications and integrated Biome configuration |
| [Changesets](https://github.com/vercel/turborepo/tree/main/examples/with-changesets) | Configured to publish packages via Changesets |
| [Docker](https://github.com/vercel/turborepo/tree/main/examples/with-docker) | Monorepo with an Express API and a Next.js App deployed with Docker utilizing turbo prune |
| [Gatsby](https://github.com/vercel/turborepo/tree/main/examples/with-gatsby) | Monorepo with a Gatsby.js and a Next.js app both sharing a UI Library |
| [With MCP Servers](https://github.com/vercel/turborepo/tree/main/examples/with-mcp-servers) | Turborepo monorepo with multiple MCP tool servers as isolated workspace packages |
| [Nest.js](https://github.com/vercel/turborepo/tree/main/examples/with-nestjs) | Monorepo with Nest.js |
| [Next.js + Elysia](https://github.com/vercel/turborepo/tree/main/examples/with-nextjs-elysia) | Monorepo with Next.js frontend and Elysia backend |
| [npm workspaces](https://github.com/vercel/turborepo/tree/main/examples/with-npm) | Monorepo example using NPM workspaces |
| [Prisma](https://github.com/vercel/turborepo/tree/main/examples/with-prisma) | Monorepo with a Next.js App fully configured with Prisma |
| [React Native](https://github.com/vercel/turborepo/tree/main/examples/with-react-native-web) | Simple React Native & Next.js monorepo with a shared UI library |
| [Rollup](https://github.com/vercel/turborepo/tree/main/examples/with-rollup) | Monorepo with a single Next.js app sharing a UI library bundled with Rollup |
| [Rsbuild + React](https://github.com/vercel/turborepo/tree/main/examples/with-rsbuild) | Monorepo example with Rsbuild and React |
| [Rsbuild Module Federation](https://github.com/vercel/turborepo/tree/main/examples/with-rsbuild-module-federation) | Monorepo example with Rsbuild, React, and Module Federation |
| [Solid.js](https://github.com/vercel/turborepo/tree/main/examples/with-solid) | Monorepo example with SolidJS applications |
| [typeorm](https://github.com/vercel/turborepo/tree/main/examples/with-typeorm) | Monorepo with a Next.js App fully configured with typeorm |
| [Ultracite](https://github.com/vercel/turborepo/tree/main/examples/with-ultracite) | Basic monorepo example with two Next.js applications and integrated Ultracite configuration |
| [Vite](https://github.com/vercel/turborepo/tree/main/examples/with-vite) | Monorepo with multiple Vanilla JS apps bundled with Vite, sharing a UI Library |
| [Vite + React](https://github.com/vercel/turborepo/tree/main/examples/with-vite-react) | Monorepo example with Vite and React |
| [Vitest](https://github.com/vercel/turborepo/tree/main/examples/with-vitest) | Monorepo example with Vitest for testing |
| [Vue/Nuxt](https://github.com/vercel/turborepo/tree/main/examples/with-vue-nuxt) | Monorepo with Vue and Nuxt, sharing a UI Library |
| [Yarn](https://github.com/vercel/turborepo/tree/main/examples/with-yarn) | Monorepo example using Yarn workspaces |
