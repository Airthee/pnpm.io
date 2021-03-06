---
id: workspaces
title: Workspaces
original_id: workspaces
---

pnpm supports concurrent actions in multi-package repositories (workspaces). A workspace must have a [pnpm-workspace.yaml](pnpm-workspace_yaml) file in
its root. A workspace also may have a [.npmrc](npmrc) in its root.

## Workspace ranges (workspace:)

Supported since v3.7.0.

By default, pnpm will link packages from the workspace if the available packages match
the declared ranges. For instance, `foo@1.0.0` is linked into `bar` if `bar` has `"foo": "^1.0.0"` in dependencies.
But if `bar` has `"foo": "2.0.0"` in dependencies and `foo@2.0.0` is not in the workspace then
`foo@2.0.0` will be installed from the registry. This behavior introduces some uncertainty.

Luckily, pnpm supports the `workspace:` protocol (same as in Yarn v2). When this protocol is used pnpm will refuse
to resolve to anything else than a local workspace package. So if you set `"foo": "workspace:2.0.0"` installation
will fail telling that no `"foo@2.0.0"` is present in the workspace.

##  Publishing workspace packages

When a workspace package is packed into an archive (whether it's through `pnpm pack` or one of the publish commands like `pnpm publish`), we dynamically replace any `workspace:` dependency by:

* The corresponding version in the target workspace (if you use `*`)
* The associated semver range (for any other range type)

So for example, if we assume we have three workspace packages whose current version is `1.5.0`, the following:

```json
{
  "dependencies": {
    "foo": "workspace:*",
    "bar": "workspace:^1.2.3"
  }
}
```

Will be transformed into:

```json
{
  "dependencies": {
    "foo": "1.5.0",
    "bar": "^1.2.3"
  }
}
```

This feature allows you to not have to depend on something else than your local workspace packages, while still being able to publish the resulting packages to the remote registry without having to run intermediary publish steps - your consumers will be able to use your published workspaces as any other package, still benefiting from the guarantees semver offers.

## Options

### link-workspace-packages

Added in: v2.14.0

* Default: **true**
* Type: **Boolean**

When `true`, locally available packages are linked to `node_modules` instead of being downloaded from the registry.
This is very convenient in a multi-package repository.

#### Usage

Create a `.npmrc` file in the root of your multi-package repository with the following content:

```
link-workspace-packages = true
```

Create a `pnpm-workspace.yaml` file with the following content:

```yaml
packages:
  - '**'
```

Run `pnpm recursive install`.

### shared-workspace-lockfile

Added in: v2.17.0 (initially named `shared-workspace-shrinkwrap`)

* Default: **true**
* Type: **Boolean**

When `true`, pnpm creates a single `pnpm-lock.yaml` file in the root of the workspace (in the directory that contains the `pnpm-workspace.yaml` file).
A shared lockfile also means that all dependencies of all workspace packages will be in a single `node_modules`.

Advantages of this option:

* every dependency is a singleton
* faster installations in a multi-package repository
* fewer changes in code reviews

**NOTE:** even though all the dependencies will be hard linked into the root `node_modules`, packages will have access only to those dependencies
that are declared in their `package.json`. So pnpm's strictness is preserved.
