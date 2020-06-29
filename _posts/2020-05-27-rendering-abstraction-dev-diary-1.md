---
headerImage: false
title: "Rendering Abstraction Layer, Dev Diary #1"
series: Rendering Abstraction Layer, Dev Diary
author: LoganHarvell
date: 2020-05-27 14:54
description: Creating a rendering abstraction layer for a custom game engine.
tag:
- Dev Diary
- Rendering
- Engine
- C++
layout: post
category: blog
---

## Summary

Hello and welcome! This post marks the first in a series covering the design and development of a rendering abstraction layer for a custom game engine written in C++. Since the beginning of 2020, I have designed and developed a [data-driven game engine]({{site.url}}/fiea-game-engine/), and this current project is a continuation of that previous work. I will be posting weekly updates over the next couple of months.

Before explaining exactly what the project entails, first I'll explain the motivation behind this endeavor. After wrapping up the initial engine development towards the end of April, which culminated in the recreation of the battle mode of *Super Bomberman* for the SNES using the engine, I wanted to delve even further into engine development. However, in addition to engine development, I also wished to gain more experience with computer graphics, so I decided to double down on a project that ties the two topics together. So what exactly does this project entail?

## Goals

This project proposes supporting the integration of multiple rendering API into the custom game engine created during the previous semester. To do so, an abstraction layer for rendering functionality will be designed as an interface that can be implemented by any rendering API, i.e. OpenGL, Vulkan, DirectX11, DirectX12, and Metal.

An abstraction layer simplifies extending the engine to support specific or multiple different rendering API as needed to best support the end application and target platforms. The end goal of this project is a demonstration of a dynamic scene rendered using the rendering abstraction layer with an implementation first using OpenGL 4.6, and then DirectX11, as a proof-of-concept.

## Task Breakdown

- **Refactoring the engine**
  - Simplifying the current hierarchical system into an World/Entity based system
  - Designing a Transform class with 3D math support integrated into an Actor class derived from Entity
- **Asset management**
  - Support for importing asset data from external files
    - Textures using SOIL
    - Models using ASSIMP
  - Content management system for efficiently storing and accessing imported data within memory
- **Integrating rendering with OpenGL 4.6**
  - Shader interface
  - Material system
  - Scene/View interface
  - Render and Device context managers
- **Creating the rendering interface**
  - Separating out the rendering interface from the OpenGL implementation
  - Implementing the interface with DirectX11
- **Creating the demo**
  - Obtain, create, and import a scene complete with models and textures, materials, shaders, and lighting
  - Adding dynamic elements to the demo, i.e. moving and animated objects, variable lighting
  - Render the demo with both the OpenGL and DirectX11 rendering interface implementations
  - Brief performance analysis between implementations

## Timeline by Week

- **Week 1 (5/18-5/24)**
  - Refactored hierarchical system
  - Transform class
- **Week 2 (5/25-5/31)**
  - Texture interface and import integration
  - Model interface and import integration
- **Week 3 (6/1-6/7)**
  - Finish model interface and import integration
  - Render/device context manager
- **Week 4 (6/8-6/14)**
  - Scene/View interface
  - Demo rendering a model
- **Week 5 (6/15-6/21)**
  - Material system
  - Shader interface
  - Basic lighting implementations
- **Week 6 (6/22-6/28)**
  - Import a scene with models and textures
  - Render the scene with materials, shaders, and lighting
- **Week 7 (6/29-7/5)**
  - Separate out the rendering interface
  - Implement interface with OpenGL
- **Week 8 (7/6-7/12)**
  - Implement interface with DirectX11
  - Render demo with both OpenGL and DirectX11
- **Weeks 9 (7/13-7/20)**
  - Add dynamic elements to demo
  - Adjustable lighting
  - Moving/animated objects
- **Week 10 (7/13-7/26)**
  - Optimization, code cleanup, documentation
  - Performance analysis and comparison
  - Polishing the demo

## Progress

Based on the schedule above, I am now into the second week of the timeline. Work has gonna pretty well on refactoring the engine. Previously the game used a hierarchical structure based on a `World` class as the top level object that managed one or more Sectors. Analogous to a level, Sectors were a container class for the fundamental game object in the engine, `Entity` class objects. Then it went even further, with `Entity` objects themselves composed of Actions, which were analogous to components with a connotation that they would represent a specific behavior in their update loop, thus the name.

![Old Ownership Hierarchy](/assets/images/FieaGameEngine/OldEngineHierarchy.png)
<figcaption class="caption">Old ownership hierarchy.</figcaption>

The new design was simplified and expanded to have everything derived from a shared base game object class, `Entity`. The `Entity` class manages it's own properties and can also add, remove, or create child `Entity` instances. In this way `Entity` forms a common base from which all game objects can derive from to be integrated into the game loop, such as specialized "Actions" classes that add custom behavior to a parent `Entity`.

![New Inheritance Hierarchy](/assets/images/FieaGameEngine/NewEngineInheritanceHierarchy.png)
<figcaption class="caption">Refactored inheritance hierarchy under Entity base class.</figcaption>

Thus, the ownership structure has changed significantly due to the new inheritance hierarchy. The World is now a specialized `Entity` derived class. It is a top level game object that manages the GameClock, `EventQueue`, and `WorldState`, acting as the root object for a simulation. Since it inherits from `Entity`, it is also capable of managing loading and unloading child `Entity` instances, similar to how one would load and unload levels or scenes.

Under this new hierarchy, both Sector and the base `Action` classes were removed, replaced by the single `Entity`, while all previous "Action" classes were converted to derive from Entity instead. The only new class that was introduced, is an `Actor` class. This new class is simply an Entity with a `Transform` class to support a hierarchical transform system.

![New Ownership Hierarchy](/assets/images/FieaGameEngine/NewEngineOwnershipHierarchy.png)
<figcaption class="caption">New ownership hierarchy.</figcaption>

While I am a little behind on finishing testing and implementing the `Transform` class (getting only a little distracted with thread pools, refactoring the event system, and code cleanup), I am working hard to get back on schedule, and I will be back next week with another update. Thanks for reading!
