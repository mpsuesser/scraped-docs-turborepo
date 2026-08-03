---
url: https://turborepo.dev/docs/reference/package-configurations
title: "Package Configurations"
description: "Reference for creating per-package turbo.json overrides to customize task behavior."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

Learn how to use Package Configurations to bring greater task flexibility to your monorepo's package.

Many monorepos can declare a `turbo.json` in the root directory with a [task description](configuration.md#tasks) that applies to all packages. But, sometimes, a monorepo can contain packages that need to configure their tasks differently.

To accommodate this, Turborepo enables you to extend the root configuration with a `turbo.json` in any package. This flexibility enables a more diverse set of apps and packages to co-exist in a Workspace, and allows package owners to maintain specialized tasks and configuration without affecting other apps and packages of the monorepo.

## How it works

To override the configuration for any task defined in the root `turbo.json`, add a `turbo.json` file in any package of your monorepo with a top-level `extends` key:

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      // Custom configuration for the build task in this package
    },
    "special-task": {}, // New task specific to this package
  },
}
```

## Inheritance behavior

When a Package Configuration extends the root `turbo.json`, task properties are inherited differently depending on their type.

### Scalar fields are inherited

Scalar fields like `outputLogs`, `cache`, `persistent`, and `interactive` are **inherited** from the root configuration. You only need to specify them in a Package Configuration if you want to override them.

For example, if your root `turbo.json` sets `"outputLogs": "hash-only"` for a task, all packages inherit that setting automatically.

### Array fields replace by default

Array fields like `outputs`, `env`, `inputs`, `dependsOn`, and `passThroughEnv` **completely replace** the root configuration's values by default.

```
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"],
      "env": ["NODE_ENV"],
    },
  },
}
```

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      // This REPLACES the root outputs - "dist/**" is NOT included
      "outputs": [".next/**"],
    },
  },
}
```

### Extending arrays with $TURBO\_EXTENDS$

To **add** to inherited array values instead of replacing them, use the [`$TURBO_EXTENDS$` microsyntax](configuration.md#turbo_extends):

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      // Inherits "dist/**" from root AND adds ".next/**"
      "outputs": ["$TURBO_EXTENDS$", ".next/**"],
    },
  },
}
```

The `$TURBO_EXTENDS$` marker must be the first element in the array. It works with `outputs`, `env`, `inputs`, `dependsOn`, `passThroughEnv`, and `with`.

### Extending from other packages

Package Configurations can extend from other packages' `turbo.json` files, not just the root. This enables composing shared task configurations across packages.

Extend from any package by using its `name` from `package.json` in your `extends` array. For example, if you have a Next.js app at `./apps/web` with `"name": "web"` in its `package.json`:

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"],
    },
    "dev": {
      "cache": false,
      "persistent": true,
    },
  },
}
```

Another Next.js app can extend from it to share the same configuration:

```
{
  "extends": ["//", "web"],
  "tasks": {
    "build": {
      // Additional customization specific to this package
      "env": ["NEXT_PUBLIC_DOCS_URL"],
    },
  },
}
```

#### Inheritance order

When extending from multiple configurations, task definitions are merged in the order they appear in the `extends` array:

1. Root `turbo.json` (`"//"`) is applied first
2. Each additional package configuration is applied in order
3. The current package's configuration is applied last

Later configurations override earlier ones for scalar fields. For array fields, see [Extending arrays with `$TURBO_EXTENDS$`](#extending-arrays-with-turbo_extends) to append instead of replace.

#### Patterns for sharing configuration

**Extend from an existing package**: If you already have a package with the configuration you want to share, other packages can extend from it directly. This works well when one package serves as the "canonical" example for similar packages (e.g., your main Next.js app that other Next.js apps can extend from).

**Create a dedicated configuration package**: For larger monorepos, you may want to create packages specifically for sharing configuration. This keeps configuration separate from application code and makes it clear that other packages depend on these settings. These packages typically only contain a `package.json` and `turbo.json`.

#### shared-config/package.json

```
{
  "name": "shared-config",
  "private": true
}
```

#### shared-config/turbo.json

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

#### apps/web/turbo.json

```
{
  "extends": ["//", "shared-config"],
  "tasks": {
    "build": {
      "env": ["MY_API_URL"]
    }
  }
}
```

### Excluding tasks from inheritance

When extending from the root or other packages, your package inherits all their task definitions by default. You can use the task-level `extends` field to opt out of specific tasks.

#### Excluding a task entirely

To completely exclude an inherited task from your package, set `extends: false` with no other configuration:

```
{
  "tasks": {
    "build": {},
    "lint": {},
    "test": {},
  },
}
```

```
{
  "extends": ["//"],
  "tasks": {
    "lint": {
      "extends": false, // This package does not have a lint task
    },
  },
}
```

When you run `turbo run lint`, the `ui` package will be skipped entirely for the `lint` task.

#### Creating a fresh task definition

To create a new task definition that doesn't inherit any configuration from the extends chain, use `extends: false` along with other task configuration:

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "extends": false, // Don't inherit from root
      "outputs": ["out/**"],
      "env": ["SPECIAL_VAR"],
    },
  },
}
```

This is useful when you need completely different task configuration that shouldn't merge with inherited values.

#### Exclusions propagate through the chain

When a package excludes a task, that exclusion propagates to packages that extend from it:

```
{
  "extends": ["//"],
  "tasks": {
    "lint": {
      "extends": false, // Exclude lint from this config
    },
  },
}
```

```
{
  "extends": ["//", "base-config"],
  "tasks": {
    // web does not inherit lint from root because base-config excluded it
  },
}
```

## Examples

### Different frameworks in one Workspace

Let's say your monorepo has multiple [Next.js](https://nextjs.org/) apps, and one [SvelteKit](https://kit.svelte.dev/) app. Both frameworks create their build output with a `build` script in their respective `package.json`. You *could* configure Turborepo to run these tasks with a single `turbo.json` at the root like this:

```
{
  "tasks": {
    "build": {
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**", ".svelte-kit/**"],
    },
  },
}
```

Notice that both `.next/**` and `.svelte-kit/**` need to be specified as [`outputs`](configuration.md#outputs), even though Next.js apps do not generate a `.svelte-kit` directory, and vice versa.

With Package Configurations, you can instead add custom configuration in the SvelteKit package in `apps/my-svelte-kit-app/turbo.json`:

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "outputs": [".svelte-kit/**"],
    },
  },
}
```

and remove the SvelteKit-specific [`outputs`](configuration.md#outputs) from the root configuration:

```
{
  "tasks": {
    "build": {
-      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**", ".svelte-kit/**"]
+      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"]
    }
  }
}
```

This not only makes each configuration easier to read, it puts the configuration closer to where it is used.

### Specialized tasks

In another example, say that the `build` task in one package `dependsOn` a `compile` task. You could universally declare it as `dependsOn: ["compile"]`. This means that your root `turbo.json` has to have an empty `compile` task entry:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["compile"]
    },
    "compile": {}
  }
}
```

With Package Configurations, you can move that `compile` task into the `apps/my-custom-app/turbo.json`,

```
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "dependsOn": ["compile"]
    },
    "compile": {}
  }
}
```

and remove it from the root:

```
{
  "tasks": {
+    "build": {}
-    "build": {
-      "dependsOn": ["compile"]
-    },
-    "compile": {}
  }
}
```

Now, the owners of `my-app`, can have full ownership over their `build` task, but continue to inherit any other tasks defined at the root.

## Comparison to package-specific tasks

The [`package#task` syntax](../crafting-your-repository/configuring-tasks.md#depending-on-a-specific-task-in-a-specific-package) in the root `turbo.json` **completely overwrites** all task configuration—nothing is inherited.

With Package Configurations, scalar fields are inherited and only the fields you specify are overridden. This means less duplication when you only need to change one or two properties.

## Boundaries Tags Experimental

Package Configurations are also used to declare Tags for Boundaries. To do so, add a `tags` field to your `turbo.json`:

```
{
+ "tags": ["my-tag"],
  "extends": ["//"],
  "tasks": {
    "build": {
      "dependsOn": ["compile"]
    },
    "compile": {}
  }
}
```

From there, you can define rules for which dependencies or dependents a tag can have. Check out the [Boundaries documentation](boundaries.md#tags) for more details.

## Limitations

Although the general idea is the same as the root `turbo.json`, Package Configurations come with a set of guardrails that can prevent packages from creating potentially confusing situations.

### Package Configurations cannot use the workspace#task syntax as task entries

The `package` is inferred based on the location of the configuration, and it is not possible to change configuration for another package. For example, in a Package Configuration for `my-nextjs-app`:

```
{
  "tasks": {
    "my-nextjs-app#build": {
      // This is not allowed. Even though it's
      // referencing the correct package, "my-nextjs-app"
      // is inferred, and we don't need to specify it again.
      // This syntax also has different behavior, so we do not want to allow it.
      // (see "Comparison to package-specific tasks" section)
    },
    "my-sveltekit-app#build": {
      // Changing configuration for the "my-sveltekit-app" package
      // from Package Configuration in "my-nextjs-app" is not allowed.
    },
    "build": {
      // Just use the task name!
    },
  },
}
```

Note that the `build` task can still depend on a package-specific task:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["some-pkg#compile"],
    },
  },
}
```

### Package Configurations can only override values in the tasks key

It is not possible to override [global configuration](configuration.md#global-options) like `globalEnv` or `globalDependencies` in a Package Configuration. Configuration that would need to be altered in a Package Configuration is not truly global and should be configured differently.

### Root turbo.json cannot use the extends key

To avoid creating circular dependencies on packages, the root `turbo.json` cannot extend from anything. The `extends` key will be ignored.

## Troubleshooting

In large monorepos, it can sometimes be difficult to understand how Turborepo is interpreting your configuration. To help, we've added a `resolvedTaskDefinition` to the [Dry Run](run.md#--dry----dry-run) output. If you run `turbo run build --dry-run`, for example, the output will include the combination of all `turbo.json` configurations that were considered before running the `build` task.
