---
url: https://turborepo.dev/docs/core-concepts/package-and-task-graph
title: "Package and Task Graphs"
description: "Learn how Turborepo uses directed acyclic graphs to model package dependencies and task relationships."
access_date: 2026-08-03T18:24:07.617Z
current_date: 2026-08-03T18:24:07.617Z
---

Turborepo builds a Task Graph based on your configuration and repository structure.

## Package Graph

The Package Graph is the structure of your monorepo created by your package manager. When you install [Internal Packages](internal-packages.md) into each other, Turborepo will automatically identify those dependency relationships to build a foundational understanding of your Workspace.

For a deeper explanation of how to set up your Workspace, [visit our Structuring a repository page](../crafting-your-repository/structuring-a-repository.md#anatomy-of-a-workspace). This sets the groundwork for the Task Graph, where you'll define how **tasks** relate to each other.

## Task Graph

In `turbo.json`, you express how tasks relate to each other. You can think of these relationships as dependencies between tasks, but we have a more formal name for them: the Task Graph.

Turborepo uses a data structure called a [directed acyclic graph (DAG)](https://en.wikipedia.org/wiki/Directed_acyclic_graph) to understand your repository and its tasks. A graph is made up of "nodes" and "edges". In the Task Graph, the nodes are tasks and the edges are the dependencies between tasks. A *directed* graph indicates that the edges connecting each node have a direction, so if Task A points to Task B, we can say that Task A depends on Task B. The direction of the edge depends on which task depends on which.

For example, let's say you have a monorepo with an application in `./apps/web` that depends on two packages: `@repo/ui` and `@repo/utils`:

You also have a `build` task that depends on `^build`:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

Turborepo will build a task graph like this:

![Task graph visualization. The diagram has one node at the top named "apps/web" with two lines that connect to other nodes, "packages/ui" and "packages/utils" respectively.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fsimple-task-graph.198vsfavysc04.png&w=3840&q=75&dpl=dpl_GvvaU1dSxMmdhnstDgQWwX3gUQJv)

### Transit Nodes

A challenge when building a Task Graph is handling nested dependencies. For example, let's say your monorepo has a `docs` app that depends on the `ui` package, which depends on the `core` package:

Let's assume the `docs` app and the `core` package each have a `build` task, but the `ui` package does not. You also have a `turbo.json` that configures the `build` task the same way as above with `"dependsOn": ["^build"]`. When you run `turbo run build`, what would you expect to happen?

Turborepo will build this Task Graph:

![A Task Graph visualization with a Transit Node. The diagram has one node at the top named "apps/doc" with a line that connects to a "packages/ui" node. This node does not have a "build" task. The "packages/ui" node has another line to a "packages/core" node that does have a "build" task.](https://turborepo.dev/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Ftransitive-nodes.07iwz2fbxj6m-.png&w=3840&q=75&dpl=dpl_GvvaU1dSxMmdhnstDgQWwX3gUQJv)

You can think of this graph in a series of steps:

- The `docs` app only depends on `ui`.
- The `ui` package does **not** have a build script.
- The `ui` package's *dependencies* have a `build` script, so the task graph knows to include those.

Turborepo calls the `ui` package a Transit Node in this scenario, because it doesn't have its own `build` script. Since it doesn't have a `build` script, Turborepo won't execute anything for it, but it's still part of the graph for the purpose of including its own dependencies.

#### Transit Nodes as entry points

What if the `docs/` package didn't implement the `build` task? What would you expect to happen in this case? Should the `ui` and `core` packages still execute their build tasks? Should *anything* happen here?

Turborepo's mental model is that all nodes in the Task Graph are the same. In other words, Transit Nodes are included in the graph regardless of where they appear in the graph. This model can have unexpected consequences. For example, let's say you've configured your `build` task to depend on `^test`:

```
{
  "tasks": {
    "build": {
      "dependsOn": ["^test"]
    }
  }
}
```

Let's say your monorepo has many apps and many packages. All packages have `test` tasks, but only one app has a `build` task. Turborepo's mental model says that when you run `turbo run build`, even if an app doesn't implement `build` the `test` task of all packages that are dependencies will show up in the graph.
