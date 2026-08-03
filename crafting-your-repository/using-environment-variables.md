---
url: https://turborepo.dev/docs/crafting-your-repository/using-environment-variables
title: "Using environment variables"
description: "Account for environment variables in task hashing, configure environment modes, and handle .env files."
access_date: 2026-08-03T19:10:40.966Z
current_date: 2026-08-03T19:10:40.966Z
---

Learn how to handle environments for your applications.

Environment variable inputs are a vital part of your applications that you'll need to account for in your Turborepo configuration.

There are three important questions when working with environment variables in Turborepo:

- [Are my environment variables accounted for in the task hash?](#adding-environment-variables-to-task-hashes)
- [Which Environment Mode will `turbo` use?](#environment-modes)
- [Have I handled my `.env` files?](#handling-env-files)

## Adding environment variables to task hashes

Turborepo needs to be aware of your environment variables to account for changes in application behavior. To do this, use the `env` and `globalEnv` keys in your `turbo.json` file.

```
{
  "globalEnv": ["IMPORTANT_GLOBAL_VARIABLE"],
  "tasks": {
    "build": {
      "env": ["MY_API_URL", "MY_API_KEY"]
    }
  }
}
```

- **globalEnv**: Changes to the values of any environment variables in this list will change the hash for all tasks.
- **env**: Includes changes to the values of environment variables that affect the task, allowing for better granularity. For example, a `lint` task probably doesn't need to miss cache when the value of `API_KEY` changes, but a `build` task likely should.

### Framework Inference

Turborepo automatically adds prefix wildcards to your [`env`](../reference/configuration.md#env) key for common frameworks. If you're using one of the frameworks below in a package, you don't need to specify environment variables with these prefixes:

| Framework | `env` wildcards |
| --- | --- |
| Next.js | `NEXT_PUBLIC_*` |
| Vite | `VITE_*` |
| Create React App | `REACT_APP_*` |
| Gatsby | `GATSBY_*` |
| Nuxt | `NUXT_*`, `NUXT_ENV_*` |
| Expo | `EXPO_PUBLIC_*` |
| Astro | `PUBLIC_*` |
| SvelteKit | `PUBLIC_*` |
| Remix | `REMIX_*` |
| SolidStart | `VITE_*` |
| RedwoodJS | `REDWOOD_ENV_*` |
| Sanity | `SANITY_STUDIO_*` |

If you'd like to opt out of Framework Inference, you can do so by:

- Running your tasks with `--framework-inference=false`
- Adding a negative wildcard to the `env` key (for example, `"env": ["!NEXT_PUBLIC_*"]`)

## Environment Modes

Turborepo's Environment Modes allow you to control which environment variables are available to a task at runtime:

- [Strict Mode](#strict-mode) (Default): Filter environment variables to **only** those that are specified in the `env` and `globalEnv` keys in `turbo.json`.
- [Loose Mode](#loose-mode): Allow all environment variables for the process to be available.

### Strict Mode

Strict Mode filters the environment variables available to a task's runtime to **only** those that are specified in the `globalEnv` and `env` keys in `turbo.json`.

This means that tasks that do not account for all of the environment variables that they need are likely to fail. This is a good thing, since you don't want to cache a task that can potentially have different behavior in a different environment.

Cache safety with Strict Mode

#### Passthrough variables

In advanced use cases, you may want to make some environment variables available to a task without including them in the hash. Changes to these variables don't affect task outputs but still need to be available for the task to run successfully.

For these cases, add those environment variables to [`globalPassThroughEnv`](../reference/configuration.md#globalpassthroughenv) and [`passThroughEnv`](../reference/configuration.md#passthroughenv).

#### CI vendor compatibility

Strict Mode will filter out environment variables that come from your CI vendors until you've accounted for them using [`env`](../reference/configuration.md#env), [`globalEnv`](../reference/configuration.md#globalenv), [`passThroughEnv`](../reference/configuration.md#passthroughenv), or [`globalPassThroughEnv`](../reference/configuration.md#globalpassthroughenv).

If any of these variables are important to your tasks and aren't included by [Framework Inference](#framework-inference), make sure they are in your `turbo.json` configuration.

### Loose Mode

Loose Mode does not filter your environment variables according to your `globalEnv` and `env` keys. This makes it easier to get started with incrementally migrating to Strict Mode.

Use [the `--env-mode` flag](../reference/run.md#--env-mode-option) to enable Loose Mode on any invocation where you're seeing environment variables cannot be found by your scripts:

```
turbo run build --env-mode=loose
```

As long as the environment variable is available when `turbo` is ran, your script will be able to use it. However, this also **lets you accidentally forget to account for an environment variable in your configuration much more easily**, allowing the task to hit cache when it shouldn't.

For example, you may have some code in your application that fetches data from an API, using an environment variable for the base URL:

```
const data = fetch(\`${process.env.MY_API_URL}/resource/1\`);
```

You then build your application using a value for `MY_API_URL` that targets your preview environment. When you're ready to ship your application, you build for production and see a cache hit - even though the value of the `MY_API_URL` variable has changed! `MY_API_URL` changed - but Turborepo restored a version of your application from cache that uses the preview environment's `MY_API_URL` rather than production's.

When you're using Loose Mode, `MY_API_URL` is available in the task runtime **even though it isn't accounted for in the task hash**. To make this task more likely to fail and protect you from this misconfiguration, we encourage you to opt for [Strict Mode](#strict-mode).

### Platform Environment Variables

When deploying your application to [Vercel](https://vercel.com/new?ref=turborepo), you likely already have [environment variables](https://vercel.com/docs/projects/environment-variables) configured on your project. Turborepo will automatically check these variables against your `turbo.json` configuration to ensure that you've [accounted for them](#adding-environment-variables-to-task-hashes), and will warn you about any missing variables.

This functionality can be disabled by setting `TURBO_PLATFORM_ENV_DISABLED=true`

## Handling.env files

`.env` files are great for working on an application locally. **Turborepo does not load.env files into your task's runtime**, leaving them to be handled by your framework, or tools like [`dotenv`](https://www.npmjs.com/package/dotenv).

However, it's important that `turbo` knows about changes to values in your `.env` files so that it can use them for hashing. If you change a variable in your `.env` files between builds, the `build` task should miss cache.

To do this, add the files to the [`inputs`](../reference/configuration.md#inputs) key:

```
{
  "globalDependencies": [".env"], // All task hashes
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", ".env"] // Only the \`build\` task hash
    }
  }
}
```

### Multiple.env files

You can capture multiple `.env` files at once using a `*`.

```
{
  "globalDependencies": [".env"], // All task hashes
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", ".env*"] // Only the \`build\` task hash
    }
  }
}
```

## Best practices

### Environment variables from dependencies

When an application consumes a package that relies on environment variables, those variables must be accounted for in your `turbo.json` to ensure cache correctness.

For example, if an app called `docs` depends on a package `common`, there are two scenarios for handling environment variables:

1. **The application depends on the package's task** If the `docs` app's `build` task depends on the `common` package's `build` task (e.g., `"dependsOn": ["^build"]`), and `common` declares an environment variable in its own `turbo.json`, Turborepo handles this automatically. Changing the environment variable will cause `common` to miss the cache and rebuild, which in turn causes `docs` to rebuild because of the dependency relationship.
2. **The application imports the package directly without a build step** If the `common` package does not have a build step, but exposes variables that the `docs` app uses at build time (like `process.env.MY_VAR`), the `docs` application *must* declare the environment variable directly in its own `env` or `globalEnv` key in its `turbo.json`.

```
{
  "tasks": {
    "build": {
      "env": ["MY_VAR"]
    }
  }
}
```

### Use.env files in Application Packages

Using a `.env` file at the root of the repository is not recommended. Instead, we recommend placing your `.env` files into the [Application Packages](../core-concepts/package-types.md#application-packages) where they're used.

This practice more closely models the runtime behavior of your applications since environment variables exist in each application's runtime individually. Additionally, as your monorepo scales, this practice makes it easier to manage each application's environment, preventing environment variable leakage across applications.

### Use eslint-config-turbo

[The `eslint-config-turbo` package](../reference/eslint-config-turbo.md) helps you find environment variables that are used in your code that aren't listed in your `turbo.json`. This helps ensure that all your environment variables are accounted for in your configuration.

### Avoid creating or mutating environment variables at runtime

Turborepo hashes the environment variables for your task at the beginning of the task. If you create or mutate environment variables during the task, Turborepo will not know about these changes and will not account for them in the task hash.

For instance, Turborepo will not be able to detect the inline variable in the example below:

```
{
  "scripts": {
    "dev": "export MY_VARIABLE=123 && next dev"
  }
}
```

`MY_VARIABLE` is being added to the environment *after* the `dev` task has started, so `turbo` will not be able to use it for hashing.

## Examples

Below are examples of proper environment variable configuration for a few popular frameworks:

## Troubleshooting

### Use --summarize

[The `--summarize` flag](../reference/run.md#--summarize) can be added to your `turbo run` command to produce a JSON file summarizing data about your task. Checking the diff for the `globalEnv` and `env` key can help you identify any environment variables that may be missing from your configuration.

## Next steps

Once you've accounted for your environment variables, you're ready to start building the CI pipelines that build, check, and deploy your applications, at the speed of `turbo`.
