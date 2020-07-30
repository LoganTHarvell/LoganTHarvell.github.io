---
headerImage: true
image: /assets/images/FieaGameEngine/Title.png
title: "Month Two Review, Moving Forward"
subtitle: "Rendering Abstraction #9"
series: Rendering Abstraction Dev Diary
author: LoganHarvell
date: 2020-07-29 21:35
description: Dev diary series on implementing a rendering abstraction layer within a custom game engine.
tag:
- Dev Diary
- Rendering
- Engine
- C++
layout: post
category: blog
---

## Summary

This post is serves as both the *9th* entry and as a review of the project regarding the extension of the [Fiea Game Engine](/fiea-game-engine) with the development of a rendering abstraction layer. The goal of the rendering abstraction is to facilitate implementations of a single interface using a specific and/or multiple different modern rendering API as best suited for applications and target platforms that are developed within the engine.

If you would like a more in-depth look at what this project has entailed, weekly progress updates can be found on the Blog page, or you can explore the [source code](https://github.com/LoganTHarvell/FieaGameEngine) for the engine on GitHub. Otherwise, this post will serve as a good overview of the project and an overview of all the work accomplished so far, before eventually discussing the state of the project moving forward.

Over two months ago, when first proposing the development of a rendering abstraction layer using the custom game engine that I have worked on since the end of 2019, I gave a brief breakdown of the tasks and an initial timeline. Since then, I reviewed my progress and revised the my tasks and schedule as detailed in [Rendering Abstraction #5](/rendering-abstraction-dev-diary-5). Here is a quick summary of the tasks accomplished and the actual timeline as it happened the past 10 weeks of development.

![Tasks](/assets/images/FieaGameEngine/TenWeekReviewTasks.png)

![Timeline](/assets/images/FieaGameEngine/TenWeekProgress.png)

## Improving the Engine

I am very pleased with the progress that I achieved. First, having significantly improved the engine. Revising the `Entity` class to support a property-centric entity-child structure, which includes an `Actor` class with support for hierarchical transforms, and improving the configuration language support with JSON for data-driven development.

![Entity Hierarchy](/assets/images/FieaGameEngine/NewEngineOwnershipHierarchy.png)
<figcaption class="caption">Entity-child structure for game objects.</figcaption>

Around this time I also implemented a `Model` component for representing the physical representation of a game object, with support for animations and also the ability to import new assets using ASSIMP and store them as binary files for easy save and load using the `ContentManager` class adapted from a DirectX 11 framework I adapted from the [examples](https://bitbucket.org/pvarcholik/real-time-3d-rendering-with-directx-and-hlsl/src/master/) created by Dr. Paul Varcholik for his book *Real Time 3D Rendering with DirectX and HLSL*.

Using this framework I was able to create an interesting "Solar System Demo" that demonstrates the hierarchical entity structure in action. The `SolarSystemDemo` class served as a game instance with a `SolarSystem` class derived from `Entity` that acts as a root game object, or a "level". The solar system entity then establishes the game object hierarchy, owning `SolarBody` instances derived from `Actor` that represent the sun and all the solar system's planets (and the Earth's moon), with all of these objects populated using a JSON configuration file. Each `SolarBody` instance shares a sphere model loaded using the `ContentManager` into the owning `SolarSystem` class, whose data-driven parameters drove characteristics of the demo.

![Process](/assets/images/FieaGameEngine/SolarSystemDemoFlow.png)
<figcaption class="caption">Flow of the Solar System Demo.</figcaption>

With this design, I went so far as to support hot reloading of the demo at runtime through the data members exposed through the runtime type reflection system integrated into `Entity` classes.

![HotReload](/assets/images/FieaGameEngine/HotReload.gif)

## Rendering Abstraction

At the point just after last month's review, the rendering abstraction was still in its infancy with the only real use being a handle to the device and render contexts. The demo depended on the DirectX 11 framework for most of the rendering.

However, this left me in a nice situation, where I could break apart the DirectX 11 framework into approachable byte sized pieces before abstracting and removing dependencies for common rendering functionality and adapting the framework as a DirectX11 implementation of the abstract interface, while at the same time borrowing from ideas presented by Lukas Hermanns in his work for [LLGL](https://github.com/LukasBanana/LLGL).

![Process](/assets/images/FieaGameEngine/Process.png)
<figcaption class="caption">Process of development.</figcaption>

Naturally, I began the push to abstract much of the core rendering functionality. First creating a `RenderingManager` abstraction base class that acts as the main interface, being owned by the game instance and passed down the entity hierarchy during the game loop through the `WorldState` wrapper class, as shown in the Solar System Demo diagram.

This design closely follows the Strategy design pattern, where a client references the abstraction layer using it as an interface to for underlying implementations that can be swapped out and replaced, even at runtime.

![RenderingAbstraction](/assets/images/FieaGameEngine/RenderingAbstraction.png)
<figcaption class="caption">Rendering Abstraction</figcaption>

The main purpose of the rendering manager as of now is to:

- Facilitate the creation of resources
  - Creating buffers from mesh data or descriptors
  - Creating shaders from files for the ContentManager
- Handle maintaining the state of the render context
  - Configuring resources to prepare for drawing
  - Setting buffers, shaders and their resources, vertex shader input layout, etc.
- Expose drawing methods, which are mainly used by `Material` class instances within the engine

To support this, I had to create a number of abstractions for the common resources used for rendering and DirectX 11 implementations wrapping the native data type for that API, which include:

- **Buffers**, used for storing data like shader input, vertex data, indices for other buffers, etc.
- **Textures**, used for storing pixel data generally as sub-resources for shaders and materials
- **Samplers**, data describing how the shaders should access texture data

![Resources](/assets/images/FieaGameEngine/Resources.png)

Combined with a shader abstraction that wraps handles to compiled shader programs, these resources are used by the non-API dependent `Material` class to prepare for a render call and then draw through method calls to a `RenderingManager` instance.

## Future of the Project

The above denotes most of the work I have managed to accomplish so far. If you have read my original proposal or last month's review, then you may have noticed that I had originally intended to work on an OpenGL implementation in addition to the DirectX 11 implementation that I have worked on since then. This has shifted into a future goal, as the abstraction and separation of the DirectX 11 implementation is still not quite finished, with a fair amount of the render context initialization has not been abstracted yet, notably the swap chain.

Going forward, this project will continue at a steady pace, albeit potentially a little slower. I intend to maintain, at the least, monthly updates on my progress. With that said, I hope to finish abstracting these last remaining pieces over the next month and then rework the SolarSystemDemo entirely within the engine framework, removing code reliant on the DirectX 11 framework used to jump start the first demo.

To anyone reading this, thank you for your interest, and I hope you will come back again next month for another update.
