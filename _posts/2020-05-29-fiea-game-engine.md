---
headerImage: true
image: /assets/images/FieaGameEngine/Title.png
title: "Fiea Game Engine"
author: LoganHarvell
date: 2020-05-29 8:19
description: Designing a custom data-driven game engine in C++.
tag:
- Engine
- C++
layout: post
category: project
youTubeIdOverview: QRP27i-vNck
youTubeIdBomberman: Q54NgtNZpVE
---

## Summary

In January 2020, during the start of my spring semester, this project began as part of the curriculum in the programming track at UCF's *Florida Interactive Entertainment Academy*. I was tasked with designing and implementing a game engine in C++ from the ground up. Starting with fundamental templated container classes mirroring the C++ standard library to a full property-centric runtime type reflection system and a multi-threaded event system.

Having always been interested in engine development and systems design, I tackled these tasks head on. Over the course of the semester, I learned a whole lot about engine development. However, there is infinitely more to learn, so I continue to develop this project as a means of exploring engine and systems development further. Explore the [source code](https://github.com/LoganTHarvell/FieaGameEngine) for the engine on GitHub.

As of May 2020, I have begun work on a rendering abstraction layer for the engine with the intention of enabling support for multiple rendering API. The abstraction layer will act as an interface for rendering functionality that could then be implemented by any rendering API, i.e. OpenGL, Vulkan, DirectX11, DirectX12, and Metal, as needed to best support the developed application and it's target platforms. You can read more about this in the weekly developer diary blog series starting with the first post [here](/rendering-abstraction-dev-diary-1).

## Overview

If you would like to view the overview presentation slides for the Fiea Game Engine, click <a href="/assets/files/FieaGameEngineOverview.pdf" target="_blank">here</a>.

{% include youTube.html id=page.youTubeIdOverview %}

### Core

* **Containers**
  * `SList`, a templated singly linked list class with support for forward iterators
  * `Vector`, similar to `std::vector` with support for random access iterators
  * `HashMap`, similar to `std::unordered_map` with support for bi-directional iterators
  * `Stack`, adaptor class for `Vector`, represents a basic stack
  * `Datum`, a vector class whose type can be determined at runtime through use of a discriminated union
* **Runtime Type Reflection System**
  * `RTTI`, a custom base class for runtime type information
  * `Scope`, analogous to a property table, using `HashMap`, `Vector`, and `Datum` to store key-data pairs
  * `Attributed`, a `Scope` derived class that supports prescribed attributes (properties)
  * `TypeManager`, a singleton that manages type-attribute registration for `Attributed` derived classes
  * `Factory`, a templated implementation of the abstract factory design pattern
* **Generic Parser**
  * Utilizes the chain-of-responsibility pattern
  * Combined with `Factory`, this enables the engine to be completely data driven
  * Handler for parsing JSON data into the reflection system, enabling JSON as a configuration language
* **Events**
  * `IEventSubscriber`, acts as an interface for classes to observe an event
  * `EventPublisher`, publishes an event to subscribers
  * `Event`, subject that can be subscribed to by `IEventSubscriber` instances
    * Derived from `EventPublisher`
    * Takes a generic type "payload" as a template parameter
    * Manages the list of subscribers to the event
  * `EventQueue`, manages the queue of events, firing an event as it expires
* **Entity System**
  * `Entity`, an `Attributed` derived class thinly wrapping `Scope`, represents a base level engine object
    * All engine classes that wish to be integrated into the core game loop derive from this class
    * Supports composition through parent and child relationships with other entities
  * `World`, a top level `Entity` class that manages a simulation, a.k.a. a game
    * Owns the `GameClock` and the `WorldState`, which owns reference to `GameTime` and the `EventQueue`
    * Uses the above classes to manage the game loop
    * Root `Entity` objects can be added/removed, analogous to loading/unloading a level
  * `Reaction`, a generic implementation of `IEventSubscriber` integrated into the reflection system
* **Math**
  * `Transform`, represents position and orientation transformations of an object in 3D space

### Engine

* **Entities**
  * `Actor`, an `Entity` with a `Transform`, supports hierarchical transforms
* **Components**
  * `Model`, represents the physical attributes, can be loaded from a 3D model
  * `AnimatorComponent`, manages playing animations
* **Actions**
  * Several `Entity` derived classes that grant behavior to their parent class
  * Can be used for any behavior including data manipulation and control flow
  * Used with the parser and reflection system to define behavior within JSON

<div class="breaker"></div>

![New Inheritance Hierarchy](/assets/images/FieaGameEngine/NewEngineInheritanceHierarchy.png)
<figcaption class="caption">Engine core inheritance hierarchy.</figcaption>

<div class="breaker"></div>

![New Ownership Hierarchy](/assets/images/FieaGameEngine/NewEngineOwnershipHierarchy.png)
<figcaption class="caption">Engine core ownership hierarchy.</figcaption>

## Goals

After wrapping up the initial engine development towards the end of April 2020, the first major milestone for the engine was marked by its use in the recreation of the battle mode of *Super Bomberman* for the SNES.

{% include youTube.html id=page.youTubeIdBomberman %}

The next goal of this project will be the culmination of the current work on a rendering abstraction layer for the engine. This is planned as a visual demonstration of a dynamic scene rendered using the rendering abstraction layer with an implementation both in OpenGL 4.6 and DirectX11 as a proof-of-concept.
