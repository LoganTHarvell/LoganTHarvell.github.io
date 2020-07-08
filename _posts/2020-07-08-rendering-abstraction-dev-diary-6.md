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

Very early on, I had a `RenderingManager` class that had a DirectX implementation in a source file surrounded by an `ifdef` when a pre-processor definition was set appropriately. This was just a quick and dirty solution to get the solar system demo up and running.

This meant I was required to have a `RenderingManager` definition to compile, and I could not change the rendering implementation without completely recompiling. So this past week I really struggled with finding the right design for the rendering abstraction layer, and I kept a couple guidelines in mind, directly antithetical to that first pass:

- Ability to compile with no rendering support
- Easy rendering implementation swaps at runtime (and obviously no re-compilation required)
- Minimal required pre-processor definitions

## Working Through Some Things

One of the first new attempts at refactoring was making `RenderingManager` and abstract base class and implementing the base class with a DirectX11 implementation. Then I moved the DirectX11 implementation to it's own shared items project in Visual Studio, with only the Windows version of the engine static library referencing it.

This first change solved the ability to compile without rendering support and isolated the DirectX11 dependency to only projects that would support it (read: no more `ifdef`).

Next, I took a long detour examining how the pointer-to-implementation (PImpl) design pattern could be taken advantage of for swapping between implementations. The (PImpl) design pattern is a tool for separating a class into two parts an abstraction and an implementation, and allows for the two to be developed separately.

This is especially helpful when you want to be able to swap out a loaded binary with a different version, as long as the public interface of the abstraction has not changed. No recompilation is necessary.

At first, this seemed to be exactly what I needed, but after struggling with the implementation I discovered it was actually just the wrong tool for the job. It only allows for one implementation at the same time, but I want to be able to support several.

So next I looked at other design patterns, including Bridge and Template Method patterns. Their approaches led me in the right direction. Though I realized later, it was still not quite on the mark. However by then my implementation had naturally evolved into a working version that when re-examined is closely follows the Strategy design pattern.

## Following the Strategy

In its current state, it actually remains similar to the implementation before going down the rabbit hole with the PImpl pattern and company, but greatly expanded. The rendering interface is broken into distinct components, with each having an abstract base class that defines the shared implementations and interface of the rendering component (i.e. a buffer).
