---
url: https://turborepo.dev/docs/guides/tools/storybook
title: "Storybook"
description: "Set up Storybook for developing and documenting UI components in your monorepo."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

Learn how to use Storybook in a Turborepo.

[Storybook](https://storybook.js.org/) is a popular way to build UI components in an isolated environment. By putting Storybook into your Turborepo, you can easily develop your design system right alongside your applications.

## Quickstart

If you'd rather use a template, this guide is walking through how to build [this Storybook/Turborepo template](https://vercel.com/templates/react/turborepo-design-system) on Vercel.

#### pnpm

```
pnpm dlx create-turbo@latest -e design-system
```

#### yarn

```
yarn dlx create-turbo@latest -e design-system
```

#### npm

```
npx create-turbo@latest -e design-system
```

#### bun

```
bunx create-turbo@latest -e design-system
```

## Guide

### Create a monorepo

If you don't have an existing project, use [create-turbo](../../getting-started/installation.md) to create a new monorepo:

#### pnpm

```
pnpm dlx create-turbo@latest
```

#### yarn

```
yarn dlx create-turbo@latest
```

#### npm

```
npx create-turbo@latest
```

#### bun

```
bunx create-turbo@latest
```

### Create a directory for the app

You'll need a directory for the Storybook application:

```
mkdir apps/storybook
cd apps/storybook
```

### Add the Storybook application

In the `apps/storybook` directory, initialize a new Storybook application:

#### pnpm

```
pnpm create storybook@latest
```

#### yarn

```
yarn create storybook@latest
```

#### npm

```
npm create storybook@latest
```

#### bun

```
bun create storybook@latest
```

Follow the prompts to create an application. For the rest of this guide, we'll assume React and TypeScript.

### Add your UI kit to Storybook

Now, install your UI package into Storybook.

#### pnpm

```
pnpm add @repo/ui --filter=storybook
```

#### yarn

```
yarn workspace storybook add @repo/ui
```

#### npm

```
npm install @repo/ui --workspace=storybook
```

#### bun

```
cd apps/storybook && bun install @repo/ui
```

### Set up a story for your Button component

Delete the stories and components found in `src/stories` that were created by the Storybook scaffolding tool. You will be making your own.

As an example, here is a story for the `Button` component from `@repo/ui/button`.

```
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

### Align scripts to your tasks

Last, integrate the new Storybook application into your Turborepo:

```
{
  "scripts": {
    "dev": "storybook dev -p 6006",
    "build": "storybook build"
  }
}
```

These scripts will now run with the `turbo dev` and `turbo build` tasks in your `turbo.json`.

To ensure file outputs are cached when you run `build`, add `storybook-static` to the outputs of your `turbo.json` build task:

```
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

### Add Storybook build outputs to.gitignore

Ensure that the build outputs for Storybook are not committed to source control

```
+ storybook-static
```

### Verify your configuration

Run `turbo build` to build the Storybook application alongside the rest of your applications.

You can also run `turbo build` again to see cache hits for your builds.

## More tips

### Co-locating stories

If you'd prefer to co-locate your stories to their source code (rather than having them in the Storybook application), you'll need some extra configuration.

#### Re-configure Storybook sources

In `.storybook/main.ts`, change the `stories` paths in `config` to the directories you'd like to capture. For instance, if you'd like to write stories in the UI package:

```
const config = {
  stories: [
-   "../src/**/*.mdx",
-   "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
+   "../../../packages/ui/src/**/*.stories.@(js|jsx|mjs|ts|tsx)",
};
```

#### Move story files to the UI package

Following along with [the guide above](#set-up-a-story-for-your-button-component), move the `./apps/storybook/src/stories/Button.stories.tsx` file to `./packages/ui/src/Button.stories.tsx`.

Update components imports so that they reference the now co-located modules. For instance, in the story's imports:

```
- import { Button } from "@repo/ui/button";
+ import { Button } from "./button";
```

You'll also need to install any Storybook packages required for writing stories. For example, moving the story from above would require that you install `@storybook/react` into your `@repo/ui` package.

#### pnpm

```
pnpm add @storybook/react --filter=@repo/ui --save-dev
```

#### yarn

```
yarn workspace @repo/ui add @storybook/react --dev
```

#### npm

```
npm install @storybook/react --workspace=@repo/ui --save-dev
```

#### bun

```
cd packages/ui && bun install @storybook/react --dev
```

#### Configure caching

Because stories are now in the UI package, changes to those stories can cause cache misses for any builds that depend on your UI package. However, changing a story doesn't mean your production applications should miss cache.

To prevent this, exclude stories from the inputs to your `build` task in your root `turbo.json`. You'll also need to create a `build:storybook` task, which you'll need in a moment:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["$TURBO_DEFAULT$", "!**/*.stories.{tsx,jsx,mdx}"],
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"]
    },
    "build:storybook": {}
  }
}
```

Additionally, create a [Package Configuration](../../reference/package-configurations.md) in the `storybook` application so stories are **accounted for in building the Storybook application, specifically**:

```
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

#### Rename the build script

Last, make sure your script to build Storybook uses the configuration we just wrote by renaming it to the name of the task:

```
{
  "scripts": {
    "dev": "storybook dev -p 6006",
    "build:storybook": "storybook build"
  }
}
```

The script that was once `"build"` is now `"build:storybook"` to ensure the stories are included in hashes for caching.

#### Verify your configuration

To ensure your setup is correct:

1. Run `turbo build:storybook build`. You should see cache misses.
2. Run `turbo build:storybook build` again. You should see all cache hits.
3. Make a code change **to a story** in your `@repo/ui` package.
4. Run `turbo build:storybook build` again. You should **only** see a cache miss for the Storybook application. All others should hit cache.

### Adding CSS

If your UI package exports its own CSS, you'll need to add it to the renders in the Storybook app, similar to how you would add it to your applications. [The Storybook documentation](https://storybook.js.org/docs/configure/styling-and-css#css) recommends you add it to the `.storybook/preview.ts` file.
