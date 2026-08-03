---
url: https://turborepo.dev/docs/guides/tools/docker
title: "Docker"
description: "Use turbo prune to create optimized Docker images from your monorepo with minimal dependencies."
access_date: 2026-08-03T19:40:59.569Z
current_date: 2026-08-03T19:40:59.569Z
---

Learn how to use Docker in a monorepo.

Building a [Docker](https://www.docker.com/) image is a common way to deploy all sorts of applications. However, doing so from a monorepo has several challenges.

## The problem

**In a monorepo, unrelated changes can make Docker do unnecessary work when deploying your app.**

Let's imagine you have a monorepo that looks like this:

server.js

package.json

package.json

package.json

package-lock.json

You want to deploy `apps/api` using Docker, so you create a Dockerfile:

```
FROM node:16

WORKDIR /usr/src/app

# Copy root package.json and lockfile
COPY package.json ./
COPY package-lock.json ./

# Copy the api package.json
COPY apps/api/package.json ./apps/api/package.json

RUN npm install

# Copy app source
COPY . .

EXPOSE 8080

CMD [ "node", "apps/api/server.js" ]
```

This will copy the root `package.json` and the root lockfile to the Docker image. Then, it'll install dependencies, copy the app source and start the app.

You should also create a `.dockerignore` file to prevent node\_modules from being copied in with the app's source.

```
node_modules
npm-debug.log
```

### The lockfile changes too often

Docker is pretty smart about how it deploys your apps. Just like Turborepo, it tries to do as [little work as possible](https://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/).

In our Dockerfile's case, it will only run `npm install` if the files it has in its image are *different* from the previous run. If not, it'll restore the `node_modules` directory it had before.

This means that whenever `package.json`, `apps/api/package.json` or `package-lock.json` change, the Docker image will run `npm install`.

This sounds great - until we realize something. The `package-lock.json` is *global* for the monorepo. That means that **if we install a new package inside `apps/web`, we'll cause `apps/api` to redeploy**.

In a large monorepo, this can result in a huge amount of lost time, as any change to a monorepo's lockfile cascades into tens or hundreds of deploys.

## The solution

The solution is to prune the inputs to the Dockerfile to only what is strictly necessary. Turborepo provides a simple solution - `turbo prune`.

```
turbo prune api --docker
```

Running this command creates a **pruned version of your monorepo** inside an `./out` directory. It only includes workspaces which `api` depends on. It also **prunes the lockfile** so that only the relevant `node_modules` will be downloaded.

### The --docker flag

By default, `turbo prune` puts all relevant files inside `./out`. But to optimize caching with Docker, we ideally want to copy the files over in two stages.

First, we want to copy over only what we need to install the packages. When running `--docker`, you'll find this inside `./out/json`.

package.json

package.json

server.js

package.json

package.json

turbo.json

package-lock.json

Afterwards, you can copy the files in `./out/full` to add the source files.

Splitting up **dependencies** and **source files** in this way lets us **only run `npm install` when dependencies change** - giving us a much larger speedup.

### Example

Our detailed [`with-docker` example](https://github.com/vercel/turborepo/tree/main/examples/with-docker) goes into depth on how to use `prune` to its full potential. Here's the Dockerfile, copied over for convenience.

Build the Dockerfile from the root of your monorepo:

```
docker build -f apps/web/Dockerfile .
```

```
FROM node:18-alpine AS base
RUN apk update
RUN apk add --no-cache libc6-compat
# Set working directory
WORKDIR /app

# ---
FROM base AS prepare
# Replace <your-major-version> with the major version installed in your repository. For example:
# RUN yarn global add turbo@^2
RUN yarn global add turbo@^<your-major-version>
COPY . .
# Add lockfile and package.json's of isolated subworkspace
# Generate a partial monorepo with a pruned lockfile for a target workspace.
# Assuming "web" is the name entered in the project's package.json: { name: "web" }
RUN turbo prune web --docker

# ---
FROM base AS builder
# First install the dependencies (as they change less often)
COPY --from=prepare /app/out/json/ .
RUN yarn install
# Build the project
COPY --from=prepare /app/out/full/ .

# Uncomment and use build args to enable remote caching
# ARG TURBO_TEAM
# ENV TURBO_TEAM=$TURBO_TEAM

# ARG TURBO_TOKEN
# ENV TURBO_TOKEN=$TURBO_TOKEN

RUN yarn turbo build

# ---
FROM base AS runner
# Don't run production as root for security reasons
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
USER nextjs

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/apps/web/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/apps/web/.next/static ./apps/web/.next/static
COPY --from=builder --chown=nextjs:nodejs /app/apps/web/public ./apps/web/public

CMD node apps/web/server.js
```

## Remote Caching

To take advantage of remote caches during Docker builds, you will need to make sure your build container has credentials to access your [Remote Cache](../../core-concepts/remote-caching.md).

There are many ways to take care of secrets in a Docker image. We will use a simple strategy here with multi-stage builds using secrets as build arguments that will get hidden for the final image.

Assuming you are using a Dockerfile similar to the one above, we will bring in some environment variables from build arguments right before `turbo build`:

```
ARG TURBO_TEAM
ENV TURBO_TEAM=$TURBO_TEAM

ARG TURBO_TOKEN
ENV TURBO_TOKEN=$TURBO_TOKEN

RUN yarn turbo run build
```

`turbo` will now be able to hit your Remote Cache. To see a Turborepo cache hit for a non-cached Docker build image, run a command like this one from your project root:

```
docker build -f apps/web/Dockerfile . --build-arg TURBO_TEAM="your-team-name" --build-arg TURBO_TOKEN="your-token" --no-cache
```
