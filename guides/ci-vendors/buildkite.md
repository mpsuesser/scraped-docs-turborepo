---
url: https://turborepo.dev/docs/guides/ci-vendors/buildkite
title: "Buildkite"
description: "Configure Buildkite pipelines to run Turborepo tasks with Remote Caching."
access_date: 2026-08-04T14:55:55.614Z
current_date: 2026-08-04T14:55:55.614Z
---

Learn how to use Buildkite with Turborepo.

The following example shows how to use Turborepo with [Buildkite](https://buildkite.com/).

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
      "outputs": [".next/**", "!.next/cache/**", "!.next/dev/**"],
      "dependsOn": ["^build"]
    },
    "test": {
      "dependsOn": ["^build"]
    }
  }
}
```

Create a file called `.buildkite/pipeline.yml` in your repository with the following contents:

#### pnpm

```
steps:
  - label: ":test_tube: Test"
    command: |
      pnpm install
      pnpm test

  - label: ":hammer: Build"
    command: |
      pnpm install
      pnpm build
```

#### yarn

```
steps:
  - label: ":test_tube: Test"
    command: |
      yarn
      yarn test

  - label: ":hammer: Build"
    command: |
      yarn
      yarn build
```

#### npm

```
steps:
  - label: ":test_tube: Test"
    command: |
      npm install
      npm test

  - label: ":hammer: Build"
    command: |
      npm install
      npm run build
```

#### bun

```
steps:
  - label: ":test_tube: Test"
    command: |
      bun install
      bun run test

  - label: ":hammer: Build"
    command: |
      bun install
      bun run build
```

## Create a Pipeline

To create your pipeline in the Buildkite dashboard, you'll need to first upload the pipeline definition from your repository.

1. Select **Pipelines** to navigate to the Buildkite dashboard.
2. Select **New pipeline**.
3. Enter your pipeline's details in the respective **Name** and **Description** fields.
4. In the **Steps** editor, ensure there's a step to upload the definition from your repository:

```
steps:
  - label: ":pipeline:"
    command: buildkite-agent pipeline upload
```

5. Select **Create Pipeline**, then click **New Build**, then select **Create Build**.

Run the pipeline whenever you make changes you want to verify.

## Remote Caching

To use Remote Caching, retrieve the team and token for the Remote Cache for your provider. In this example, we'll use [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching):

- `TURBO_TOKEN` - The Bearer token to access the Remote Cache
- `TURBO_TEAM` - The account to which the monorepo belongs

To use Vercel Remote Caching, you can get the value of these variables in a few steps:

1. Create a Scoped Access Token to your account in the [Vercel Dashboard](https://vercel.com/account/tokens). Copy the value to a safe place. You'll need it in a moment.
	![Vercel Access Tokens](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fvercel-create-token.2km5ecr58yqs2.png&w=3840&q=75&dpl=dpl_3F46xSPrLipxuWzbbUM4iFdKneg3)
2. Obtain [your Team URL](https://vercel.com/d?to=%2F%5Bteam%5D%2F%7E%2Fsettings&title=Find+Team+URL) and copy its value as well. Both values will be used in the next step.
3. In the Buildkite dashboard, create two new [Buildkite secrets](https://buildkite.com/docs/pipelines/security/secrets/buildkite-secrets), one for each value. Name them `TURBO_TOKEN` and `TURBO_TEAM`.
4. Update `pipeline.yml` to fetch and apply `TURBO_TOKEN` and `TURBO_TEAM` as environment variables with the [Buildkite Secrets](https://github.com/buildkite-plugins/secrets-buildkite-plugin) plugin as shown. (For additional secret-management options, read [Managing pipeline secrets](https://buildkite.com/docs/pipelines/security/secrets/managing) in the Buildkite documentation.)
	```
	steps:
	  - label: ":test_tube: Test"
	    command: |
	      npm install
	      npm test
	    plugins:
	      - secrets:
	          variables:
	            TURBO_TOKEN: TURBO_TOKEN
	            TURBO_TEAM: TURBO_TEAM
	  - label: ":hammer: Build"
	    command: |
	      npm install
	      npm run build
	    plugins:
	      - secrets:
	          variables:
	            TURBO_TOKEN: TURBO_TOKEN
	            TURBO_TEAM: TURBO_TEAM
	```
	Commit and push these changes to your repository, and on the next pipeline run, the secrets will be applied and Vercel Remote Caching will be active.
