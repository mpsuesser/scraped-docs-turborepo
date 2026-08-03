---
url: https://turborepo.dev/docs/guides/publishing-libraries
title: "Publishing libraries"
description: "Bundle, version, and publish packages from your monorepo to npm using tsup and Changesets."
access_date: 2026-08-03T18:56:55.656Z
current_date: 2026-08-03T18:56:55.656Z
---

Learn how to publish libraries to the npm registry from a monorepo.

Publishing a package to the npm registry from a monorepo can be a smooth experience, with the right tools.

While this guide cannot solve for every possible compiling, bundling, and publishing configuration needed for robust packages, it will explain some of the basics.

## Bundling

Unlike [Internal Packages](../crafting-your-repository/creating-an-internal-package.md), external packages can be deployed to [npm](https://www.npmjs.com/) *and* used locally. In this guide, we'll bundle a package to both [ECMAScript modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) (`esm`) and [CommonJS modules](https://en.wikipedia.org/wiki/CommonJS) (`cjs`), the most commonly used formats on npm.

## Setting up a build script

Let's start with a package created using the [Internal Packages](../crafting-your-repository/creating-an-internal-package.md) tutorial.

There, we created a `@repo/math` package which contained a few helper functions for adding and subtracting numbers. We've decided that this package is good enough for npm, so we're going to bundle it.

We're going to add a `build` script to `@repo/math`, using a bundler. If you're unsure which one to choose, we recommend [`tsup`](https://tsup.egoist.dev/).

Install `tsup` inside the `./packages/math` package using your package manager and then create a build script for it:

```
{
  "scripts": {
    "build": "tsup src/index.ts --format cjs,esm --dts"
  }
}
```

`tsup` outputs files to the `dist` directory by default, so you should:

1. Add `dist` to your `.gitignore` files to make sure they aren't committed to source control.
2. Add `dist` to the outputs of `build` in your `turbo.json`.

```
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

That way, when `tsup` is run the outputs can be [cached](../crafting-your-repository/caching.md) by Turborepo.

Finally, we should update our package entrypoints. Inside `package.json`, change `main` to point at `./dist/index.js` for clients using CommonJS modules (`cjs`), `module` to point at `./dist/index.mjs` for clients using ECMAScript modules (`esm`), and `types` to the type definition file - `./dist/index.d.ts`:

```
{
  "main": "./dist/index.js",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts"
}
```

### Building our package before our app

Before we can run `turbo run build`, there's one thing we need to consider. We've just added a [task dependency](../crafting-your-repository/running-tasks.md) into our monorepo. The `build` of `packages/math` needs to run **before** the `build` of `apps/web`.

Fortunately, we can use [`dependsOn`](../reference/configuration.md#dependson) to easily configure this.

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

Now, we can run `turbo run build`, and it'll automatically build our packages *before* it builds our app.

### Setting up a dev script

There's a small issue with our setup. We are building our package just fine, but it's not working great in dev. Changes that we make to our `@repo/math` package aren't being reflected in our app.

That's because we don't have a `dev` script to rebuild our packages while we're working. We can add one easily:

```
{
  "scripts": {
    "build": "tsup src/index.ts --format cjs,esm --dts",
    "dev": "tsup src/index.ts --format cjs,esm --dts --watch"
  }
}
```

This passes the `--watch` flag to `tsup`, meaning it will watch for file changes.

If we've already set up [dev scripts](../crafting-your-repository/developing-applications.md#configuring-development-tasks) in our `turbo.json`, running `turbo run dev` will run our `packages/math` dev task in parallel with our `apps/web` dev task.

Our package is now in a spot where we can consider deploying to npm. In our [versioning and publishing](#versioning-and-publishing) section, we'll do just that.

## Versioning and publishing

Manually versioning and publishing packages in a monorepo can be tiresome. Luckily, there's a tool that makes things easy - the [Changesets](https://github.com/changesets/changesets) CLI.

We recommend Changesets because it's intuitive to use, and - just like Turborepo - fits with the monorepo tools you're already used to.

Some alternatives are:

- [intuit/auto](https://github.com/intuit/auto) - Generate releases based on semantic version labels on pull requests
- [microsoft/beachball](https://github.com/microsoft/beachball) - The Sunniest Semantic Version Bumper

## Publishing

Once your package has been bundled, you can then publish it to the npm registry.

We recommend taking a look at the Changesets docs. Here's our recommended reading order:

1. [Why use changesets?](https://github.com/changesets/changesets/blob/main/docs/intro-to-using-changesets.md) - an intro that takes you through the fundamentals.
2. [Installation instructions](https://github.com/changesets/changesets/blob/main/packages/cli/README.md)
3. If you're using GitHub, consider using the [Changeset GitHub bot](https://github.com/apps/changeset-bot) - a bot to nudge you to add changesets to PR's.
4. You should also consider adding the [Changesets GitHub action](https://github.com/changesets/action) - a tool which makes publishing extremely easy.

## Using Changesets with Turborepo

Once you've started using Changesets, you'll gain access to three useful commands:

```
# Add a new changeset
changeset

# Create new versions of packages
changeset version

# Publish all changed packages to npm
changeset publish
```

Linking your publishing flow into Turborepo can make organizing your deploy a lot simpler and faster.

Our recommendation is to configure Changesets to automatically commit `changeset version` 's changes

```
{
  // …
  "commit": true
  // …
}
```

and add a `publish-packages` script into your root `package.json`:

```
{
  "scripts": {
    // Include build, lint, test - all the things you need to run
    // before publishing
    "publish-packages": "turbo run build lint test && changeset version && changeset publish"
  }
}
```

If your packages are public, set Changeset's `access` to `public`:

```
{
  // …
  "access": "public"
  // …
}
```

This means that when you run `publish-packages`, your monorepo gets built, linted, tested and published - and you benefit from all of Turborepo's speedups.
