---
url: https://turborepo.dev/docs/reference/eslint-plugin-turbo
title: "eslint-plugin-turbo"
description: "Learn more about eslint-plugin-turbo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[The `eslint-plugin-turbo` package](https://www.npmjs.com/package/eslint-plugin-turbo) helps you find environment variables that are used in your code that are not a part of Turborepo's hashing. Environment variables used in your source code that are not accounted for in `turbo.json` will be highlighted in your editor and errors will show as ESLint output.

## Installation

Install `eslint-plugin-turbo` into the location where your ESLint configuration is held:

#### pnpm

```bash title="Terminal"
pnpm add eslint-plugin-turbo --filter=@repo/eslint-config
```

#### yarn

```bash title="Terminal"
yarn workspace @acme/eslint-config add eslint-plugin-turbo --dev
```

#### npm

```bash title="Terminal"
npm i --save-dev eslint-plugin-turbo -w @acme/eslint-config
```

#### bun

```bash title="Terminal"
cd packages/eslint-config && bun install eslint-plugin-turbo --dev
```

#### nub

```bash title="Terminal"
cd packages/eslint-config && nub add eslint-plugin-turbo --save-dev
```

#### aube

```bash title="Terminal"
cd packages/eslint-config && aube add eslint-plugin-turbo --save-dev
```

## Usage (Flat Config `eslint.config.js`)

ESLint v9 uses the Flat Config format seen below:

```js title="./packages/eslint-config/base.js"
import turbo from "eslint-plugin-turbo";

export default [turbo.configs["flat/recommended"]];
```

Otherwise, you may configure the rules you want to use under the rules section.

```js title="./packages/eslint-config/base.js"
import turbo from "eslint-plugin-turbo";

export default [
  {
    plugins: {
      turbo,
    },
    rules: {
      "turbo/no-undeclared-env-vars": "error",
    },
  },
];
```

## Example (Flat Config `eslint.config.js`)

```js title="./packages/eslint-config/base.js"
import turbo from "eslint-plugin-turbo";

export default [
  {
    plugins: {
      turbo,
    },
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

Add `turbo` to the plugins section of your `.eslintrc` configuration file. You can omit the `eslint-plugin-` prefix:

```json title="./packages/eslint-config/base.json"
{
  "plugins": ["turbo"]
}
```

Then configure the rules you want to use under the rules section.

```json title="./packages/eslint-config/base.json"
{
  "rules": {
    "turbo/no-undeclared-env-vars": "error"
  }
}
```

## Example (Legacy `eslintrc*`)

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
