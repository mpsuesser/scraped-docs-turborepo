---
url: https://turborepo.dev/docs/reference/eslint-config-turbo
title: "eslint-config-turbo"
description: "Learn more about eslint-config-turbo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[The `eslint-config-turbo` package](https://www.npmjs.com/package/eslint-config-turbo) helps you find environment variables that are used in your code that are not a part of Turborepo's hashing. Environment variables used in your source code that are not accounted for in `turbo.json` will be highlighted in your editor and errors will show as ESLint output.

## Installation

Install `eslint-config-turbo` into the location where your ESLint configuration is held:

#### pnpm

```bash title="Terminal"
pnpm add eslint-config-turbo --filter=@repo/eslint-config
```

#### yarn

```bash title="Terminal"
yarn workspace @acme/eslint-config add eslint-config-turbo --dev
```

#### npm

```bash title="Terminal"
npm install --save-dev eslint-config-turbo -w @acme/eslint-config
```

#### bun

```bash title="Terminal"
cd packages/eslint-config && bun install eslint-config-turbo --dev
```

#### nub

```bash title="Terminal"
cd packages/eslint-config && nub add eslint-config-turbo --save-dev
```

#### aube

```bash title="Terminal"
cd packages/eslint-config && aube add eslint-config-turbo --save-dev
```

## Usage (Flat Config `eslint.config.js`)

```js title="./packages/eslint-config/base.js"
import turboConfig from "eslint-config-turbo/flat";

export default [
  ...turboConfig,
  // Other configuration
];
```

You can also configure rules available in the configuration:

```js title="./packages/eslint-config/base.js"
import turboConfig from "eslint-config-turbo/flat";

export default [
  ...turboConfig,
  // Other configuration
  {
    rules: {
      "turbo/no-undeclared-env-vars": [
        "error",
        {
          allowList: ["^ENV_[A-Z]+$"],
        },
      ],
    },
  },
];
```

## Usage (Legacy `eslintrc*`)

Add `turbo` to the extends section of your eslint configuration file. You can omit the `eslint-config-` prefix:

```json title="./packages/eslint-config/base.json"
{
  "extends": ["turbo"]
}
```

You can also configure rules available in the configuration:

```json title="./packages/eslint-config/base.json"
{
  "plugins": ["turbo"],
  "rules": {
    "turbo/no-undeclared-env-vars": [
      "error",
      {
        "allowList": ["^ENV_[A-Z]+$"]
      }
    ]
  }
}
```

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
