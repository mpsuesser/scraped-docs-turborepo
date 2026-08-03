---
url: https://turborepo.dev/docs/reference/scan
title: "scan (Deprecated)"
description: "The `turbo scan` command has been deprecated and will be removed in a future major version."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# scan (Deprecated)



<Callout type="warn" title="Deprecated">
  `turbo scan` has been deprecated and will be removed in a future major version.
  Running it will print a deprecation warning and exit.
</Callout>

Enable faster tooling for your Turborepo locally with one, interactive command.

```bash title="Terminal"
turbo scan
```

You'll be taken through a short series of steps to enable the fastest settings for your Turborepo. These optimizations include:

* **Git FS Monitor**: `turbo` leans on Git to do file change discovery. Since we have to wait for `git` to tell us about changes, we can use [Git's built-in filesystem monitor](https://git-scm.com/docs/git-fsmonitor--daemon) to get those notifications sooner.
* **Turborepo Daemon** (deprecated for `turbo run`): Turborepo's daemon is no longer used for `turbo run`. It is still used by `turbo watch` and the Turborepo LSP.
* **Remote Caching**: While Turborepo can cache your work locally, it can also share a cache across all of your machines. Enabling [Remote Caching](/docs/core-concepts/remote-caching) makes your caching **multiplayer**, ensuring that you, your teammates, and your CI machines, never do the same work twice.
* **Check `turbo` version**: We're always working towards making `turbo` better. To ensure you are using the latest version of Turborepo, we'll check your version and remind you to install `latest` if you aren't using it yet.
* **Check for Turborepo LSP**:
  Visit the [VSCode Extension Marketplace](https://marketplace.visualstudio.com/items?itemName=Vercel.turbo-vsc) and install the Turborepo LSP extension for your IDE.


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
