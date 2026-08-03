---
url: https://turborepo.dev/docs/guides/ci-vendors/gitlab-ci
title: "GitLab CI"
description: "Configure GitLab CI pipelines to run Turborepo tasks with Remote Caching."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# GitLab CI









The following example shows how to use Turborepo with [GitLab CI](https://docs.gitlab.com/ee/ci/).

For a given root `package.json`:

```json title="./package.json"
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

```json title="./turbo.json"
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

<PackageManagerTabs>
  <Tab value="pnpm">
    ```yaml title=".gitlab-ci.yml"
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
  </Tab>

  <Tab value="yarn">
    ```yaml title=".gitlab-ci.yml"
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
  </Tab>

  <Tab value="npm">
    ```yaml title=".gitlab-ci.yml"
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
  </Tab>

  <Tab value="bun">
    ```yaml title=".gitlab-ci.yml"
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
  </Tab>
</PackageManagerTabs>

## Remote Caching

To use Remote Caching, retrieve the team and token for the Remote Cache for your provider. In this example, we'll use [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching):

* `TURBO_TOKEN` - The Bearer token to access the Remote Cache
* `TURBO_TEAM` - The slug of the Vercel team to share the artifacts with

To use Vercel Remote Caching, you can get the value of these variables in a few steps:

1. Create a Scoped Access Token to your account in the [Vercel Dashboard](https://vercel.com/account/tokens)

<img alt="Vercel Access Tokens" src={__img0} placeholder="blur" />

Copy the value to a safe place. You'll need it in a moment.

2. Go to your GitLab repository settings and click on the **Settings** and then **CI/CD** tab. Create a new variable called `TURBO_TOKEN` and enter the value of your Scoped Access Token.

<img alt="GitLab CI Variables" src={__img1} placeholder="blur" />
<img alt="GitLab CI Create Variable" src={__img2} placeholder="blur" />

3. Make a second secret called `TURBO_TEAM` and set it to your team slug - the part after `vercel.com/` in [your Team URL](https://vercel.com/d?to=%2F%5Bteam%5D%2F%7E%2Fsettings\&title=Find+Team+URL). For example, the slug for `vercel.com/acme` is `acme`.

Remote Caching will now be operational in your GitLab workflows.


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
