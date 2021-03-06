---
id: outdated
title: pnpm outdated
original_id: outdated
---

Check for outdated packages. The check can be limited to a subset of the installed
packages by providing arguments (patterns are supported).

## Synopsis

```text
pnpm outdated [-r] [--filter &lt;package selector>]
              [&lt;package pattern> ...]

pnpm recursive outdated [--filter &lt;package selector>]
                        [&lt;package pattern> ...]
```

## Examples

```
pnpm outdated
pnpm outdated gulp-* @babel/core
```

## Options

### --recursive, -r

Check for outdated dependencies in every package found in subdirectories
or in every workspace package, when executed inside a workspace.

### --filter &lt;package_selector>

[Read more about filtering.](../filtering)

### --global

List outdated global packages.

### --long

Added in: v4.0.0

Print details.

### --no-table

Added in: v4.0.0

Prints the outdated dependencies in a list, not in a table. Good for small consoles.

### --compatible

Added in: v4.7.0

Prints only versions that satisfy specs in `package.json`.

### --dev

Added in: v4.7.0

Checks only `devDependencies`.

### --prod

Added in: v4.7.0

Checks only `dependencies` and `optionalDependencies`.

### --no-optional

Added in: v4.7.0

Doesn't check `optionalDependencies`.
