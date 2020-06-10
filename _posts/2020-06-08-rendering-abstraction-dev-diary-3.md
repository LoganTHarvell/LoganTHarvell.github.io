---
headerImage: false
title: "Rendering Abstraction Layer, Dev Diary #3"
series: Rendering Abstraction Layer, Dev Diary
author: LoganHarvell
date: 2020-06-09 12:28
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

Welcome! This is now the third entry into the developer diary detailing the creation of a rendering abstraction layer within a custom [data-driven game engine](/custom-game-engine)! If you are new, past entries can be found on my blog or through the links found at the bottom of this post. Last week, I introduced a tentative asset pipeline. Today, I will discuss a slightly streamlined design and some of the implementation.

![Tentative Asset Pipeline](/assets/images/AssetManagement.png)
<figcaption class="caption">Previous asset pipeline design.</figcaption>

<div class="breaker"></div>

![New Asset Pipeline](/assets/images/NewAssetManagement.png)
<figcaption class="caption">New asset pipeline design.</figcaption>

With this design in mind, focus shifted towards implementation. Following along the [examples](https://bitbucket.org/pvarcholik/real-time-3d-rendering-with-directx-and-hlsl/src/master/) provided by my advisor Dr. Paul Varcholik for his book *Real Time 3D Rendering with DirectX and HLSL*, I implemented two key pieces in the asset pipeline and a component for animating a model at runtime.

- `Model`: a class representing physical properties
- `AssetImporter`: a static class encapsulating functionality for loading assets from a file
- `AnimatorComponent`: a component class for animating a model at runtime

To support these additions, I also implemented a Transform class that wraps a matrix representing 3D spatial transformations that utilizes [OpenGL Mathematics (glm)](https://glm.g-truc.net/0.9.9/index.html).
  
## Model Class

The `Model` class is a composition of supporting classes. A single `Model` may contain one or many of each of these elements. These classes include:

- `Mesh`: represents the physical geometry of a model
- `ModelMaterial`: a wrapper class for a model texture stack
- `AnimationClip`: represents an animation sequence for a model
- `Bone`: represents a vertex offset within a skeleton of a model used for animation
- `SceneNode`: represents a node in a weighted hierarchy that makes up the skeleton of a model

## Animation

Animation was something that I had originally planned on integrating into the engine much later. However, since it was closely related to a model and had its own requirements during import, the `Model` class was implemented to support data structures related to animation.

Briefly described above, each model may contain a set of `AnimationClip` instances and sets of `Bone` and `SceneNode` instances that make up the skeleton of a model. Going deeper, each `AnimationClip` contains a series of `BoneAnimation` instances, which stores the `Keyframe` instances associated with a `Bone`, and a `Keyframe` cotains the transformation information for a Bone at a given timestamp.

To play these animations, I created the `AnimatorComponent` class, a component class derived from Entity that manages playback of a given `AnimationClip` of an associated `Model`.

## Asset Importer

With a fleshed out `Model` class, I then moved onto the creation of an `AssetImporter` class capable of loading a model from any of the dozens of file formats supported by [Open Asset Import Library (ASSIMP)](https://github.com/assimp/assimp). The `AssetImporter` class provides a single `Load()` function for loading models, but this is broken down behind the scenes into steps for loading the meshes, model materials, and animation clips.

## Next Steps

This past week diverged slightly from my initial plans, so the next steps are a little off from the original timeline. I ended up going deeper with the `Model` class, opting to add animation support earlier than planned, but the `AssetImporter` does not yet support texture importing.

Additionally, my current implementation leaves a lot of room for improvement. The `Model` and `AnimatorComponent` classes lack JSON configuration support, and the `ContentManager` class, which I described last week, is an additional system needed to fully realize the asset management design that I proposed.

Nevertheless, this past week saw significant progress, and I will use the work done ahead of schedule to make up the work that still needs to be done on asset management system this week. After that is wrapped up, I will be aiming to demo rendering a model in OpenGL 4.6+ using the engine.

Thank you for following along! I hope to see you again next week!
