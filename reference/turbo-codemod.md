---
url: https://turborepo.dev/docs/reference/turbo-codemod
title: "@turbo/codemod"
description: "Learn more about how Turborepo uses codemods to make version migrations easy."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

Turborepo provides codemod transformations and automatic migration scripts to help upgrade your Turborepo codebase when a feature is deprecated.

Codemods are transformations that run on your codebase programmatically. This allows for a large amount of changes to be applied without having to manually manage repetitive changes.

## Usage

First, ensure that you've run your package manager's install command.

```bash title="Terminal"
npx @turbo/codemod [transform] [path] [--dry] [--print]
```

- `transform` - name of transform, see available transforms below.
- `path` - files or directory to transform
- `--dry` - Do a dry-run (Code will not be edited)
- `--print` - Prints the changed output for comparison

## Upgrading Turborepo versions

In most cases, you can run:

```bash title="Terminal"
npx @turbo/codemod migrate
```

All the codemods you need to upgrade will be run for you.

### Turborepo 2.x

The codemods below are used for migration paths in the second major version of Turborepo.

#### update-versioned-schema-json (2.7.5)

Updates the `$schema` URL in `turbo.json` files to use the versioned subdomain format. This applies to both root and workspace `turbo.json` files.

```bash title="Terminal"
npx @turbo/codemod update-versioned-schema-json
```

**Example**

```diff title="./turbo.json"
{
- "$schema": "https://turborepo.dev/schema.json",
+ "$schema": "https://v2-7-5.turborepo.dev/schema.json",
}
```

#### update-schema-json-url (2.0.0)

Updates a versioned schema.json URL to v2.

```bash title="Terminal"
npx @turbo/codemod update-schema-json-url
```

**Example**

```diff title="./turbo.json"
{
- "$schema": "https://turborepo.dev/schema.v1.json",
+ "$schema": "https://turborepo.dev/schema.v2.json",
}
```

#### add-package-names (2.0.0)

Adds a name to `package.json` in any packages that don't have one.

```bash title="Terminal"
npx @turbo/codemod add-package-names
```

**Example**

```diff title="./packages/ui/package.json"
{
+ "name": "@repo/ui",
}
```

#### clean-globs (2.0.0)

Fix glob patterns that are invalid due to changes in processing of globs in `turbo`.

```bash title="Terminal"
npx @turbo/codemod clean-globs
```

**Example**

```diff title="./turbo.json"
{
  "tasks": {
    "build": {
      "outputs": [
        // Collapse back-to-back doublestars
-         "**/**.ext", // [!code highlight]
+         "**.ext" // [!code highlight]
        // Ensure a file extension does not have a double-star
-         "**.ext", // [!code highlight]
+         "**/*.ext" // [!code highlight]
        // Proper expansion of directory names
-         "prefix**/", // [!code highlight]
+         "prefix*/**" // [!code highlight]
      ]
    }
  }
}
```

#### migrate-dot-env (2.0.0)

Move `.env` files from the removed `dotEnv` key to `inputs`.

```bash title="Terminal"
npx @turbo/codemod migrate-dot-env
```

**Example**

```diff title="./turbo.json"
{
  "tasks": {
    "build": {
-       "dotEnv": [".env"], // [!code highlight]
      "inputs": [
        "dist/**",
+         ".env" // [!code highlight]
      ],
    }
  }
}
```

#### rename-output-mode (2.0.0)

Rename the `outputMode` key to `outputLogs`.

```bash title="Terminal"
npx @turbo/codemod rename-output-mode
```

**Example**

```diff title="./turbo.json"
{
  "tasks": {
    "build": {
-     "outputMode": "errors-only" // [!code highlight]
+     "outputLogs": "errors-only" // [!code highlight]
    }
  }
}
```

#### rename-pipeline (2.0.0)

Rename the `pipeline` key to `tasks`.

```bash title="Terminal"
npx @turbo/codemod rename-pipeline
```

**Example**

```diff title="./turbo.json"
{
- "pipeline": {
+ "tasks": {
    "build": {
      ...
    },
    "dev": {
      ...
    },
    "lint": {
      ...
    }
  }
}
```

#### stabilize-ui (2.0.0)

Renames the `experimentalUI` key in `turbo.json` to `ui`.

```bash title="Terminal"
npx @turbo/codemod stabilize-ui
```

**Example**

```diff title="./turbo.json"
{
- "experimentalUI": true
+ "ui": true
}
```

### Turborepo 1.x

The codemods below are used for migration paths in the first major version of Turborepo.

#### stabilize-env-mode (1.10.0)

Migrates `turbo.json`'s `experimentalGlobalPassThroughEnv` to `globalPassThroughEnv` and `experimentalPassThroughEnv` to `passThroughEnv`.

```bash title="Terminal"
npx @turbo/codemod stabilize-env-mode
```

**Example**

```diff title="./turbo.json"
{
  "$schema": "https://turborepo.dev/schema.json",
- "experimentalGlobalPassThroughEnv": ["CC"],
+ "globalPassThroughEnv": ["CC"],
  "pipeline": {
    "build": {
-     "experimentalPassThroughEnv": ["GOROOT"],
+     "passThroughEnv": ["GOROOT"],
    }
  }
}
```

#### transform-env-literals-to-wildcards (1.10.0)

Updates any existing environment variable fields whose contents would be ambiguous to the new wildcard syntax.

```bash title="Terminal"
npx @turbo/codemod transform-env-literals-to-wildcards
```

**Example**

```diff title="./turbo.json"
{
  "$schema": "https://turborepo.dev/schema.json",
- "globalEnv": ["THIS_*_IS_LITERAL"],
- "globalPassThroughEnv": ["!LITERAL_LEADING_EXCLAMATION"],
+  "globalEnv": ["THIS_\\*_IS_LITERAL"],
+  "globalPassThroughEnv": ["\\!LITERAL_LEADING_EXCLAMATION"],
  "pipeline": {
    "build": {
-     "env": ["50_PERCENT_OFF*_HAS_SMALL_PRINT"],
-     "passThroughEnv": ["**BOLDED**"],
+     "env": ["50_PERCENT_OFF\\*_HAS_SMALL_PRINT"],
+     "passThroughEnv": ["\\*\\*BOLDED\\*\\*"],
    }
  }
}
```

#### set-default-outputs (1.7.0)

Migrates `turbo.json` outputs to include the previously inferred `dist/**` and `build/**`.

```bash title="Terminal"
npx @turbo/codemod set-default-outputs
```

**Example**

```diff title="turbo.json"
{
  "$schema": "https://turborepo.dev/schema.json",
  "globalDependencies": [".env"],
  "globalEnv": ["CI_ENV"],
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "env": ["API_BASE"],
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"]
    },
-   "lint": {
-     "outputs": []
-    },
+   "lint": {},
    "dev": {
      "cache": false,
      "persistent": true,
+     "outputs": ["dist/**", "build/**"]
    }
  }
}

```

#### migrate-env-var-dependencies (1.5.0)

Migrates all environment variable dependencies in `turbo.json` from `dependsOn` and `globalDependencies` to `env` and `globalEnv` respectively.

```bash title="Terminal"
npx @turbo/codemod migrate-env-var-dependencies
```

**Example**

```diff title="./turbo.json"
// After, turbo.json
{
  "$schema": "https://turborepo.dev/schema.json",
- "globalDependencies": [".env", "$CI_ENV"],
+ "globalDependencies": [".env"],
+ "globalEnv": ["CI_ENV"],
  "pipeline": {
    "build": {
-     "dependsOn": ["^build", "$API_BASE"],
+     "dependsOn": ["^build"],
+     "env": ["API_BASE"],
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"],
    },
    "lint": {},
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

#### add-package-manager (1.1.0)

Transforms the root `package.json` so that `devEngines.packageManager` declares the detected package manager (`yarn`, `npm`, `pnpm`, `bun`) and version. Turborepo uses this declaration for faster package manager detection, rather than inferring from the filesystem alone.

```bash title="Terminal"
npx @turbo/codemod add-package-manager
```

**Example**

```diff title="./package.json"
{
  "name": "turborepo-basic",
  "version": "0.0.0",
  "private": true,
+  "devEngines": {
+    "packageManager": {
+      "name": "yarn",
+      "version": "1.22.17"
+    }
+  },
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

#### create-turbo-config (1.1.0)

Creates the `turbo.json` file at the root of your project based on the `"turbo"` key in `package.json`.
The `"turbo"` key is subsequently deleted from `package.json`.

```bash title="Terminal"
npx @turbo/codemod create-turbo-config
```

**Example**

#### package.json

```diff title="./package.json"
// After, package.json
{
  "name": "@acme/workspace",
  "private": true,
- "turbo": {
-   "pipeline": {
-     ...
-   }
- },
}
```

#### turbo.json

```diff title="./turbo.json"
+ {
+   "$schema": "https://turborepo.dev/schema.json",
+   "pipeline": {
+     ...
+   }
+ }
```

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
