---
url: https://turborepo.dev/docs/guides/tools/storybook
title: "Storybook"
description: "Learn how to use Storybook in a Turborepo."
access_date: 2026-10-16T19:42:51.265Z
current_date: 2026-10-16T19:42:51.265Z
---

> For an index of all documentation, see [/llms.txt](https://turborepo.dev/llms.txt).

[Storybook](https://storybook.js.org/) is a popular way to build UI components in an isolated environment. By putting Storybook into your Turborepo, you can easily develop your design system right alongside your applications.

## Quickstart

If you'd rather use a template, this guide is walking through how to build [this Storybook/Turborepo template](https://vercel.com/templates/react/turborepo-design-system) on Vercel.

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest -e design-system
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest -e design-system
```

#### npm

```bash title="Terminal"
npx create-turbo@latest -e design-system
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest -e design-system
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest -e design-system
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest -e design-system
```

## Guide

### 1. Create a monorepo

If you don't have an existing project, use [create-turbo](../../getting-started/installation.md) to create a new monorepo:

#### pnpm

```bash title="Terminal"
pnpm dlx create-turbo@latest
```

#### yarn

```bash title="Terminal"
yarn dlx create-turbo@latest
```

#### npm

```bash title="Terminal"
npx create-turbo@latest
```

#### bun

```bash title="Terminal"
bunx create-turbo@latest
```

#### nub

```bash title="Terminal"
nubx create-turbo@latest
```

#### aube

```bash title="Terminal"
aubx create-turbo@latest
```

### 2. Create a directory for the app

You'll need a directory for the Storybook application:

```bash title="Terminal"
mkdir apps/storybook
cd apps/storybook
```

### 3. Add the Storybook application

In the `apps/storybook` directory, initialize a new Storybook application:

#### pnpm

```bash title="Terminal"
pnpm create storybook@latest
```

#### yarn

```bash title="Terminal"
yarn create storybook@latest
```

#### npm

```bash title="Terminal"
npm create storybook@latest
```

#### bun

```bash title="Terminal"
bun create storybook@latest
```

#### nub

```bash title="Terminal"
nubx create-storybook@latest
```

#### aube

```bash title="Terminal"
aubx create-storybook@latest
```

Follow the prompts to create an application. For the rest of this guide, we'll assume React and TypeScript.

> After going through Storybook's onboarding, you can [uninstall the onboarding
> addon](https://github.com/storybookjs/addon-onboarding/blob/main/README.md).

### 4. Add your UI kit to Storybook

Now, install your UI package into Storybook.

#### pnpm

```bash title="Terminal"
pnpm add @repo/ui --filter=storybook
```

#### yarn

```bash title="Terminal"
yarn workspace storybook add @repo/ui
```

#### npm

```bash title="Terminal"
npm install @repo/ui --workspace=storybook
```

#### bun

```bash title="Terminal"
cd apps/storybook && bun install @repo/ui
```

#### nub

```bash title="Terminal"
cd apps/storybook && nub add @repo/ui
```

#### aube

```bash title="Terminal"
cd apps/storybook && aube add @repo/ui
```

### 5. Set up a story for your Button component

Delete the stories and components found in `src/stories` that were created by the Storybook scaffolding tool. You will be making your own.

As an example, here is a story for the `Button` component from `@repo/ui/button`.

```tsx title="./apps/storybook/src/stories/Button.stories.tsx"
import type { Meta, StoryObj } from "@storybook/react";
import { Button } from "@repo/ui/button";

const meta = {
  title: "Example/Button",
  component: Button,
  tags: ["autodocs"],
} satisfies Meta<typeof Button>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Primary: Story = {
  args: {
    appName: "Button",
    children: "I am a primary button.",
  },
};
```

### 6. Align scripts to your tasks

Last, integrate the new Storybook application into your Turborepo:

```json title="apps/storybook/package.json"
{
  "scripts": {
    "dev": "storybook dev -p 6006", // [!code highlight]
    "build": "storybook build" // [!code highlight]
  }
}
```

These scripts will now run with the `turbo dev` and `turbo build` tasks in your `turbo.json`.

To ensure file outputs are cached when you run `build`, add `storybook-static` to the outputs of your `turbo.json` build task:

```diff title="turbo.json"
{
  "tasks": {
    "build": {
      "outputs": [
        ".next/**",
        "!.next/cache/**",
        "!.next/dev/**",
+       "storybook-static/**"
      ]
    }
  }
}
```

### Add Storybook build outputs to `.gitignore`

Ensure that the build outputs for Storybook are not committed to source control

```diff title=".gitignore"
+ storybook-static
```

### Verify your configuration

Run `turbo build` to build the Storybook application alongside the rest of your applications.

You can also run `turbo build` again to see cache hits for your builds.

## More tips

### Co-locating stories

If you'd prefer to co-locate your stories to their source code (rather than having them in the Storybook application), you'll need some extra configuration.

#### 1. Re-configure Storybook sources

In `.storybook/main.ts`, change the `stories` paths in `config` to the directories you'd like to capture. For instance, if you'd like to write stories in the UI package:

```diff title="./apps/storybook/.storybook/main.ts"

const config = {
  stories: [
-   "../src/**/*.mdx",
-   "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
+   "../../../packages/ui/src/**/*.stories.@(js|jsx|mjs|ts|tsx)",
};
```

#### 2. Move story files to the UI package

Following along with [the guide above](#set-up-a-story-for-your-button-component), move the `./apps/storybook/src/stories/Button.stories.tsx` file to `./packages/ui/src/Button.stories.tsx`.

Update components imports so that they reference the now co-located modules. For instance, in the story's imports:

```diff title="./packages/ui/src/Button.stories.tsx"
- import { Button } from "@repo/ui/button";
+ import { Button } from "./button";
```

> You may also need to update [absolute
> imports](typescript.md#use-nodejs-subpath-imports-instead-of-typescript-compiler-paths)
> according to your changes and usage.

You'll also need to install any Storybook packages required for writing stories. For example, moving the story from above would require that you install `@storybook/react` into your `@repo/ui` package.

#### pnpm

```bash title="Terminal"
pnpm add @storybook/react --filter=@repo/ui --save-dev
```

#### yarn

```bash title="Terminal"
yarn workspace @repo/ui add @storybook/react --dev
```

#### npm

```bash title="Terminal"
npm install @storybook/react --workspace=@repo/ui --save-dev
```

#### bun

```bash title="Terminal"
cd packages/ui && bun install @storybook/react --dev
```

#### nub

```bash title="Terminal"
cd packages/ui && nub add @storybook/react --save-dev
```

#### aube

```bash title="Terminal"
cd packages/ui && aube add @storybook/react --save-dev
```

#### 3. Configure caching

Because stories are now in the UI package, changes to those stories can cause cache misses for any builds that depend on your UI package. However, changing a story doesn't mean your production applications should miss cache.

To prevent this, exclude stories from the inputs to your `build` task in your root `turbo.json`. You'll also need to create a `build:storybook` task, which you'll need in a moment:

```json title="./turbo.json"
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["$TURBO_DEFAULT$", "!**/*.stories.{tsx,jsx,mdx}"], // [!code highlight]
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"]
    },
    "build:storybook": {} // [!code highlight]
  }
}
```

Additionally, create a [Package Configuration](../../reference/package-configurations.md) in the `storybook` application so stories are **accounted for in building the Storybook application, specifically**:

```json title="./apps/storybook/turbo.json"
{
  "extends": ["//"],
  "tasks": {
    "build:storybook": {
      "dependsOn": ["^build:storybook"],
      "outputs": ["storybook-static/**"]
    }
  }
}
```

> If you are using the [Compiled Package
> pattern](../../core-concepts/internal-packages.md#compiled-packages), you may
> also need to add `^build` to your `dependsOn`.

#### 4. Rename the build script

Last, make sure your script to build Storybook uses the configuration we just wrote by renaming it to the name of the task:

```json title="apps/storybook/package.json"
{
  "scripts": {
    "dev": "storybook dev -p 6006",
    "build:storybook": "storybook build" // [!code highlight]
  }
}
```

The script that was once `"build"` is now `"build:storybook"` to ensure the stories are included in hashes for caching.

#### 5. Verify your configuration

To ensure your setup is correct:

1. Run `turbo build:storybook build`. You should see cache misses.
2. Run `turbo build:storybook build` again. You should see all cache hits.
3. Make a code change **to a story** in your `@repo/ui` package.
4. Run `turbo build:storybook build` again. You should **only** see a cache miss for the Storybook application. All others should hit cache.

### Adding CSS

If your UI package exports its own CSS, you'll need to add it to the renders in the Storybook app, similar to how you would add it to your applications. [The Storybook documentation](https://storybook.js.org/docs/configure/styling-and-css#css) recommends you add it to the `.storybook/preview.ts` file.

---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)
