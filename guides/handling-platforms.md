---
url: https://turborepo.dev/docs/guides/handling-platforms
title: "Handling platforms"
description: "Learn how to handle caching around operating systems, architectures, and other arbitrary conditions for Turborepo tasks."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

# Handling platforms



## Node.js versions

To account for Node.js versions, use [the engines key in package.json](https://docs.npmjs.com/cli/v10/configuring-npm/package-json#engines). Turborepo will account for changes to this field and miss cache accordingly.

## Operating systems, architecture, and other arbitrary conditions

For advanced use cases, you may want the operating system (OS), architecture, or other external factors to contribute to your hash.

### 1. Write an arbitrary file to disk

First, create a script that accounts for the hash contributors that you are interested in. For example, here is a Node.js script that identifies platform and architecture and writes those details to a file (`turbo-cache-key.json`):

```js title="./scripts/create-turbo-cache-key.js"
#!/usr/bin/env node

const { writeFileSync } = require("fs");

const { platform, arch } = process;
const file = "turbo-cache-key.json";
const str = JSON.stringify({ platform, arch });
console.log(`Generating cache key: ${str}`);
writeFileSync(file, str);
```

### 2. Add the file to your .gitignore

You won't want to commit this file to source control since it's dependent on environment. Add it to your `.gitignore`:

```diff title=".gitignore"
+ turbo-cache-key.json
```

### 3. Add the file to the hash

Now, make sure that `turbo` is aware of the file by adding it to task inputs. You can do this two ways:

* **For specific tasks**: Include the file in [the `inputs` array](../reference/configuration.md#inputs) of the task(s):

```json title="./turbo.json"
{
  "tasks": {
    "build-for-platforms": {
      "dependsOn": ["^build"],
      "inputs": ["$TURBO_DEFAULT$", "turbo-cache-key.json"]
    }
  }
}
```

* **For all tasks**: Add the file to [`globalDependencies`](../reference/configuration.md#globaldependencies)

```json title="./turbo.json"
{
  "globalDependencies": ["turbo-cache-key.json"],
  "tasks": {
    ...
  }
}
```

### 4. Generate the file before running `turbo`

Last, you'll want to ensure that you run the script before running `turbo`. For example:

```json title="./package.json"
{
  "scripts": {
    "build-for-platforms": "node ./scripts/create-turbo-cache-key.js && turbo run build"
  }
}
```

`turbo run build` will now take into account the contents of `turbo-cache-key.json` when calculating the hash for the `build` task.


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
