---
url: https://turborepo.dev/docs/guides/generating-code
title: "Generating code"
description: "Use Turborepo's built-in generators and custom Plop configurations to scaffold new packages and code."
access_date: 2026-08-03T18:24:07.617Z
current_date: 2026-08-03T18:24:07.617Z
---

Learn how to generate code using Turborepo.

Splitting your monorepo into packages is a great way to organize your code, speed up tasks, and improve the local development experience. With Turborepo's code generation, it's easy to generate new source code for packages, modules, and even individual UI components in a structured way that integrates with the rest of your repository.

## Add an empty package

Add a new, empty app or package to your monorepo.

```
turbo gen workspace
```

View all available [options](../reference/generate.md#workspace) for `gen workspace`.

## Copy an existing package

You can use an existing workspace as a template for your new app or package. This works for both workspaces within your existing monorepo, and remote workspaces from other repositories (specified via GitHub URL).

### Examples

Create a new package in your monorepo by copying from an existing package in your repo.

```
turbo gen workspace --copy
```

Create a new workspace in your monorepo by copying from a remote package.

```
turbo gen workspace --copy https://github.com/vercel/turborepo/tree/main/examples/with-tailwind/packages/tailwind-config
```

View all available [options](../reference/generate.md#workspace) for `gen workspace --copy`.

## Custom generators

If a built-in generator does not fit your needs, you can create your own custom generator using [Plop](https://plopjs.com/) configurations. Turborepo will automatically detect any generator configurations within your repo, and make them available to run from the command line.

While Turborepo understands all Plop configuration options and features, it provides a few additional features to improve the experience of writing generators within a repo configured with Turborepo.

1. Generators are automatically discovered, loaded, and organized per workspace (no need to manually `load` them within a single configuration file)
2. Generators are automatically run from the root of the workspace where they are defined
3. Generators can be invoked from anywhere within your repo (or outside it via the [`--root`](../reference/generate.md#--root-path) flag)
4. TypeScript generators are supported with zero configuration
5. `plop` is not required to be installed as a dependency of your repo

### Getting started

To build and run a custom generator, run the following command from anywhere within your monorepo using Turborepo.

```
turbo gen
```

You'll be prompted to select an existing generator or to create one if you don't have any yet. You can also create your configuration manually at `turbo/generators/config.ts` (or `config.js`) at the root of your repo - or within *any* workspace.

For example, the following illustrates a monorepo with three locations for generators:

#### pnpm

package.json

package.json

package.json

package-lock.yaml

pnpm-workspace.yaml

turbo.json

#### yarn

package.json

package.json

package.json

yarn.lock

turbo.json

#### npm

package.json

package.json

package.json

package-lock.json

turbo.json

#### bun

package.json

package.json

package.json

bun.lock

turbo.json

Generators created within workspaces are automatically run from the workspace root, **not** the repo root, nor the location of the generator configuration.

This makes your generators more simple to write. Creating a file at `[workspace-root]` only needs to be specified as `<file>` rather than `../../<file>`.

Learn more about [creating custom generators using Plop](https://plopjs.com/documentation/#creating-a-generator).

### Writing generators

A generator configuration file is a function that returns a [Plop](https://plopjs.com/) configuration object. The configuration object is used to define the generator's prompts, and actions.

In its simplest form, a generator configuration file looks like:

```
import type { PlopTypes } from "@turbo/gen";

export default function generator(plop: PlopTypes.NodePlopAPI): void {
  // create a generator
  plop.setGenerator("Generator name", {
    description: "Generator description",
    // gather information from the user
    prompts: [
      ...
    ],
    // perform actions based on the prompts
    actions: [
      ...
    ],
  });
}
```

#### Prompts

Prompts are written using [Plop prompts](https://plopjs.com/documentation/#using-prompts) and are used to gather information from the user.

#### Actions

Actions can use [built-in Plop actions](https://plopjs.com/documentation/#built-in-actions), or [custom action functions](https://plopjs.com/documentation/#functionsignature-custom-action) that you define yourself:

```
import type { PlopTypes } from "@turbo/gen";

const customAction: PlopTypes.CustomActionFunction = async (answers) => {
  // fetch data from a remote API
  const results = await fetchRemoteData();
  // add the response to the answers, making this data available to actions
  answers.results = results;
  // return a status string
  return 'Finished data fetching!';
}

export default function generator(plop: PlopTypes.NodePlopAPI): void {
  // create a generator
  plop.setGenerator("Generator name", {
    description: "Generator description",
    prompts: [
      ...
    ],
    actions: [
      customAction
      {/* actions now have access to \`answers.results\` */}
      ...
    ],
  });
}
```

### Turborepo variables

When running actions, Turborepo automatically injects a `turbo` object into the answers data. This gives your templates and custom actions access to useful information about the repository without needing to discover it yourself.

#### Using in templates

In [Handlebars templates](https://plopjs.com/documentation/#built-in-actions), access the variables using double curly braces:

```
// Generated at {{ turbo.paths.root }}/src/components
```

#### Using in custom actions

In [custom action functions](https://plopjs.com/documentation/#functionsignature-custom-action), the `turbo` object is available on the `answers` parameter:

```
import type { PlopTypes } from "@turbo/gen";

export default function generator(plop: PlopTypes.NodePlopAPI): void {
  plop.setGenerator("example", {
    description: "An example using turbo variables",
    prompts: [],
    actions: [
      async (answers) => {
        const { turbo } = answers;
        console.log(\`Monorepo root: ${turbo.paths.root}\`);
        return "Done!";
      },
    ],
  });
}
```

The available variables are documented in the [`@turbo/gen` reference](../reference/turbo-gen.md#turborepo-variables).

### Running generators

Once you have created your generator configuration file, you can skip the selection prompt and directly run a specified generator with:

```
turbo gen [generator-name]
```

Arguments can also be passed directly to the generator prompts using `--args`

```
turbo gen [generator-name] --args answer1 answer2 ...
```

See [bypassing prompts](https://plopjs.com/documentation/#bypassing-prompts) in the Plop documentation for more information.

View all available [options](../reference/generate.md#run-generator-name) for `gen`.
