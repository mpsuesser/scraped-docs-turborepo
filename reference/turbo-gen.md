---
url: https://turborepo.dev/docs/reference/turbo-gen
title: "@turbo/gen"
description: "Reference for the `@turbo/gen` package that provides type definitions for Turborepo code generators."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
---

# @turbo/gen



Use this package for type definitions in your [Turborepo code generators](generate.md).

```ts title="./turbo/generators/my-generator.ts"
import type { PlopTypes } from "@turbo/gen"; // [!code highlight]

// [!code word:PlopTypes]
export default function generator(plop: PlopTypes.NodePlopAPI): void {
  // Create a generator
  plop.setGenerator("Generator name", {
    description: "Generator description",
    // Gather information from the user
    prompts: [
      ...
    ],
    // Perform actions based on the prompts
    actions: [
      ...
    ],
  });
}
```

## Turborepo variables

When running generator actions, Turborepo injects a `turbo` object into the action answers data. This object provides information about the repository and the generator's context.

### `turbo.paths`

| Variable                | Type                  | Description                                                                                                                             |
| ----------------------- | --------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `turbo.paths.cwd`       | `string`              | The current working directory when the generator was invoked.                                                                           |
| `turbo.paths.root`      | `string`              | The root directory of the Turborepo project.                                                                                            |
| `turbo.paths.workspace` | `string \| undefined` | The root directory of the workspace that contains the generator. `undefined` for root-level generators that are not within a workspace. |

### `turbo.configs`

An array of `TurboConfig` objects representing all `turbo.json` and `turbo.jsonc` files found in the repository.

Each `TurboConfig` object has the following shape:

| Property          | Type      | Description                                                                |
| ----------------- | --------- | -------------------------------------------------------------------------- |
| `config`          | `object`  | The parsed contents of the Turbo configuration file.                       |
| `turboConfigPath` | `string`  | The absolute path to the Turbo configuration file.                         |
| `workspacePath`   | `string`  | The absolute path to the workspace directory containing the configuration. |
| `isRootConfig`    | `boolean` | Whether the configuration is from the root of the monorepo.                |

For more information, [visit the Generating code guide](../guides/generating-code.md).


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
