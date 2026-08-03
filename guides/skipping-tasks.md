---
url: https://turborepo.dev/docs/guides/skipping-tasks
title: "Skipping tasks"
description: "Use `turbo query affected` to skip CI tasks entirely when a workspace has no relevant code changes."
access_date: 2026-08-03T19:46:13.967Z
current_date: 2026-08-03T19:46:13.967Z
---

[Caching](../crafting-your-repository/caching.md) dramatically speeds up your tasks - but you may be able to go even faster by checking if your workspace is actually affected by code changes. If it isn't, you can skip executing a task altogether.

Let's say you want to skip the unit tests for your `web` workspace when there aren't any changes to your `web` application (or its package dependencies). If you are already using [Remote Caching](../core-concepts/remote-caching.md), you will probably get a cache hit - but you would still spend time provisioning the CI container, installing `npm` dependencies, and other things that can take a while.

Ideally, you would do a quick check to see if any of that work needs to happen in the first place.

---

## Using turbo query affected

After you've checked out the repo, but **before** any other work, you can take a few seconds to check if your `web` workspace is affected by the changes in your branch:

```
affected=$(turbo query affected --packages web)
count=$(echo "$affected" | jq '.data.affectedPackages.length')

if [ "$count" -gt 0 ]; then
  echo "web is affected, proceeding with build"
  # Run your build steps
else
  echo "web is not affected, skipping"
  exit 0
fi
```

The JSON output tells you exactly *what* changed and *why*, so you can make decisions beyond a simple skip/build:

```
{
  "data": {
    "affectedPackages": {
      "items": [
        {
          "name": "web",
          "path": "apps/web",
          "reason": { "__typename": "FileChanged" }
        }
      ],
      "length": 1
    }
  }
}
```

While you may have been able to hit a `>>> FULL TURBO` cache for this task, you just saved time with all of the other setup tasks required to run your CI.

### Checking for a specific task

You can check if a specific task is affected:

```
affected=$(turbo query affected --tasks test --packages web)
count=$(echo "$affected" | jq '.data.affectedTasks.length')
```

### Customizing the comparison

By default, `turbo query affected` compares against the [merge-base](https://git-scm.com/docs/git-merge-base) with your default branch. This means it detects all changes across your entire branch, not just the latest commit.

You can customize this with `--base` and `--head`:

```
turbo query affected --packages web --base main --head HEAD
```

You can also set `TURBO_SCM_BASE` and `TURBO_SCM_HEAD` environment variables. Explicit `--base` and `--head` flags take precedence.

### Quick check with --exit-code

If you only need a binary "affected or not" signal without parsing JSON, you can use the `--exit-code` flag as a shorthand. It exits with code `1` when affected results are found, `0` when nothing is affected, or `2` on errors:

```
turbo query affected --packages web --exit-code
```

For more details and the full flag reference, see the [`turbo query affected` reference](../reference/query.md#affected).

## Legacy: turbo-ignore

Deprecated

`turbo-ignore` is a Node.js package that was previously the recommended way to skip CI tasks. It uses `--filter` and `--dry=json` under the hood to detect package-level changes. If you are still using it, see the [migration guide](../reference/query.md#migrating-from-turbo-ignore) for how to switch.
