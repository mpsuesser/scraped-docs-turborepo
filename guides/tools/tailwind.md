---
url: https://turborepo.dev/docs/guides/tools/tailwind
title: "Tailwind CSS"
description: "Share Tailwind CSS configuration and styles across packages in your monorepo."
access_date: 2026-08-03T19:40:59.569Z
current_date: 2026-08-03T19:40:59.569Z
---

Learn how to use Tailwind CSS in a Turborepo.

[Tailwind CSS](https://tailwindcss.com/) is a CSS framework that allows you to rapidly build modern websites without ever leaving your HTML.

## Quickstart

If you'd rather use a template, this guide is walking through how to build [this Tailwind CSS + Turborepo template](https://github.com/vercel/turborepo/tree/main/examples/with-tailwind).

#### pnpm

```
pnpm dlx create-turbo@latest -e with-tailwind
```

#### yarn

```
yarn dlx create-turbo@latest -e with-tailwind
```

#### npm

```
npx create-turbo@latest -e with-tailwind
```

#### bun

```
bunx create-turbo@latest -e with-tailwind
```

## Guide

### Create a monorepo

If you don't have an existing project, use [create-turbo](../../getting-started/installation.md) to create a new monorepo:

#### pnpm

```
pnpm dlx create-turbo@latest
```

#### yarn

```
yarn dlx create-turbo@latest
```

#### npm

```
npx create-turbo@latest
```

#### bun

```
bunx create-turbo@latest
```

### Add Tailwind CSS to your application

[Follow Tailwind CSS's guides](https://tailwindcss.com/docs/installation/using-vite) to set up Tailwind CSS for your frontend framework.

Once completed, you can start working on bringing your UI package into the applications.

### Create a shared Tailwind CSS configuration package

First, build an [Internal Package](../../core-concepts/internal-packages.md) with four files:

#### package.json

This `package.json` installs Tailwind CSS so we can create the file shared styles and export for the rest of the repository.

```
{
  "name": "@repo/tailwind-config",
  "version": "0.0.0",
  "type": "module",
  "private": true,
  "exports": {
    ".": "./shared-styles.css",
    "./postcss": "./postcss.config.js"
  },
  "devDependencies": {
    "postcss": "^8.5.3",
    "tailwindcss": "^4.1.5"
  }
}
```

#### shared-styles.css

This `shared-styles.css` file will be shared to the libraries and applications in the repository. The variables shown will be available anywhere that the file is included.

```
@import "tailwindcss";

@theme {
  --blue-1000: #2a8af6;
  --purple-1000: #a853ba;
  --red-1000: #e92a67;
}
```

#### postcss.config.js (Optional)

If your frontends need a PostCSS configuration file, you can create one to share.

```
export const postcssConfig = {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

### Create the UI package

You can now build the components to share to your applications.

For a full example, [visit the source code for `@repo/ui` package in the Tailwind CSS example](https://github.com/vercel/turborepo/tree/main/examples/with-tailwind/packages/ui). The files required for your Tailwind CSS setup are below.

#### package.json

The `package.json` installs the dependencies for the package, sets up scripts for development and build environments, and marks the exports for the package.

```
{
  "exports": {
    "./styles.css": "./dist/index.css",
    "./*": "./dist/*.js"
  },
  "scripts": {
    "build:styles": "tailwindcss -i ./src/styles.css -o ./dist/index.css",
    "build:components": "tsc",
    "dev:styles": "tailwindcss -i ./src/styles.css -o ./dist/index.css --watch",
    "dev:components": "tsc --watch"
  },
  "devDependencies": {
    "@repo/tailwind-config": "workspace:*",
    "@tailwindcss/cli": "^4.1.5",
    "@tailwindcss/postcss": "^4.1.5",
    "autoprefixer": "^10.4.20",
    "tailwindcss": "^4.1.5"
  }
}
```

#### turbo.json

Create a `build` and `dev` task that runs the scripts for building of components and style sheets in parallel.

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "dependsOn": ["build:styles", "build:components"]
    },
    "build:styles": {
      "outputs": ["dist/**"]
    },
    "build:components": {
      "outputs": ["dist/**"]
    },
    "dev": {
      "with": ["dev:styles", "dev:components"]
    },
    "dev:styles": {
      "cache": false,
      "persistent": true
    },
    "dev:components": {
      "cache": false,
      "persistent": true
    }
  }
}
```

#### styles.css

This `styles.css` contains component-level styles for the shared UI library.

```
/* Component-level styles for the UI package */
@import "tailwindcss" prefix(ui);
```

### Use the UI package in an application

Install the packages you've created into your application.

#### pnpm

```
pnpm add @repo/ui @repo/tailwind-config --save-dev --filter=@repo/ui --filter=web
```

#### yarn

```
yarn workspace web add @repo/ui @repo/tailwind-config --dev
yarn workspace @repo/ui add @repo/ui @repo/tailwind-config --dev
```

#### npm

```
npm install @repo/ui @repo/tailwind-config --workspace=web --workspace=@repo/ui --save-dev
```

#### bun

```
cd apps/web && bun install @repo/ui @repo/tailwind-config --dev
cd packages/ui && bun install @repo/ui @repo/tailwind-config --dev
```

Then, configure the files in your application so the styles from the UI package are reflected in the application.

#### globals.css

```
@import "tailwindcss";
@import "@repo/tailwind-config";
```

#### layout.tsx

```
import "@repo/ui/styles.css";
import "./globals.css";
```

#### postcss.config.js (Optional)

```
import { postcssConfig } from "@repo/tailwind-config/postcss";

export default postcssConfig;
```
