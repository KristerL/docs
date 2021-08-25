---
title: Overview
id: overview
---

import { Image } from '@site/src/components/image'
import scopeWorkspace from './images/workspace-scope.png';
import componentImg from './component.png';

It is a decoupled, versioned source-code container designed for the purpose of containing a single module or component. It is the most fundamental building block of Bit and what enables the composition of software from independent pieces.

Each Component has a [Component ID](/components/component-id) and contains [Source Code](/components/source-code), [Artifacts](/components/artifact), [Configuration of Aspects](/extending-bit/aspect) and [Metadata](/components/metadata). Components are connected and composed with each other through [Dependencies](/dependencies/overview).

<img src={componentImg} alt="Bit Components" height={450} />

To learn more about how components connect through dependencies, head over to the [Dependencies documentation section](/dependencies/overview).

Components are hosted and managed in [Scope](/scope/overview) and can be maintained, edited and versioned in any [Workspace](/workspace/overview). 
Upon every Tag or Snap a new object is persisted to the Scope. Operations like [Import](/) and [Export](/) allows to transfer components between
different Scopes.

A Bit Component

<img src={scopeWorkspace} height={500} />

## Component Anatomy

- [Component ID](/)
- [Tags](/)
- [Snaps](/)
- [Dependencies](/)
- [Configuration](/)

## Snaps

## Tags

## Lanes

Snaps/Lanes

A component is any group of files that serve a single purpose. In the context of Bit, that can be a JavaScript/Node.JS module, a CSS module, a React component, or any other framework or flavor of JS.
Each of these can be of different levels of complexity and concreteness.

For example, a component can be a full page or a simple UI element.
It can be a small utility function or a microservice.

Bit components, also known as 'independent components', are components that are independently developed, versioned, and collaborated on.

Independent components can play various roles in backend and frontend applications.
They can play the role of (in-memory) libraries when integrated into a project as standard packages, or that of separately-run apps and services that communicate over the network, when deployed independently using their own CI/CD.

Each independent component contains the version history of its source code, dependency graph, development setup, and artifacts (including their auto-generated Node package).

## The journey from multiple files to a single independent component

Every independent component starts its journey as a "regular component". That can be done by pre-configured templates or by creating your own files.

In order for Bit to source-control and manage the component's files as a single discrete unit (a component), it needs to map them to a single [component ID](component-id.md).

A component tracked by Bit can be built, tagged with a release version, and exported to a remote scope.

The exported independent component will contain the component's source code, dependency graph, development setup, and artifacts (including its auto-generated Node package).
