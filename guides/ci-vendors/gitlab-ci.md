---
url: https://turborepo.dev/docs/guides/ci-vendors/gitlab-ci
title: "GitLab CI"
description: "Configure GitLab CI pipelines to run Turborepo tasks with Remote Caching."
access_date: 2026-08-03T21:45:14.046Z
current_date: 2026-08-03T21:45:14.046Z
---

Learn how to use GitLab CI with Turborepo.

The following example shows how to use Turborepo with [GitLab CI](https://docs.gitlab.com/ee/ci/).

For a given root `package.json`:

```
{
  "name": "my-turborepo",
  "scripts": {
    "build": "turbo run build",
    "test": "turbo run test"
  },
  "devDependencies": {
    "turbo": "latest"
  }
}
```

And a `turbo.json`:

```
{
  "$schema": "https://turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "outputs": [".svelte-kit/**"],
      "dependsOn": ["^build"]
    },
    "test": {
      "dependsOn": ["^build"]
    }
  }
}
```

Create a file called `.gitlab-ci.yml` in your repository with the following contents:

#### pnpm

```
image: node:latest
stages:
  - build
build:
  stage: build
  before_script:
    - curl -f https://get.pnpm.io/v6.16.js | node - add --global pnpm@6.32.2
    - pnpm config set store-dir .pnpm-store
  script:
    - pnpm install
    - pnpm build
    - pnpm test
  cache:
    key:
      files:
        - pnpm-lock.yaml
    paths:
      - .pnpm-store
```

> For more information visit the pnpm documentation section on GitLab CI integration, view it [here](https://pnpm.io/continuous-integration#gitlab)

#### yarn

```
image: node:latest
stages:
  - build
build:
  stage: build
  script:
    - yarn install
    - yarn build
    - yarn test
  cache:
    paths:
      - node_modules/
      - .yarn
```

#### npm

```
image: node:latest
stages:
  - build
build:
  stage: build
  script:
    - npm install
    - npm run build
    - npm run test
```

#### bun

```
default:
  image: oven/bun:1.2
  cache:
    key:
      files:
        - bun.lock
    paths:
      - node_modules/
  before_script:
      - bun install

build:
script: - bun run build

test:
script: - bun run test
```

## Remote Caching

To use Remote Caching, retrieve the team and token for the Remote Cache for your provider. In this example, we'll use [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching):

- `TURBO_TOKEN` - The Bearer token to access the Remote Cache
- `TURBO_TEAM` - The slug of the Vercel team to share the artifacts with

To use Vercel Remote Caching, you can get the value of these variables in a few steps:

1. Create a Scoped Access Token to your account in the [Vercel Dashboard](https://vercel.com/account/tokens)

![Vercel Access Tokens](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fvercel-create-token.2km5ecr58yqs2.png&w=3840&q=75&dpl=dpl_Np2RHkNG8vgrhan6tknPK2NjEkNL)

Copy the value to a safe place. You'll need it in a moment.

2. Go to your GitLab repository settings and click on the **Settings** and then **CI/CD** tab. Create a new variable called `TURBO_TOKEN` and enter the value of your Scoped Access Token.

![GitLab CI Variables](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fgitlab-ci-variables.2uk5w48asgn0n.png&w=3840&q=75&dpl=dpl_Np2RHkNG8vgrhan6tknPK2NjEkNL) ![GitLab CI Create Variable](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fgitlab-ci-create-variable.3k43b_fjxz0eu.png&w=3840&q=75&dpl=dpl_Np2RHkNG8vgrhan6tknPK2NjEkNL)

3. Make a second secret called `TURBO_TEAM` and set it to your team slug - the part after `vercel.com/` in [your Team URL](https://vercel.com/d?to=%2F%5Bteam%5D%2F%7E%2Fsettings&title=Find+Team+URL). For example, the slug for `vercel.com/acme` is `acme`.

Remote Caching will now be operational in your GitLab workflows.
