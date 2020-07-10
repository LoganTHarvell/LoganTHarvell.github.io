---
headerImage: true
image: /assets/images/FieaGameEngine/Title.png
title: "Rendering with a Strategy (Pattern)"
subtitle: "Rendering Abstraction #6"
series: Rendering Abstraction Dev Diary
author: LoganHarvell
date: 2020-07-08 2:50
description: Dev diary series on implementing a rendering abstraction layer within a custom game engine.
tag:
- Dev Diary
- Rendering
- Engine
- C++
layout: post
category: blog
---

## Welcome and Recap

Hello again to those following along and welcome to anyone just stopping by and checking things out. This is the *6th* entry in the dev diary series covering the development of a rendering abstraction layer into a custom [data-driven game engine](/fiea-game-engine)! Past entries can be found on my blog or through the links found at the bottom of this post.

Feel free to explore the [source code](https://github.com/LoganTHarvell/FieaGameEngine) for the engine on GitHub. Active development, alongside a demo, can be found in its own branch named [SolarSystemDemo](https://github.com/LoganTHarvell/FieaGameEngine/tree/SolarSystemDemo).

Last week, I gave an updated schedule and stated this week would cover the integration of SOIL2 and SDL2 into the engine in order to remove dependencies on DirectX and the Windows API for texture loading and windowing, respectively. This week, I am letting you know I am a *liar*.

Instead of doing those first. I decided to focus on the second part that I had promised last week, further development of the rendering abstraction layer and initial implementations for DirectX11. This post will cover that effort.

## Struggling with the Design

Very early on, I had a `RenderingManager` class that had a DirectX implementation in a source file surrounded by a `#ifdef` when a pre-processor definition was set appropriately. This was just a quick and dirty solution to get the solar system demo up and running.

This meant I was required to have a `RenderingManager` definition to compile, and I could not change the rendering implementation without completely recompiling. So this past week I really struggled with finding the right design for the rendering abstraction layer, and I kept a couple guidelines in mind, directly antithetical to that first pass:

- Ability to compile with no rendering support
- Easy rendering implementation swaps at runtime (and obviously no re-compilation required)
- Minimal required pre-processor definitions

## Working Through It

One of the first new attempts at refactoring was making `RenderingManager` and abstract base class and implementing the base class with a DirectX11 implementation. Then I moved the DirectX11 implementation to it's own shared items project in Visual Studio, with only the Windows version of the engine static library referencing it.

This first change solved the ability to compile without rendering support and isolated the DirectX11 dependency to only projects that would support it (read: no more `#ifdef`).

Next, I took a long detour examining how the pointer-to-implementation (PImpl) design pattern could be taken advantage of for swapping between implementations. The (PImpl) design pattern is a tool for separating a class into two parts an abstraction and an implementation, and allows for the two to be developed separately.

This is especially helpful when you want to be able to swap out a loaded binary with a different version, as long as the public interface of the abstraction has not changed. No recompilation is necessary.

At first, this seemed to be exactly what I needed, but after struggling with the implementation I discovered it was actually just the wrong tool for the job. It only allows for one implementation at the same time, but I want to be able to support several.

So next I looked at other design patterns, notably the Bridge pattern, where the users can still separate the abstraction from the implementation, but also swap the implementor at run-time. Later I would realize my intent actually more aligns with the Strategy pattern, but, regardless, this approach led me in the right direction.

The next invaluable assist came from [LLGL](https://github.com/LukasBanana/LLGL), an open source low-level graphics library from [Lukas Hermanns](https://lukas-hermanns.info/). I was able to take a lot of inspiration from the project.

## Following the Strategy

In its current state, it actually remains similar to the implementation as it existed before going down the rabbit hole with the PImpl pattern, but greatly expanded. The client (generally the `World` instance), references the abstraction of the rendering interface, the `RenderingManager`, same as before. However, the rendering interface is broken down further into distinct components, with each having an abstract base class that defines the shared implementations and interface of the rendering component, i.e. a resource buffer.

![Rendering Abstraction](/assets/images/FieaGameEngine/RenderingAbstraction.png)

At the moment, the `RenderingManager` has the single DirectX 11 implementation, which contains implementors of the rendering components `RenderContext` and `Buffer`, looking to LLGL for an idea of how to set up the structure. These implementations allow me to abstract the index buffer and set the render context to draw it.

The next step is implementing the `DrawIndexed` method, which will let me remove all dependencies in the DirectX 11 framework related to drawing index buffers, replacing the code with calls to the `RenderingManager`.

## Next Steps (Keep Following It)

Obviously, the rendering abstraction still has a lot of work to be done, and the solar system demo still largely relies on the DirectX 11 framework written for the book *Real Time 3D Rendering with DirectX and HLSL* by Dr. Paul Varcholik.

Logically, the next step going forward is to continue incrementally implementing the rendering components and desired methods, replacing the dependencies in the DirectX 11 framework with calls to `RenderingManager` as described for `DrawIndexed` above. Once a part of the framework no longer has any dependencies, they will be integrated into the engine, and this will continue until the entire framework is completely integrated.

With that, you now know what I will be doing for the next short while, so I hope to come back with another update next week going over the progress. Thanks for reading!
