---
url: https://turborepo.dev/docs/reference/eslint-config-turbo
title: "eslint-config-turbo"
description: "Reference for the `eslint-config-turbo` package that detects undeclared environment variables in your code."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

[The `eslint-config-turbo` package](https://www.npmjs.com/package/eslint-config-turbo) helps you find environment variables that are used in your code that are not a part of Turborepo's hashing. Environment variables used in your source code that are not accounted for in `turbo.json` will be highlighted in your editor and errors will show as ESLint output.

## Installation

Install `eslint-config-turbo` into the location where your ESLint configuration is held:

#### pnpm

```
pnpm add eslint-config-turbo --filter=@repo/eslint-config
```

#### yarn

```
yarn workspace @acme/eslint-config add eslint-config-turbo --dev
```

#### npm

```
npm install --save-dev eslint-config-turbo -w @acme/eslint-config
```

#### bun

```
cd packages/eslint-config && bun install eslint-config-turbo --dev
```

## Usage (Flat Config eslint.config.js)

```
import turboConfig from "eslint-config-turbo/flat";

export default [
  ...turboConfig,
  // Other configuration
];
```

You can also configure rules available in the configuration:

```
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

## Usage (Legacy eslintrc\*)

Add `turbo` to the extends section of your eslint configuration file. You can omit the `eslint-config-` prefix:

```
{
  "extends": ["turbo"]
}
```

You can also configure rules available in the configuration:

```
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
