---
url: https://turborepo.dev/docs/crafting-your-repository/managing-dependencies
title: "Managing dependencies"
description: "Install, organize, and maintain external and internal dependencies across packages in your workspace."
access_date: 2026-08-03T18:13:51.263Z
current_date: 2026-08-03T18:13:51.263Z
---

Learn how to manage dependencies in your monorepo's workspace.

- **External dependencies** come from [the npm registry](https://www.npmjs.com/), allowing you to leverage valuable code from the ecosystem to build your applications and libraries faster.
- **Internal dependencies** let you share functionality within your repository, dramatically improving discoverability and usability of shared code. We will discuss how to build an Internal Package in [the next guide](creating-an-internal-package.md).

#### pnpm

```
{
  "dependencies": {
    "next": "latest", // External dependency
    "@repo/ui": "workspace:*" // Internal dependency
  }
}
```

#### yarn

```
{
  "dependencies": {
    "next": "latest", // External dependency
    "@repo/ui": "*" // Internal dependency
  }
}
```

#### npm

```
{
  "dependencies": {
    "next": "latest", // External dependency
    "@repo/ui": "*" // Internal dependency
  }
}
```

#### bun

```
{
  "dependencies": {
    "next": "latest", // External dependency
    "@repo/ui": "workspace:*" // Internal dependency
  }
}
```

## Best practices for dependency installation

### Install dependencies where they're used

When you install a dependency in your repository, you should install it directly in the package that uses it. The package's `package.json` will have every dependency that the package needs. This is true for both external and internal dependencies.

To quickly install dependencies in multiple packages, you can use your package manager:

#### pnpm

```
pnpm add jest --save-dev --recursive --filter=web --filter=@repo/ui --filter=docs
```

[→ pnpm documentation](https://pnpm.io/cli/recursive)

#### yarn

Yarn 1:

```
yarn workspace web add jest --dev
yarn workspace @repo/ui add jest --dev
```

[→ Yarn 1 documentation](https://classic.yarnpkg.com/en/docs/cli/add)

Yarn 2+:

```
yarn workspaces foreach -R --from '{web,@repo/ui}' add jest --dev
```

[→ Yarn 2+ documentation](https://yarnpkg.com/cli/workspaces/foreach#usage)

#### npm

```
npm install jest --workspace=web --workspace=@repo/ui --save-dev
```

[→ npm documentation](https://docs.npmjs.com/cli/v7/using-npm/config#workspace)

#### bun

```
cd apps/web && bun install jest --dev
cd packages/ui && bun install jest --dev
```

[→ bun documentation](https://bun.sh/docs/install/workspaces)

This practice has several benefits:

- **Improved clarity**: It's easier to understand what a package depends on when its dependencies are listed in its `package.json`. Developers working in the repository can see at a glance what dependencies are used within the package.
- **Enhanced flexibility**: In a monorepo at scale, it can be unrealistic to expect each package to use the same version of an external dependency. When there are many teams working in the same codebase, there will be differing priorities, timelines, and needs due to the realities of [operating at scale](https://vercel.com/blog/how-to-scale-a-large-codebase). By installing dependencies in the package that uses them, you can enable your `ui` team to bump to the latest version of TypeScript, while your `web` team can prioritize shipping new features and bumping TypeScript later. Additionally, if you still want to keep dependency versions in sync, [you can do that, too](#keeping-dependencies-on-the-same-version).
- **Better caching ability**: If you install too many dependencies in the root of your repository, you'll be changing the workspace root whenever you add, update, or delete a dependency, leading to unnecessary cache misses.
- **Pruning unused dependencies**: For Docker users, [Turborepo's pruning feature](../reference/prune.md) can remove unused dependencies from Docker images to create lighter images. When dependencies are installed in the packages that they are meant for, Turborepo can read your lockfile and remove dependencies that aren't used in the packages you need.

### Few dependencies in the root

Following the first principle above to [install dependencies in the package where they're used](#install-dependencies-where-theyre-used), you'll find that you naturally end up with few dependencies in the root of your workspace.

The only dependencies that belong in the workspace root are **tools for managing the repository** whereas dependencies for building applications and libraries are installed in their respective packages. Some examples of dependencies that make sense to install in the root are [`turbo`](https://www.npmjs.com/package/turbo), [`husky`](https://www.npmjs.com/package/husky), or [`lint-staged`](https://www.npmjs.com/package/lint-staged).

## Managing dependencies

### Turborepo does not manage dependencies

Note that Turborepo does not play a role in managing your dependencies, leaving that work up to your package manager of choice.

It's up to the package manager to handle things like downloading the right external dependency version, symlinking, and resolving modules. The recommendations on this page are best practices for managing dependencies in a Workspace, and are not enforced by Turborepo.

### Module resolution differs amongst package managers

Package managers have different module resolution algorithms, which leads to differences in behavior that can be difficult to predict.

In the Turborepo documentation, we make many recommendations according to the expected behaviors of the package managers. Our coverage of how to handle dependencies is best effort and you may need to adapt the documented behavior for your package manager or repository's needs.

However, if you find an issue with the documentation that appears to be universally incorrect for all package managers or a specific one, please let us know with a GitHub Issue so we can improve.

### node\_modules locations

Depending on your choice of package manager, version, settings, and where your dependencies are installed in your Workspace, you may see `node_modules` and the dependencies inside it in various locations within the Workspace. Dependencies could be found in the root `node_modules`, in packages' `node_modules`, or both.

As long as your scripts and tasks are able to find the dependencies they need, your package manager is working correctly.

Referencing \`node\_modules\` in your code

### Keeping dependencies on the same version

Some monorepo maintainers prefer to keep dependencies on the same version across all packages by rule. There are several ways to achieve this:

#### Using purpose-built tooling

Tools like [`syncpack`](https://www.npmjs.com/package/syncpack), [`manypkg`](https://www.npmjs.com/package/@manypkg/cli), and [`sherif`](https://www.npmjs.com/package/sherif) can be used for this specific purpose.

#### Using your package manager

You can use your package manager to update dependency versions in one command.

#### pnpm

```
pnpm up --recursive typescript@latest
```

[\-> pnpm documentation](https://pnpm.io/cli/update#--recursive--r)

#### yarn

Yarn 1:

```
yarn upgrade-interactive --latest
```

[\-> Yarn 1 documentation](https://classic.yarnpkg.com/en/docs/cli/upgrade-interactive)

Yarn 2+:

```
yarn upgrade typescript@latest --upgrade
```

[\-> Yarn 2+ documentation](https://yarnpkg.com/cli/up)

#### npm

```
npm install typescript@latest --workspaces
```

[\-> npm documentation](https://docs.npmjs.com/cli/v7/using-npm/config#workspaces)

#### bun

```
bun update typescript --latest
```

[\-> Bun documentation](https://bun.sh/docs/cli/update)

#### pnpm catalogs

In pnpm v9.5+, you can use catalogs to define dependency version ranges as reusable constants. This will keep dependencies on the same version since you're referencing the same value across the workspace.

To learn more, [visit the pnpm catalogs documentation](https://pnpm.io/catalogs).

#### Using an IDE

Your IDE's refactoring tooling can find and replace the version of a dependency across all `package.json` files in your repository at once. Try using a regex like `"next": ".*"` on `package.json` files to find all instances of the `next` package and replace them with the version you want. When you're done, make sure to run your package manager's install command to update your lockfile.

## Next steps

Now that you know how to manage dependencies effectively in a workspace, let's [create an Internal Package](creating-an-internal-package.md) to be used as a dependency in your monorepo.
