---
url: https://turborepo.dev/docs/core-concepts/package-types
title: "Package types"
description: "Understand the difference between Application Packages and Library Packages in a workspace."
access_date: 2026-08-03T17:27:52.096Z
current_date: 2026-08-03T17:27:52.096Z
---

# Package types



In Turborepo, we talk about two types of packages:

* [Application Packages](#application-packages)
* [Library Packages](#library-packages)

## Application Packages

An Application Package is a package in your workspace that will be deployed from your workspace. Examples of Application Packages are Next.js, Svelte, Vite, or CLI applications that are commonly found in the `./apps` directory.

It's best practice that your Application Packages are the "end" of your [Package Graph](/docs/core-concepts/package-and-task-graph#package-graph), not being installed into other packages of your repository. Your CI/CD pipelines will most often finalize at these nodes of your Package and Task Graphs.

### Installing an application package into another package

In rare cases, you may need to install an Application Package into another package. This should be the exception. If you find you are doing this often, you may want to rethink your package structure.

An example of an exception for this rule is installing your Application Package into a package that handles end-to-end testing. Once installed, you can depend on the Application Package in your end-to-end testing package so it is aware of re-deploys of the application.

## Library Packages

Library Packages contain code that you intend to share around your workspace. They aren't independently deployable. Instead, they support the Application Packages to create the final deployables from your repository. You might also refer to these packages as [Internal Packages](/docs/core-concepts/internal-packages), which have their own sub-types.


---

For a semantic overview of all documentation, see [/sitemap.md](/sitemap.md)

For an index of all available documentation, see [/llms.txt](/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](/agents.md)
