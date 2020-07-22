---
headerImage: true
image: /assets/images/FieaGameEngine/Title.png
title: "Shaders and Materials"
subtitle: "Rendering Abstraction #8"
series: Rendering Abstraction Dev Diary
author: LoganHarvell
date: 2020-07-16 13:50
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

Welcome! This is the *8th* dev diary entry in the series covering the development of a rendering abstraction layer into the [Fiea Game Engine](/fiea-game-engine)! As always, if you are new, then you may wish to browse past entries found on my blog or using the links at the bottom of this post.

Moreover, feel free to explore the engine [source code](https://github.com/LoganTHarvell/FieaGameEngine) on GitHub. Active development, alongside a demo, can be found in its own branch named [SolarSystemDemo](https://github.com/LoganTHarvell/FieaGameEngine/tree/SolarSystemDemo).

Last week I discussed the upcoming work regarding finishing all remaining buffer convenience methods, abstracting the shader and material classes, as well removing dependencies from the content management and basic support classes like cameras, shapes, etc.

As such, this update is fairly light. I finished implementing the convenience methods for creating index, vertex, and constant buffers within `RenderingManager`. These methods simply wrap buffer descriptor data with a call to the underlying implementation of `CreateBuffer()`.

Additionally, I implemented basic drawing methods, largely used by the material class. This update will largely focus on this material system, with an overview of how the system currently works and some of the implementation details, including both how it has changed and what work still needs to be done.

## Shaders and Materials

The `Shader` and `Material` classes are the foundational classes within rendering framework. Shaders are small programs that can be executed on the GPU quickly, converting the data within buffers ultimately into the pixels to be drawn to the screen.

Each shader contains its own binary data, native handle to the shader class, and the input layout of the per vertex attributes. Materials contain shaders and manage setting the appropriate shader and input layouts to be used when drawing a given vertex buffer and optionally an associated index buffer.

At the moment, the `Material` class stores a reference to the `RenderingManager` instance on construction, which it then uses to perform the necessary input assembly stage calls, i.e. setting the topology, input layout, buffers, and setting the shader stage data. Before finally calling the appropriate draw method.

All buffers and drawing are entirely abstracted within the `Material` class. However, the shader system still needs to be abstracted to be able to completely remove the DirectX 11 dependencies from the material class.

To do this, the shader base class and each derived shader type will have to separated into an abstracted interface, with the underlying native data handles and behavior to be stored and executed in the rendering API implementation of the shader interface.

Any `Entity` class can then own a material, which can then be used to draw the meshes of a `Model` that represents it physically in the game.

## Next Goals

After the material and shader systems are fully abstracted, the front-facing rendering functionality of the rendering framework will be largely abstracted and migrated into the engine, with mostly initialization and setup details needing to be moved into the engine. The next logical step is replacing the rendering framework used in the demo completely with engine code.
