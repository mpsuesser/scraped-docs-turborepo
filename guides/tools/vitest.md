---
url: https://turborepo.dev/docs/guides/tools/vitest
title: "Vitest"
description: "Configure Vitest for cached, parallelized testing across monorepo packages with optional merged coverage."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

Learn how to use Vitest in a monorepo.

[Vitest](https://vitest.dev/) is a test runner from the Vite ecosystem. Integrating it with Turborepo will lead to enormous speed-ups.

[The Vitest documentation](https://vitest.dev/guide/workspace) shows how to create a "Vitest Projects" configuration that runs all tests in the monorepo from one root command, enabling behavior like merged coverage reports out-of-the-box. This feature doesn't follow modern best practices for monorepos, since its designed for compatibility with Jest (whose Workspace feature was built before [package manager Workspaces](../../crafting-your-repository/structuring-a-repository.md)).

Because of this you have two options, each with their own tradeoffs:

- [Leveraging Turborepo for caching](#leveraging-turborepo-for-caching)
- [Using Vitest's Projects feature](#using-vitests-projects-feature)

### Leveraging Turborepo for caching

To improve on cache hit rates and only run tests with changes, you can choose to configure tasks per-package, splitting up the Vitest command into separate, cacheable scripts in each package. This speed comes with the tradeoff that you'll need to create merged coverage reports yourself.

#### Setting up

Let's say we have a simple [package manager Workspace](../../crafting-your-repository/structuring-a-repository.md) that looks like this:

package.json

package.json

Both `apps/web` and `packages/ui` have their own test suites, with `vitest` [installed into the packages that use them](../../crafting-your-repository/managing-dependencies.md#install-dependencies-where-theyre-used). Their `package.json` files include a `test` script that runs Vitest:

```
{
  "scripts": {
    "test": "vitest run"
  },
  "devDependencies": {
    "vitest": "latest"
  }
}
```

Inside the root `turbo.json`, create a `test` task:

```
{
  "tasks": {
    "test": {
      "dependsOn": ["transit"]
    },
    "transit": {
      "dependsOn": ["^transit"]
    }
  }
}
```

Now, `turbo run test` can parallelize and cache all of the test suites from each package, only testing code that has changed.

#### Running tests in watch mode

When you run your test suite in CI, it logs results and eventually exits upon completion. This means you can [cache it with Turborepo](../../crafting-your-repository/caching.md). But when you run your tests using Vitest's watch mode during development, the process never exits. This makes a watch task more like a [long-running, development task](../../crafting-your-repository/developing-applications.md).

Because of this difference, we recommend specifying **two separate Turborepo tasks**: one for running your tests, and one for running them in watch mode.

For example, inside the `package.json` file for each workspace:

```
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest --watch"
  }
}
```

And, inside the root `turbo.json`:

```
{
  "tasks": {
    "test": {
      "dependsOn": ["^test"]
    },
    "test:watch": {
      "cache": false,
      "persistent": true
    }
  }
}
```

You can now run your tasks using [global `turbo`](../../getting-started/installation.md#global-installation) as `turbo run test:watch` or from a script in your root `package.json`:

#### Global turbo

```
turbo run test

turbo run test:watch
```

#### ./package.json

```
{
  "scripts": {
    "test": "turbo run test",
    "test:watch": "turbo run test:watch"
  }
}
```

#### Creating merged coverage reports

[Vitest's Projects feature](#using-vitests-projects-feature) creates an out-of-the-box coverage report that merges all of your packages' tests coverage reports. Following the Turborepo strategy, though, you'll have to merge the coverage reports yourself.

To do this with Vitest's native reporter merging, you'll follow a few general steps:

1. Configure package-level Vitest runs to write blob reports.
2. Run `turbo run test` to create or restore each package's cached blob report.
3. Stage the package-local blob files into one flat directory.
4. Merge the blob reports with [`vitest --merge-reports`](https://vitest.dev/guide/cli.html#merge-reports).

Start by enabling the `blob` reporter in your shared Vitest configuration:

```
export const sharedConfig = {
  test: {
    reporters: ["default", "blob"],
    outputFile: {
      blob: "coverage/blob/report.json",
    },
    coverage: {
      provider: "istanbul" as const,
      enabled: true,
    },
  },
};
```

Because the blob output path is package-relative, each package's `test` task owns only its own `coverage/blob/**` output. This lets Turborepo restore package coverage blobs independently from cache.

Then configure Turborepo's task outputs:

```
{
  "tasks": {
    "test": {
      "dependsOn": ["transit", "@repo/vitest-config#build"],
      "outputs": ["coverage/blob/**"]
    },
    "transit": {
      "dependsOn": ["^transit"]
    }
  }
}
```

Vitest's `--merge-reports` command expects all blob files to be direct children of the directory passed to the flag. If your blobs are package-local for caching, add a small script that copies them into a flat staging directory before invoking Vitest:

```
{
  "scripts": {
    "merge-blob-reports": "node dist/scripts/merge-blob-reports.js",
    "report": "vitest --merge-reports coverage/merged-blob --passWithNoTests --coverage --coverage.provider=istanbul --coverage.reporter=html --coverage.reporter=text --coverage.reportsDirectory=coverage/report"
  }
}
```

The report package can then cache the staging step separately from the final coverage report:

```
{
  "extends": ["//"],
  "tasks": {
    "merge-blob-reports": {
      "dependsOn": ["build"],
      "inputs": [
        "$TURBO_DEFAULT$",
        "$TURBO_ROOT$/apps/*/coverage/blob/**",
        "$TURBO_ROOT$/packages/*/coverage/blob/**"
      ],
      "outputs": ["coverage/merged-blob/**"]
    },
    "report": {
      "dependsOn": ["merge-blob-reports"],
      "inputs": [
        "$TURBO_DEFAULT$",
        "$TURBO_ROOT$/apps/*/coverage/blob/**",
        "$TURBO_ROOT$/packages/*/coverage/blob/**"
      ],
      "outputs": ["coverage/report/**"]
    }
  }
}
```

With this in place, use a root script that runs tests before the report task:

```
{
  "scripts": {
    "report": "turbo run test && turbo run report"
  }
}
```

### Using Vitest's Projects feature

The Vitest Projects feature doesn't follow the same model as a [package manager Workspace](../../crafting-your-repository/structuring-a-repository.md). Instead, it uses a root configuration that discovers and runs tests across multiple projects from a single command.

#### Root configuration behavior

When using projects, the root `vitest.config.ts` serves as the entry point that defines which projects to include via the `projects` array. Each project can have its own configuration that extends or overrides the root settings:

```
import { defineConfig } from "vitest/config";

export default defineConfig({
  test: {
    // Root-level config applies to ALL projects
    globals: true,
    environment: "jsdom",
  },
  projects: [
    {
      name: "web",
      root: "./apps/web",
      test: {
        // Project-specific config
        include: ["src/**/*.test.ts"],
      },
    },
    {
      name: "ui",
      root: "./packages/ui",
      test: {
        include: ["src/**/*.spec.ts"],
      },
    },
  ],
});
```

**Important:** Project-level configs in the `projects` array cannot extend the root config's `test` object directly. Instead, share configuration by:

1. Creating a shared configuration file that projects import
2. Using TypeScript to export a config object that projects spread into their settings

In this model, there aren't package boundaries, from a modern JavaScript ecosystem perspective. This means you can't rely on Turborepo's caching, since Turborepo leans on those package boundaries.

Because of this, you'll need to use [Root Tasks](../../crafting-your-repository/configuring-tasks.md#registering-root-tasks) if you want to run the tests using Turborepo. Once you've configured [a Vitest Projects setup](https://vitest.dev/guide/workspace), create the Root Tasks for Turborepo:

```
{
  "tasks": {
    "//#test": {
      "outputs": ["coverage/**"]
    },
    "//#test:watch": {
      "cache": false,
      "persistent": true
    }
  }
}
```

**Notably, the file inputs for a Root Task include all packages by default, so any change in any package will result in a cache miss.** While this does make for a simplified configuration to create merged coverage reports, you'll be missing out on opportunities to cache repeated work.

#### Running specific projects

When using Vitest's projects feature, you can run tests for specific projects using the `--project` (or `-p`) CLI flag:

```
# Run tests for a specific project
vitest run --project=web

# Run tests for multiple projects
vitest run --project=web --project=ui

# Watch mode for a specific project
vitest --watch --project=web
```

This is useful when you want to run tests for only certain packages without affecting the entire monorepo's test suite.

### Using a hybrid approach

You can combine the benefits of both approaches by implementing a hybrid solution. This approach unifies local development using Vitest's Projects feature while preserving Turborepo's caching in CI. This comes at the tradeoff of slightly more configuration and a mixed task running model in the repository.

First, create a shared configuration package since individual projects can't extend the root config when using projects. Create a new package for your shared Vitest configuration:

```
{
  "name": "@repo/vitest-config",
  "version": "0.0.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch"
  },
  "dependencies": {
    "vitest": "latest"
  },
  "devDependencies": {
    "@repo/typescript-config": "workspace:*",
    "typescript": "latest"
  }
}
```

```
{
  "extends": "@repo/typescript-config/base.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"],
  "exclude": ["dist", "node_modules"]
}
```

```
export const sharedConfig = {
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: ["./src/test/setup.ts"],
    // Other shared configuration
  },
};
```

Then, create your root Vitest configuration using projects:

```
import { defineConfig } from "vitest/config";
import { sharedConfig } from "@repo/vitest-config";

export default defineConfig({
  ...sharedConfig,
  projects: [
    {
      name: "packages",
      root: "./packages/*",
      test: {
        ...sharedConfig.test,
        // Project-specific configuration
      },
    },
  ],
});
```

In this setup, your packages maintain their individual Vitest configurations that import the shared config. First, install the shared config package:

```
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest --watch"
  },
  "devDependencies": {
    "@repo/vitest-config": "workspace:*",
    "vitest": "latest"
  }
}
```

Then create the Vitest configuration:

```
import { defineConfig } from "vitest/config";
import { sharedConfig } from "@repo/vitest-config";

export default defineConfig({
  ...sharedConfig,
  test: {
    ...sharedConfig.test,
    // Package-specific overrides if needed
  },
});
```

Make sure to update your `turbo.json` to include the new configuration package in the dependency graph:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["^test", "@repo/vitest-config#build"]
    },
    "test:watch": {
      "cache": false,
      "persistent": true
    }
  }
}
```

While your root `package.json` includes scripts for running tests globally:

```
{
  "scripts": {
    "test:projects": "vitest run",
    "test:projects:watch": "vitest --watch"
  }
}
```

This configuration allows developers to run `pnpm test:projects` or `pnpm test:projects:watch` at the root for a seamless local development experience using Vitest projects, while CI continues to use `turbo run test` to leverage package-level caching. **You'll still need to handle merged coverage reports manually as described in the previous section**.
