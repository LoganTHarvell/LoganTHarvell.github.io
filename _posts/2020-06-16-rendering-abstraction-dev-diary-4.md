---
headerImage: false
title: "Rendering Abstraction Layer, Dev Diary #4"
series: Rendering Abstraction Layer, Dev Diary
author: LoganHarvell
date: 2020-06-16 14:20
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

Welcome to the fourth entry following the creation of a rendering abstraction layer within a custom [data-driven game engine](/custom-game-engine)! Past entries can be found on my blog or through the links found at the bottom of this post.

As a reminder, in the last entry, I described my initial implementation of an asset management pipeline, including an implementation of a `Model` class that represents the physical properties of an `Entity` object in my engine and a `Transform` class to support it. This past week saw those implementations put to the test as I demonstrated rendering the solar system within the engine using DirectX11.

![Solar System Demo](/assets/images/SolarSystemDemo.png)
<figcaption class="caption">Solar Sytem Demo.</figcaption>

## Data-Driven Solar System

Utilizing the data-driven capabilities of the engine, I exposed the properties of the solar system demo through a `SolarSystem` class derived from `Entity`, allowing configuration through JSON. Below is an example of the JSON used to change the unit scale and time scale factors of the demo.

{% highlight json linenos %}
{
  "DistanceScale": {
    "type": "float",
    "value": 80.0
  },
  "TimeScale": {
    "type": "float",
    "value": 0.3
  }
}
{% endhighlight %}

However, the data-driven aspects did not stop there. In fact, the entire solar system hierarchy was generated from JSON, exposed through the `SolarBody` class, inherited from `Actor` to take advantage of the embedded transform.

{% highlight json linenos %}
{
  "Sun": {
    "type": "SolarBody",
    "value": {

      "Mercury": {
        "type": "SolarBody",
        "value": {}
      },
      "Venus": {
        "type": "SolarBody",
        "value": {}
      },
      "Earth": {
        "type": "SolarBody",
        "value": {
          "Moon": {}
        }
      },
      "Mars": {
        "type": "SolarBody",
        "value": {}
      },
      "Jupiter": {
        "type": "SolarBody",
        "value": {}
      },
      "Saturn": {
        "type": "SolarBody",
        "value": {}
      },
      "Uranus": {
        "type": "SolarBody",
        "value": {}
      },
      "Neptune": {
        "type": "SolarBody",
        "value": {}
      },
      "Pluto": {
        "type": "SolarBody",
        "value": {}
      }

    }
  }
}
{% endhighlight %}

Finally, each solar body had configurable properties exposed to the reflection system to more accurately simulate the appearence, orientation, and astrological movement of each.

{% highlight json linenos %}
"Earth": {
  "type": "SolarBody",
  "value": {

    "ColorMap": {
      "type": "string",
      "value": "Textures/EarthComposite.dds"
    },
    "SpecularMap": {
      "type": "string",
      "value": "Textures/EarthSpecularMap.png"
    },
    "Diameter": {
      "type": "float",
      "value": 1.0
    },
    "OrbitalDistance": {
      "type": "float",
      "value": 1.0
    },
    "OrbitalPeriod": {
      "type": "float",
      "value": 1.0
    },
    "RotationPeriod": {
      "type": "float",
      "value": 1.0
    },
    "Tilt": {
      "type": "float",
      "value": 0.41
    },

    "Moon": {
      "type": "SolarBody",
      "value": {}
    }
  }
}
{% endhighlight %}

## Reworked Transform Class

To support the hierarchical nature of the solar system, the `Transform` class was completely reworked. Previously, the class was mainly a wrapper for a OpenGL Mathematics matrix (glm::mat4x4). However, this made it difficult and inefficient to break the transform class into its separate components, translation, rotation, and scale.

So, to solve this, the class now stores these components:

- `Translation`, a vector (glm::vec3)
- `Rotation`, a quaternion (glm::quat)
- `Scale`, a vector (glm::vec3)

This both simplifies single type transformations to just vector arithmetic, preventing unnecessary matrix transformations, and allowed for easy access to each component without requiring matrix decomposition.

However, as purely components, when a full transformation matrix was needed, matrix composition was required. While a fairly simple task given, I saw an opportunity to maintain some of the performance granted by the previous implementation.

By maintaining the inner matrix as a cache that stores the result of the last matrix composition, combined with the dirty flag pattern, I was able to still have direct access to the underlying transformation matrix as in the previous implementation, as long as no transformation has occurred, a reasonable compromise in my opinion.

## Rendering API Interface

With the integration of the rendering framework from the [examples](https://bitbucket.org/pvarcholik/real-time-3d-rendering-with-directx-and-hlsl/src/master/) created by Dr. Paul Varcholik for his book *Real Time 3D Rendering with DirectX and HLSL*, I reached the point where the initial implmentation of rendering abstraction was needed to prevent dependencies on DirectX in the API agnostic engine code.

As the initial implementation, I created the `RenderingManager` class as an interface that declares structs and pure virtual method prototypes for wrapping rendering API data types and functionality respectively.

Then I created `RenderingManager_DirectX11`, as a derived class from `RenderingManager` that implements the structs and pure virtual methods using DirectX11 data types and functions. This derived class is then only defined with the corresponding preproccessor defintion, preventing the need for DirectX11 support in programs that cannot or do not wish to include it.

In practice, this allowed for the `World` class to maintain a reference to the current `RenderingManager` instance. This can then be passed within the `WorldState` instance down through the `Entity` hierarchy, enabling components to access the rendering interface without dependencies on DirectX11.

![Rendering Manager](/assets/images/RenderingManager.png)
<figcaption class="caption">Rendering Manager design.</figcaption>

## Next Steps

As a next step, I plan to migrate the content manager, material, and shader system borrowed from the framework by Dr. Paul Varcholik to native engine implementations, expanding the functionality of the `RenderingManager` interface along the way.

Additionally, I plan on improving the `Actor` class to natively support local and world transforms, a limitation that became apparent when implementing the solar system demo.

Thank you for reading! I hope to see you again next week for another update.
