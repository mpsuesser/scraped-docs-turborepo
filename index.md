---
url: https://turborepo.dev/docs
title: "Introduction"
description: "Learn what Turborepo is, how it solves monorepo scaling problems, and how to navigate the documentation."
access_date: 2026-08-03T19:02:53.509Z
current_date: 2026-08-03T19:02:53.509Z
---

## What is Turborepo?

Turborepo is a high-performance build system for JavaScript and TypeScript codebases. It is designed for scaling monorepos and also makes workflows in [single-package workspaces](guides/single-package-workspaces.md) faster, too.

From individual developers to the largest enterprise engineering organizations in the world, Turborepo is saving years of engineering time and millions of dollars in compute costs through a lightweight approach to optimizing the tasks you need to run in your repository.

## The monorepo problem

Monorepos have many advantages - but **they struggle to scale**. Each workspace has its own test suite, its own linting, and its own build process. A single monorepo might have **thousands of tasks to execute**.

![A representation of a typical monorepo. The first application took 110 seconds to complete its tasks. The second application took 140 seconds to complete its tasks. The shared package between them took 90 seconds to complete its tasks.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fwhy-turborepo-problem.0vqhrgu9522sy.png&w=3840&q=75&dpl=dpl_GvvaU1dSxMmdhnstDgQWwX3gUQJv)

These slowdowns can dramatically affect the way your teams build software, especially at scale. Feedback loops need to be fast so developers can deliver high-quality code quickly.

## The monorepo solution

![The monorepo from before using Turborepo, showing how it can hit cache to complete tasks for all three packages in 80 milliseconds.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fwhy-turborepo-solution.2xt1tkfstms2z.png&w=3840&q=75&dpl=dpl_GvvaU1dSxMmdhnstDgQWwX3gUQJv)

**Turborepo solves your monorepo's scaling problem**. [Remote Cache](core-concepts/remote-caching.md) stores the result of all your tasks, meaning that **your CI never needs to do the same work twice**.

Additionally, task scheduling can be difficult in a monorepo. You may need to build, *then* test, *then* lint...

Turborepo **schedules your tasks for maximum speed**, parallelizing work across all available cores.

Turborepo can be **adopted incrementally** and you can **add it to any repository in just a few minutes**. It uses the `package.json` scripts you've already written, the dependencies you've already declared, and a single `turbo.json` file. You can **use it with any package manager**, like `npm`, `yarn` or `pnpm` since Turborepo leans on the conventions of the npm ecosystem.

## How to use these docs

We will do our best to keep jargon to a minimum - but there are some need-to-know words that will be important to understand as you read through the docs. We've created [a glossary page](https://vercel.com/docs/vercel-platform/glossary) to help you out in case you're learning about these terms.

## Join the community

If you have questions about anything related to Turborepo, you're always welcome to ask the community on [GitHub Discussions](https://github.com/vercel/turborepo/discussions), [Vercel Community](https://community.vercel.com/tag/turborepo), and [Twitter](https://twitter.com/turborepo).
