---
title: "Rendering Abstraction Layer, Dev Diary #2"
headerImage: false
author: LoganHarvell
date: 2020-06-01 14:05
layout: post
category: blog
description: Creating a rendering abstraction layer for a custom game engine.
tag:
- Dev Diary
- Rendering
- Engine
- C++
---

## Summary

To repeat guests, welcome back! Otherwise, welcome to the second entry in the dev diary series on developing a rendering abstraction layer for a custom [data-driven game engine](/custom-game-engine) in C++. If you have not read the first entry, then you can find it [here](/rendering-abstraction-dev-diary-1). Last week, I covered a few introductory points, including the project background, motivations, goals, and a rough tasks breakdown and timeline.

## Progress

If you have been following along, then you will remember that last week saw a major rework of the game engine core, dubbed the Entity system. This week saw finishing touches to the tests and a rework of the parsing system to better handle the new refactor, as the data-driven nature of the game engine relies on the ability to use JSON as a configuration language using a generic JSON parser and handlers based on the chain-of-responsibility pattern. Additionaly, I spent a fair amount of time researching the interface for asset management of models and textures within the engine in lieu of the briefly delayed initial transform class implementation.

### Refactored Parser

![Old Ownership Hierarchy](/assets/images/OldEngineHierarchy.png)
<figcaption class="caption">Old ownership hierarchy.</figcaption>

Previously, the core system of the engine was tied to four largely redundant layers of separate classes linked together through a linear chain of ownership. Due to this, the common base for the separete layers fell on the reflection system. However, this felt clunky with each layer holding a name attribute that had to be parsed in addition to the name of attribute to which the object belonged, shown below.

{% highlight json linenos %}
{
  "Name": {
    "type": "string",
    "value": "World"
  },
  "Sectors": {
    "type": "Sector",
    "value": [
      {
        "type": "Sector",
        "value": {
          "Name": {
            "type": "string",
            "value": "Sector1"
          },
          "Entities": {
            "type": "Entity",
            "value": [
              {
                "type": "Entity",
                "value": {
                  "Name": {
                    "type": "string",
                    "value": "Entity1"
                  },
                  "AuxiliaryInt": {
                    "type": "integer",
                    "value": 10
                  }
                }
              },
              {
                "type": "Entity",
                "value": {
                  "Name": {
                    "type": "string",
                    "value": "Entity2"
                  },
                  "AuxiliaryInt": {
                    "type": "integer",
                    "value": 20
                  }
                }
              }
            ]
          }
        }
      }
    ]
  }
}
{% endhighlight %}

<div class="breaker"></div>

![New Ownership Hierarchy](/assets/images/NewEngineOwnershipHierarchy.png)
<figcaption class="caption">Refactored ownership hierarchy.</figcaption>

With the refactor, the core system was simplified down to a few small main classes all dervied from a single base Entity class that maintains a set of child Entity-derived class instances directly within its attribute mappings. Due to this flatter structure and using the attribute key to replace the previous redundant name attribute, I was able to greatly shorten the JSON syntax and increase readability, shown below.

{% highlight json linenos %}
{
  "Sector1": {
    "type": "Entity",
    "value": {
      "Entity1": {
        "type": "Entity",
        "value": {
          "AuxiliaryInt": {
            "type": "integer",
            "value": 10
          }
        }
      },
      "Entity2": {
        "type": "Entity",
        "value": {
          "AuxiliaryInt": {
            "type": "integer",
            "value": 20
          }
        }
      }
    }
  }
}
{% endhighlight %}

### Asset Management

Now that the refactor is largely finished, I moved onto learning about asset management systems and the best way to handle the life cycle of models and textures within the engine. After spending some time, I have broken the asset management into what I consider the three main stages: import, runtime, and storage.

![New Ownership Hierarchy](/assets/images/AssetManagement.png)
<figcaption class="caption">Tentative asset pipeline.</figcaption>

In this pipeline, assets are imported from common file formats during the import stage using their repective importers. I plan on using SOIL2 for texture importing and ASSIMP for model importing into Texture and Model class instances representing the data. Once both assets are in the game, they will be handed off to the ContentManager. 

Naturally, the ContextManager will manage the asset pool during the runtime stage, giving references to assets where needed and saving/unloading assets when appropriate. More specifically, models will be able to save and load from a binary asset file format for convenience and efficiency. Textures will just be unloaded.

For next week, I will be finishing asset importing and basic texture and model classes. Then I will be researching render and device context management, begining the initial move towards setting up model viewing in-engine for the following week. See you then with another update!