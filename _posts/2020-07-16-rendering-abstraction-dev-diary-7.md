---
headerImage: true
image: /assets/images/FieaGameEngine/Title.png
title: "Vertex Buffers and Resource Views"
subtitle: "Rendering Abstraction #7"
series: Rendering Abstraction Dev Diary
author: LoganHarvell
date: 2020-07-16 13:00
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

Hello readers! We have reached the *7th* dev diary entry in the series covering the development of a rendering abstraction layer into the [Fiea Game Engine](/fiea-game-engine)! If you are new, then you might want to peruse the past entries found on my blog or using the links at the bottom of this post.

Additionally, feel free to explore the engine [source code](https://github.com/LoganTHarvell/FieaGameEngine) on GitHub. Active development, alongside a demo, can be found in its own branch named [SolarSystemDemo](https://github.com/LoganTHarvell/FieaGameEngine/tree/SolarSystemDemo).

Following through on the work started on the rendering abstraction layer during last week's update, I spent the past week swimming around in the deep end developing the rendering abstraction layer further. I will cover each aspect in more detail, but here's a quick overview:

- Improved buffer support, most notably for vertex buffers
- `VertexDeclarations`, vertex buffer creation helpers, have been moved into the engine
- Buffers with resource views have been implemented for DirectX11
- `VertexAttribute` was implemented to abstract vertex and fragment shader input and output

These improvements enable several improvements to the engine:

- `Texture` can now be abstracted
- `Shader` can now be abstracted
- Current DirectX11 shader implementations can be reused as abstraction layer implementations
- DirectX11 dependencies can now be removed from `ContentTypeReader`
- The above allows the entire content management system to migrated to the engine

## Vertex Buffers

Last week, I had implemented the initial `Buffer` abstraction and a DirectX11 implementation. This reached the point where I was able to swap out all instances of mesh index buffers with reference to the single abstract `Buffer` class, with the rendering specific implementation hidden underneath, able to be swapped out just by using the appropriate `RenderingManager` implementation.

Vertex buffers proved to be a little more work. Vertex buffers come in a variety of formats and layouts. For example, a vertex buffer may contain only a list of triangles, with sets of 3 vectors describing the vertices where each triangle may have duplicate vertices. Or a vertex buffer can be a unique set of vertices, where the triangles are described by the index buffer.

It doesn't event stop there. Vertex buffers will often have associated vertex attributes that describe the data layout, and what each vertex actually contains. So to answer that issue, I had to create `VertexAttribute`, a class that encapsulates that vertex information to be provided alongside the vertex buffer, so that you can describe it to the shaders that are taking in the information.

## Content Management and Shaders

In it's current implementation, HLSL shaders are compiled and loaded from binaries by calls to methods using the `ContentManager` class, which uses a family of `ContentTypeReader` classes that are aware of how to load the various types of assets used by the engine. This includes the various types of shaders, i.e. pixel, vertex, geometry, etc. These shaders are referenced and created in their non-abstracted forms, making the reader classes dependent on DirectX11 libraries.

However, now that full buffer abstraction is implemented, I can begin replacing all references to the DirectX11 native buffer data structures with references to the single abstract `Buffer` class. This allows the shader system to be abstracted. After which, the entire content management system will be isolated from the DirectX11 dependencies entirely, with the rendering API specific details able to be swapped out just by initializing the `ContentTypeReader` classes with the appropriate `RenderingManager` instance.

## Steady On

Overall, this past week saw slow, but steady, progress on the rendering abstraction layer. With that said the progress made is significant and has laid the groundwork for moving a significant portion of the DirectX11 rendering framework found in the Solar System demo into the engine.

Buffer support is now fully functional, and improvements to make it easier to create index and constant buffers will be done shortly, making the rendering layer even more accessible within the engine. With that done, I expect to both wrap up the abstraction of the shader system and the migration of the content management system to the engine source over the next few days.

So what remains? Well here's a basic rundown of the classes and systems within the DirectX11 framework found in the Solar System Demo that need to be moved to the engine.

These are some basic classes that only require light work to move to using glm types that I will be moving over this week:

- Basic shapes
- Basic math types
- Lights
- Cameras

Then through next week I will continue to work on separating out the abstraction from these systems:

- Textures
- Materials
- Swap Chain
- Render State
- Render Target

Looks like I still have a lot of work ahead of me, so make sure to come back next week to find out more!
