---
id: component-ops
title: Operate Components
---

import { Image } from '@site/src/components/image'
import envServiceImg from './envs.png';
import testingImg from './testing.png';
import builderImg from './builder.png';

Component operations are made simplified and standard with [Envs](/envs/overview) and or more specifically through [Env Services](/envs/services). [Env Services](/envs/services) allowing the creation of a [Service](/envs/service) which is meant to make a certain a development operation simplified and standard across all components. Examples to services in Bit are: [Compiler](/compiler/overview), [Tester](/tester/overview), [Builder](/builder/overview), [Linter](/linter/overview), [Bundler](/bundler/overview). Services are implemented by tool-specific Aspects like [TypeScript](/typescript/overview) or [Jest](/jest/overview) and used from [Env](/envs/env) like [React](/react/overview) or [Node](/node/overview), composing them together to form an entire development experience to a certain type of a component. This makes the development of every component simple and standard for all developers regardless to the specific tools used.

Below chart is demonstrating how a `bit compile` command flows in Bit until invokes the right `TypeScript` compiler which is defined in the component's configured Env.

<Image src={envServiceImg} width="100%" />

## Compiling

Your Workspace might include components that are using different Envs which are defined to use different Compilers and compiler configurations. While the React Env apply [TypeScript](/typescript/overview) as a Compiler on some of the components in your Workspace, others might be configured with the [Node Env](/node/overview) which is defined to use [Babel](/babel/overview) or even TypeScript but with a different `tsconfig.json`.
Bit leaves you agnostic to this complexity and allows to use a single simplified command to compile all components in your workspace, regardless to the specific Compiler configured to them.

```bash
$ bit compile
  STATUS	COMPONENT ID
✔ SUCCESS	templates/envs/my-react --> Using the Aspect env which is set to use the `Babel` Compiler.
✔ SUCCESS	templates/pages/welcome --> Using the React env which is set to use the `TypeScript` Compiler.
✔ SUCCESS	templates/ui/card --> Using the React env which is set to use the `TypeScript` Compiler.
✔ SUCCESS	templates/ui/heading --> Using the React env which is set to use the `TypeScript` Compiler.
✔ SUCCESS	templates/ui/text --> Using the React env which is set to use the `TypeScript` Compiler.
✔ SUCCESS	ui/my-welcome --> Using the React env which is set to use the `TypeScript` Compiler.

✔ 6/6 components compiled successfully.
Finished. (3s)
```

Different Envs might use different `tsconfig.json` or `.babelrc.js`.

`bit watch` does the same while re-compiling on every component change.

```bash
$ bit watch
```

Same Compiler is also applied in the Component's build pipeline, to make sure your component compiles correctly for distribution.
To learn more on Compiler, head to over to the [Compiler documentation](/compiler/overview).

## Testing

Same is applied on Testing. In this case, all components are configured to use an [Env](/envs/env) which uses [Jest](/jest/overview) but with two different configuration, one for Aspects and other for React components.

```bash
bit test
```

```bash
testing total of 6 components in workspace 'my-workspace'
testing 5 components with environment company.scope/templates/envs/my-react

 PASS  ui/my-welcome/my-welcome.spec.tsx
 PASS  templates/pages/welcome/welcome.spec.tsx
 PASS  templates/ui/text/text.spec.tsx
 PASS  templates/ui/card/card.spec.tsx
 PASS  templates/ui/heading/heading.spec.tsx

Test Suites: 5 passed, 5 total
Tests:       18 passed, 18 total
Snapshots:   0 total
Time:        2.815 s, estimated 3 s
Ran all test suites.
tested 6 components in 4.194 seconds.
```

The [Tester](/tester/overview) is also adding a UI that provides a single and standard UI for exploring and learning tests regardless to the specific tools used to test the components.

<Image src={testingImg} width="100%"  />

To learn more about the Tester and component Testing, head over to the [Tester documentation](/tester/overview).

## Building

[Builder](/builder/overview) service makes the process of building components simple, standard and blazing fast. Each [Env](/envs/env) can define its [Build Pipeline](/builder/pipeline) and compose tasks implemented by other [Env Services](/envs/services) such as [Compiler](/compiler/overview) or [Tester](/tester/overview).

```bash
$ bit build
```

<Image src={builderImg} width="100%" />

To learn about building components and the Builder, head over to the [Builder documentation](/builder/overview) section.

## Linting

By default, [React Env](/react/overview) uses [ESLint](/eslint/overview) to lint components.

```bash
bit lint
bit lint --fix
```

To learn more about linting components and the Linter, head over to the [Linter documentation](/linter/overview) section.

## Formatting

By default, component formatting is configured to work with [Prettier](/prettier/overview) on all official envs.

```bash
$ bit format
```

To learn more about the Formatter, please head over to the [Formatter documentation](/formatter/overview) section.

## Customizing envs to use different tools

TODO: @debbie to complete

## Adding your own Services

TODO: @debbie to complete