---
id: package-name
title: Package Name
---

The Package Name is the CommonJS compatible module name for the Component, used and linked from the Workspace [node_modules directory](/workspace/node-modules).
By default, package names are computed from the [Component ID](/components/component-id). For example:

```
Component ID                      Package Name
----------------------------      -----------------------------
teambit.base-ui/input/button -->  @teambit/base-ui.input.button
```


A component's package name can be modified by configuring the PKG Aspect in its [Component configuration](/components/component-config).

Use the placeholders `{name}` and `{scope}` to inject the component's [full name](/components/component-id#component-full-name) and [scope name](/components/component-id#scope-name).

```json
{
  "teambit.pkg/pkg": {
    "packageJson": {
      "name": "@{scope}/{name}",
    }
  }
}
```

Modifying a component's package name will affect the name of its package when packed as part of the build pipeline but also when symlinked in the workspace, during development.

:::caution
Packages with modified names will not be available on Bit Cloud registry.
:::
