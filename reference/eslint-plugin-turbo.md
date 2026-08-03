---
url: https://turborepo.dev/docs/reference/eslint-plugin-turbo
title: "eslint-plugin-turbo"
description: "Reference for the `eslint-plugin-turbo` package that detects undeclared environment variables in your code."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
---

[The `eslint-plugin-turbo` package](https://www.npmjs.com/package/eslint-plugin-turbo) helps you find environment variables that are used in your code that are not a part of Turborepo's hashing. Environment variables used in your source code that are not accounted for in `turbo.json` will be highlighted in your editor and errors will show as ESLint output.

## Installation

Install `eslint-plugin-turbo` into the location where your ESLint configuration is held:

#### pnpm

```
pnpm add eslint-plugin-turbo --filter=@repo/eslint-config
```

#### yarn

```
yarn workspace @acme/eslint-config add eslint-plugin-turbo --dev
```

#### npm

```
npm i --save-dev eslint-plugin-turbo -w @acme/eslint-config
```

#### bun

```
cd packages/eslint-config && bun install eslint-plugin-turbo --dev
```

## Usage (Flat Config eslint.config.js)

ESLint v9 uses the Flat Config format seen below:

```
import turbo from "eslint-plugin-turbo";

export default [turbo.configs["flat/recommended"]];
```

Otherwise, you may configure the rules you want to use under the rules section.

```
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

## Example (Flat Config eslint.config.js)

```
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

## Usage (Legacy eslintrc\*)

Add `turbo` to the plugins section of your `.eslintrc` configuration file. You can omit the `eslint-plugin-` prefix:

```
{
  "plugins": ["turbo"]
}
```

Then configure the rules you want to use under the rules section.

```
{
  "rules": {
    "turbo/no-undeclared-env-vars": "error"
  }
}
```

## Example (Legacy eslintrc\*)

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
