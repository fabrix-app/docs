#### [Docs](../../) / [About fabrix](./) / Theory

# 10.2. Design Theory

## Overview

**The Fabrix framework has exactly one feature: the ability to harmoniously manage Spools.** The capability available to the developer is thus an emergent property of the Spools the user has installed. This article describes the reasoning behind this design and how it facilitates both modularity and extensibility.

Fabrix is **not** a software library of libraries, e.g. [Spring](https://en.wikipedia.org/wiki/Spring_Framework), [Rails](https://en.wikipedia.org/wiki/Ruby_on_Rails), Sails.js, which necessarily accrues bloat and entropy over time as features are added and modified. In these systems, modularity is sacrificed for extensibility, resulting in highly centralized and monolithic systems. Fabrix behaves much more like an operating system kernel, which manages resources and defines interfaces through which the resources can communicate with one another. A particular combination of Spools in a Fabrix application can be thought of similarly to the ecosystem of Linux distributions. The underlying kernel is "Linux", but the packages included and the functionality exposed to the user is customized by the distribution.

## Spool Lifecycle

Fabrix defines the an interface called the Spool Lifecycle which inform how Spools are loaded and managed.

### Lifecycle Stages

Spools are loaded in three stages: Validate, Configure, and Initialize. All Spools must complete each stage before the next stage begins.

### Lifecycle Configuration

Each Spool declares a set of events as preconditions and postconditions for each Lifecycle Stage. This allows Fabrix to determine the optimal load order for a set of Spools. 

### Lifecycle API

Each Spool implements a method corresponding to each lifecycle stage.
- [validate](https://github.com/fabrix-app/spool#validate)
- [configure](https://github.com/fabrix-app/spool#configure-1)
- [initialize](https://github.com/fabrix-app/spool#initialize)
- [sanity](https://github.com/fabrix-app/spool#sanity)

A Spool can do anything it wishes. [spool-hapi](https://github.com/fabrix-app/spool-hapi), for example, configures and starts up a [Hapi](https://hapijs.com/) server; [spool-graphql](https://github.com/langateam/spool-graphql) compiles a GraphQL schema and accepts GraphQL queries.

## Theory

The design of the Spool loading system is based on both practical and academic experience. This section explores some of the theoretical backing of the Spool system design. 

### Node EventEmitter

Fabrix applications are, in object-oriented programming terminology, instances of the [`FabrixApp`](https://github.com/fabrix-app/fabrix/blob/master/index.js#L11) type. `FabrixApp` is a subtype of [`EventEmitter`](https://nodejs.org/api/events.html#events_class_eventemitter). Fabrix applications thus inhabit a self-contained event-space via which the Spools can communicate with one another, and with the `FabrixApp` instance itself.

### The Tuple Space

The process of loading and managing the execution of Spools is governed by a modified implementation of a [tuple-space](https://en.wikipedia.org/wiki/Tuple_space) data structure. A tuple-space consists of a single shared object space, and a collection of producers/consumers which read and deposit "tuples" to/from the shared space. In our case, the shared space is provided naturally by the `EventEmitter` type from which `FabrixApp` inherits, and the Spools are the producers/consumers of events. By specifying its preconditions/postconditions in the form of event names, each Spool provides to the system the information it needs to correctly and optimally load the Spools into the system.

### Inversion of Control

Fabrix is designed around the paradigm of [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control), which utilizes the Tuple Space and Node Event Loop together as a method of control flow, and relies on the Spools themselves to implement the essential program logic.
