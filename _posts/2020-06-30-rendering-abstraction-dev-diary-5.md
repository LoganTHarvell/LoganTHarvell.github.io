---
headerImage: false
title: "Rendering Abstraction Layer, Dev Diary #5"
series: Rendering Abstraction Layer, Dev Diary
author: LoganHarvell
date: 2020-06-30 10:00
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

Hello again! This is now the 5th entry following the creation of a rendering abstraction layer within a custom [data-driven game engine](/fiea-game-engine)! Past entries can be found on my blog or through the links found at the bottom of this post.

Since the last update, all of the source code for the game engine has been migrated to git and pushed to a public repository on GitHub. Feel free to explore the [source code](https://github.com/LoganTHarvell/FieaGameEngine) for the engine. The demo is in its own branch named [SolarSystemDemo](https://github.com/LoganTHarvell/FieaGameEngine/tree/SolarSystemDemo).

## Timeline Updates

Back in [Dev Diary #1](/rendering-abstraction-layer-dev-diary-1), I proposed a general timeline for the progress of this project. Naturally, plans change and I wanted to provide a brief breakdown of the progress so far, as well as the updates to the timeline.

![TimelineChanges](/assets/images/FieaGameEngine/TimelineComparison.png)

![TimelineUpdates](/assets/images/FieaGameEngine/UpdatedTimeline.png)

### Current Timeline

- Week 6 (6/22-6/28)
  - Update Entity parsing for assets, transforms
  - Hot reloading JSON configuration
  - Update Actor with local and world transforms
  - Move RenderingManager to PImpl pattern
- Week 7 (6/29-7/5)
  - Integrate SOIL2 for texture importing
  - Integrate SDL2 for windowing to replace winrt
  - Separate DirectX11 dependent implementation into the RenderingManager interface
- Week 8 (7/6-7/12)
  - Implement RenderingManager with DirectX11
  - Initial OpenGL implementation
- Weeks 9 (7/13-7/20)
  - Finish OpenGL rendering implementation
  - Render demo with both DirectX11 and OpenGL
- Week 10 (7/13-7/26)
  - Optimization, code cleanup, documentation
  - Performance analysis and comparison
  - Polishing the demo

## Hot Reloading the Solar System

When first attempting to support hot reloading by re-parsing an `Entity`, I found, unlike primitive types, `Entity` children were not correctly re-used when the parser finds a pre-existing `Entity`, instead the parser would append a new `Entity` into the `Datum`. Obviously, this would make updating an existing instance impossible and, by extension, hot reloading.

### Fixing the Issue

At first, I believed this was a limitation of the way `Entity`, `Attributed`, and `Scope` were designed. However, after going down long and unsuccessful attempts at adjusting the design to solve the problem, I realized it was simply not looking for pre-existing entities at a given key. That said, changing the parser to only create a new child when one did not already exist was just the first step.

As it stood, it would only ever create one `Entity` instance per key. Once the first was created, it would just re-use the first, but we also want to support arrays of entities. To do so, I had to add an array index to the parser stack frame data structure. The index tracked the current `Entity` object to be parsed. When smaller than the current size of the array, it re-uses the existing instance at that index. Alternatively, when larger, it creates a new child.

### Applied to the Demo

With the updated parse helper, now all elements of the `SolarSystem` could be properly updated by simply re-parsing the JSON configuration file after a change has been saved. This means that you could tweak the demo parameters like time and distance scaling factors, or even add planets and change their size, orbits, tilt, textures, etc. on the fly.

![HotReload](/assets/images/FieaGameEngine/HotReload.gif)

This was done by simply increasing the distance and time scale values, and the sun diameter shown in the JSON below, and also adding JSON for the planets similar to what was shown in [Dev Diary #4](/rendering-abstraction-layer-dev-diary-4).

{% highlight json linenos %}
{
  "DistanceScale": {
    "type": "float",
    "value": 130.0
  },
  "TimeScale": {
    "type": "float",
    "value": 1
  },

  "Sun": {
    "type": "SolarBody",
    "value": {
      "Diameter": {
        "type": "float",
        "value": 6.0
      },

      "comment": "json for planets below here..."
    }
  }
}
{% endhighlight %}

## Reworked Actor Class

While creating the Solar System Demo, I found that the current implementation of the `Actor` class had room for improvement. Previously, it only contained a single `Transform` that was intended to represent it's orientation in 3D space, effectively a world transform.

However, this meant I was required to explicitly create the transform hierarchy in the implementation of `SolarBody`, where the `Update` method overwrote the existing transform each frame with the necessary local changes and also found it's parent and multiplied the world transforms.

I wanted to generalize this for the `Actor` class, so I added a second transform to act as a local transform and made the other transform officially a world transform. This enabled the use of the local transform to store changes in local space. It also enabled the transform hierarchy to be managed directly by `Actor`, mirroring the parent-child relationships established by the `Entity` hierarchy, where each world transform of an `Actor` is generated by multiplying the current local transform with a parent's world transform.

Additionally, I took care to minimize the number of unnecessary matrix multiplications. To do so, I again use the dirty flag pattern to ensure the world transform is only updated when it's matrix is dir it's getter method is called, effectively deferring any matrix multiplication to the absolute last possible moment.

## Next Steps

Of course, having started updated schedule this past week, I am already slightly behind schedule, so I will have to research and transition my current RenderingManager interface to the pointer-to-implementation (PImpl) as the first thing this week.

After that is complete, following the newly updated timeline, this week I will integrate SOIL2 into the engine to remove dependency on DirectX for loading textures. Likewise, I will integrate SDL2 support for windowing to remove dependencies on Windows libraries. And finally, I will begin separating out the remaining dependencies on DirectX, so that I can migrate as much of the rendering framework as possible from the demo directly into the engine.

Thank you again for taking the time to listen to my musings. Until next time, I say farewell!
