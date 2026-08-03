---
url: https://turborepo.dev/docs/crafting-your-repository/configuring-tasks
title: "Configuring tasks"
description: "Define task dependencies, outputs, and inputs in turbo.json to orchestrate your repository's workflows."
access_date: 2026-08-03T21:45:14.046Z
current_date: 2026-08-03T21:45:14.046Z
---

Learn how to describe the workflows in your repository to get them done as fast as possible.

A task is a script that Turborepo runs. You can express relationships between tasks in your [`turbo.json` configuration](../reference/configuration.md) and [Package Graph](../core-concepts/package-and-task-graph.md#package-graph).

Turborepo will always parallelize any work that it can to ensure everything runs as fast as possible. This is faster than running tasks one at a time, and it's a part of what makes Turborepo so fast.

For example, `yarn workspaces run lint && yarn workspaces run build && yarn workspaces run test` would look like this:

![A graphical representation of tasks running sequentially, with lots of empty space where scripts are not being ran.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fslow-tasks-light.3buooozk27cm2.png&w=3840&q=75&dpl=dpl_Np2RHkNG8vgrhan6tknPK2NjEkNL)

But, to get the same work done **faster** with Turborepo, you can use `turbo run lint build test`:

![A graphical representation of turbo run lint test build. It shows all tasks running in parallel, with much less empty space where scripts are not being ran.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fturborepo-tasks-fast-light.0gccg_-he037-.png&w=3840&q=75&dpl=dpl_Np2RHkNG8vgrhan6tknPK2NjEkNL)

## Getting started

The root `turbo.json` file is where you'll register the tasks that Turborepo will run. Once you have your tasks defined, you'll be able to run one or more tasks using [`turbo run`](../reference/run.md).

- If you're starting fresh, we recommend [creating a new repository using `create-turbo`](../getting-started/installation.md) and editing the `turbo.json` file to try out the snippets in this guide.
- If you're adopting Turborepo in an existing repository, create a `turbo.json` file in the root of your repository. You'll be using it to learn about the rest of the configuration options in this guide.

turbo.json

package.json

## Defining tasks

Each key in the `tasks` object is a task that can be executed by `turbo run`. Turborepo will search your packages for **scripts in their `package.json` that have the same name as the task**.

To define a task, use [the `tasks` object](../reference/configuration.md#tasks) in `turbo.json`. For example, a basic task with no dependencies and no outputs named `build` might look like this:

```
{
  "tasks": {
    "build": {} // Incorrect!
  }
}
```

If you run `turbo run build` at this point, Turborepo will run all `build` scripts in your packages in parallel and won't cache any file outputs. **This will quickly lead to errors.** You're missing a few important pieces to make this work how you'd expect.

### Running tasks in the right order

[The `dependsOn` key](../reference/configuration.md#dependson) is used to specify the tasks that must complete before a different task begins running. For example, in most cases, you want the `build` script for your libraries to complete before your application's `build` script runs. To do this, you'd use the following `turbo.json`:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

You now have the build order you would expect, building *dependencies* before *dependents*.

**But be careful.** At this point, you haven't marked the build outputs for caching. To do so, jump to the [Specifying outputs](#specifying-outputs) section.

#### Depending on tasks in dependencies with ^

The `^` microsyntax tells Turborepo to run the task in direct dependencies before the target package. If your application depends on a library named `ui` and the library has a `build` task, the `build` script in `ui` will run **first**. Once it has successfully completed, the `build` task in your application will run.

This is an important pattern as it ensures that your application's `build` task will have all of the necessary dependencies that it needs to compile. This concept also applies as your dependency graph grows to a more complex structure with many levels of task dependencies.

#### Depending on tasks in the same package

Sometimes, you may need to ensure that two tasks in the same package run in a specific order. For example, you may need to run a `build` task in your library before running a `test` task in the same library. To do this, specify the script in the `dependsOn` key as a plain string (without the `^`).

```
{
  "tasks": {
    "test": {
      "dependsOn": ["build"]
    }
  }
}
```

#### Depending on a specific task in a specific package

You can also specify an individual task in a specific package to depend on. In the example below, the `build` task in `utils` must be run before any `lint` tasks.

```
{
  "tasks": {
    "lint": {
      "dependsOn": ["utils#build"]
    }
  }
}
```

You can also be more specific about the dependent task, limiting it to a certain package:

```
{
  "tasks": {
    "web#lint": {
      "dependsOn": ["utils#build"]
    }
  }
}
```

With this configuration, the `lint` task in your `web` package can only be run after the `build` task in the `utils` package is complete.

#### No dependencies

Some tasks may not have any dependencies. For example, a task for finding typos in Markdown files likely doesn't need to care about the status of your other tasks. In this case, you can omit the `dependsOn` key or provide an empty array.

```
{
  "tasks": {
    "spell-check": {
      "dependsOn": []
    }
  }
}
```

### Specifying outputs

The `outputs` key tells Turborepo **files and directories** it should cache when the task has successfully completed. **Without this key defined, Turborepo will not cache any files. Hitting cache on subsequent runs will not restore any file outputs.**

Below are a few examples of outputs for common tools:

#### Next.js

```
{
  "tasks": {
    "build": {
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"]
    }
  }
}
```

#### Vite

```
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

#### tsc

```
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

Globs are relative to the package, so `dist/**` will handle the `dist` that is outputted for each package, respectively. For more on building globbing patterns for the `outputs` key, see [the globbing specification](../reference/globs.md).

### Specifying inputs

The `inputs` key is used to specify the files that you want to include in the task's hash for [caching](caching.md). By default, Turborepo will include all files in the package that are tracked by Git. However, you can be more specific about which files are included in the hash using the `inputs` key.

As an example, a task for finding typos in Markdown files could be defined like this:

```
{
  "tasks": {
    "spell-check": {
      "inputs": ["**/*.md", "**/*.mdx"]
    }
  }
}
```

Now, **only** changes in Markdown files will cause the `spell-check` task to miss cache.

#### Restoring defaults with $TURBO\_DEFAULT$

[The default `inputs` behavior](../reference/configuration.md#inputs) is often what you will want for your tasks. However, you can increase your cache hit ratios for certain tasks by fine-tuning your `inputs` to ignore changes to files that are known to not affect the task's output.

For this reason, you can use the `$TURBO_DEFAULT$` microsyntax to fine-tune the default `inputs` behavior:

```
{
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "!README.md"]
    }
  }
}
```

In this task definition, Turborepo will use the default `inputs` behavior for the `build` task, but will ignore changes to the `README.md` file. If the `README.md` file is changed, the task will still hit cache.

#### Deferred hashing

Some tasks need to hash files that are created by another task in the same run. Use structured [`inputs`](../reference/configuration.md#deferred-hashing) with `mode: "jit"` or `mode: "dependencyOutputs"` to defer hashing until the task's dependencies have completed.

### Registering Root Tasks

You can also run scripts in the `package.json` in the Workspace root using `turbo`. For example, you may want to run a `lint:root` task for the files in your Workspace's root directory in addition to the `lint` task in each package:

#### turbo.json

```
{
  "tasks": {
    "lint": {
      "dependsOn": ["^lint"]
    },
    "//#lint:root": {}
  }
}
```

#### package.json

```
{
  "scripts": {
    "lint": "turbo run lint lint:root",
    "lint:root": "eslint ."
  }
}
```

With the Root Task now registered, you can use `turbo run lint:root` from the root or `turbo run //#lint:root` in any of the workspaces to run the task. You can also run `turbo run lint lint:root` to run all your linting tasks.

#### When to use Root Tasks

- **Linting and formatting of the Workspace root**: You might have code in your Workspace root that you want to lint and format. For example, you might want to run ESLint or Prettier in your root directory.
- **Incremental migration**: While you're migrating to Turborepo, you might have an in-between step where you have some scripts that you haven't moved to packages yet. In this case, you can create a Root Task to start migrating and fan the tasks out to packages later.
- **Scripts without a package scope**: You may have some scripts that don't make sense in the context of specific packages. Those scripts can be registered as Root Tasks so you can still run them with `turbo` for caching, parallelization, and workflow purposes.

## Advanced use cases

### Using Package Configurations

[Package Configurations](../reference/package-configurations.md) are `turbo.json` files that are placed directly into a package. This allows a package to define specific behavior for its own tasks without affecting the rest of the repository.

In large monorepos with many teams, this allows teams greater control over their own tasks. To learn more, visit [the Package Configurations documentation](../reference/package-configurations.md)

### Composing configurations

[Package Configurations](../reference/package-configurations.md) can be composed for greater control with less configuration:

- [**Extending from other packages**](../reference/package-configurations.md#extending-from-other-packages): Create shared configuration packages that multiple packages can extend from, enabling shared `turbo.json` configurations.
- [**Adding to extended configurations**](../reference/package-configurations.md#extending-arrays-with-turbo_extends): Use the `$TURBO_EXTENDS$` microsyntax to add to inherited array values like `outputs` and `env` instead of replacing them.

### Long-running tasks with runtime dependencies

You might have a long-running task that requires another task to always be running at the same time. For this, use [the `with` key](../reference/configuration.md#with).

```
{
  "tasks": {
    "dev": {
      "with": ["api#dev"],
      "persistent": true,
      "cache": false
    }
  }
}
```

A long-running task never exits, meaning you can't depend on it. Instead, the `with` keyword will run the `api#dev` task whenever the `web#dev` task runs.

### Performing side-effects

Some tasks should always be run no matter what, like a deployment script after a cached build. For these tasks, add `"cache": false` to your task definition.

```
{
  "tasks": {
    "deploy": {
      "dependsOn": ["^build"],
      "cache": false
    },
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

### Dependent tasks that can be run in parallel

Some tasks can be run in parallel despite being dependent on other packages. An example of tasks that fit this description are type checkers, since a type checker doesn't need to wait for outputs in dependencies to run successfully.

Because of this, you may be tempted to define your `check-types` task like this:

```
{
  "tasks": {
    "check-types": {} // Incorrect!
  }
}
```

This runs your tasks in parallel - but doesn't account for source code changes in dependencies. This means you can:

1. Make a breaking change to the interface of your `ui` package.
2. Run `turbo check-types`, hitting cache in an application package that depends on `ui`.

This is incorrect, since the application package will show a successful cache hit, despite not being updated to use the new interface. Checking for TypeScript errors in your application package manually in your editor is likely to reveal errors.

Because of this, you make a small change to your `check-types` task definition:

```
{
  "tasks": {
    "check-types": {
      "dependsOn": ["^check-types"] // This works...but could be faster!
    }
  }
}
```

If you test out making breaking changes in your `ui` package again, you'll notice that the caching behavior is now correct. However, tasks are no longer running in parallel.

To meet both requirements (correctness and parallelism), you can introduce [Transit Nodes](../core-concepts/package-and-task-graph.md#transit-nodes) to your Task Graph:

```
{
  "tasks": {
    "transit": {
      "dependsOn": ["^transit"]
    },
    "check-types": {
      "dependsOn": ["transit"]
    }
  }
}
```

These Transit Nodes create a relationship between your package dependencies using a task that doesn't do anything because it doesn't match a script in any `package.json` s. Because of this, your tasks can run in parallel **and** be aware of changes to their internal dependencies.

## Next steps

There are more options available in [the Configuring `turbo.json` documentation](../reference/configuration.md) that you will explore in the coming guides. For now, you can start running a few tasks to see how the basics work.
