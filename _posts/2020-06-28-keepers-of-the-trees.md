---
headerImage: true
image: /assets/images/KeepersOfTheTrees/logo.png
title: "Keepers of the Trees"
author: LoganHarvell
date: 2020-06-28 11:00
description: Local co-op puzzle platformer developed from Studio Chili. Available on Steam starting August 7th, 2020.
tag:
- UE4
- Game
- Keepers of the Trees
layout: post
category: project

youtubeId: 'M7K7ZyslLG0?start=482'
youtubeIdPlayerIndicator: 'sQf0BMf7oAE'
youtubeIdRespawnSystem: 'yAitmTnNeNc'
youtubeIdCheckpointSystem: '8W__dI7S2Ho'
---

{% include youtubePlayer.html id=page.youtubeId %}

## What is Keepers of the Trees?

*Keepers of the Trees* is a local co-op puzzle platformer being developed by Studio Chili for release on Steam August 7th, 2020. It is set in a fantastical forest where two curious little Keepers explore, growing and de-growing plants to their adavantage while they go on their adventures.

![KeepersLog](/assets/images/KeepersOfTheTrees/KeepersLog.jpg)
<figcaption class="caption">The Keepers</figcaption>

If you would like to have follow along with updates for the game, you can follow *Keepers of the Trees* on [twitter](https://twitter.com/Keepers_Game) and [instagram](https://www.instagram.com/keepers_game/), or you can head over to the *Keepers of the Trees* [official blog](https://keepersofthetrees.wordpress.com/) and subscribe to our newsletter.

## Studio Chili

<div class="side-by-side">
    <div class="toleft">
        <img class="image" src="/assets/images/KeepersOfTheTrees/StudioChili.jpg" alt="Studio Chili Logo">
    </div>

    <div class="toright">
        <p>Studio Chili was founded December 2019 by a group of graduate students at the <em>Florida Interactive Entertainment Academy</em> comprising a diverse group of producers, designers, artists, and programmers.</p>

        <p>In total, our team have grown to 23 talented developers led by our project lead, Samuel Roberts. Our leadership team includes Brenan Wayland for design, Austin Labarbera for art, myself for programming, and Dennis Yelito as our production manager.</p>
    </div>
</div>

## Responsibilities

As the lead programmer, I oversee the design of the architecture and system design, manage my fellow programmers, and support interdisciplinary collaboration between departments with my fellow leads on the team. For the programmers, I handle assigning tasks and perform weekly reviews to ensure the project continues smooth.

However, while the lead programmer on the project, I still like to get my hands dirty. I personally developed the implementations for both a flexible co-co camera system and a checkpoint-based respawn system for *Keeepers of the Trees*, as well as the player manager system that supports them.

### Core Game Design

*Keepers of the Trees* is two player game focused on interacting with the environment, particulary a set of plants with unique properties that each player has to grow or de-grow in order to solve puzzles. Thus, the architecture for our game was designed for flexibility, with the player class and interactable classes composed of matching components on each side of an interaction. This enabled plant behavior to be adjusted on easily by just adding and removing components for the desired behavior.

![ClassComponents](/assets/images/KeepersOfTheTrees/BaseClassDiagram.png)
<figcaption class="caption">Player-Interactable class compoenents</figcaption>

### Designing a Flexible Co-Op Camera System

Being a local co-op game, a traditional third person camera was not quite suitable for our needs. Like almost any game, the camera is one of the most critical gameplay elements. Letting players have simulataneous control over the single camera would only be frustrating to deal with, and the same would be true if only one player had camera control.

To solve this problem I designed and developed a flexible camera system with our specific needs in mind. Specifically, I enabled the camera to track the average positon of the player and maintain a set distance away with a fixed orientation.

![Co-op Camera](/assets/images/KeepersOfTheTrees/camera.gif)
<figcaption class="caption">Co-op camera initial implementation</figcaption>

I also provided camera trigger volumes, that enabled camera orientation transitions and camera behavior to be changed during gameplay.

![AnyPlayerEntered](/assets/images/KeepersOfTheTrees/AnyPlayerEntered.gif)
<figcaption class="caption">Camera trigger when any player enters</figcaption>

![BothPlayersRequired](/assets/images/KeepersOfTheTrees/BothPlayersRequired.gif)
<figcaption class="caption">Camera trigger only when both players enter</figcaption>

I also developed an offscreen player indicator mechanic to help the players find each other if they end up outside the camera view.

{% include youtubePlayer.html id=page.youtubeIdPlayerIndicator %}

#### Camera Options

- Speed
- Turn Speed
- Follow
  - Follow
    - When set, the camera will move sideways with the characters
    - When not set, the camera will be equivalent to a static camera
  - Follow Depth, when set, the camera will move in and out of the foreground
  - Follow Threshold
    - Minimum position offset before the camera will begin following
    - With a value of 0, the camera will always move with the players
  - Follow Distance, a vector describing the minimum distance kept from the tracked position
- Zoom
  - Zoom, when set, the camera will zoom in and out to keep the players on the screen
  - Zoom Speed, how quickly the camera zooms
  - Max Distance, the maximum distance the camera will zoom out to
  - Tilt, when set, the camera will tilt to keep the target vertically centered
- Set Follow Distance on Begin Play
- Snap To Initial Follow Distance
  - When set, the camera starts already at the proper follow distance
  - When not set, the camera will start at its initial position then move to be at the proper follow distance
- PlayFromHere Options
  - Rotation, initial rotation of the camera during play from here
  - Follow Distance, initial follow distance of the camera during play from here

#### Camera Trigger Volume Options

If you want to change the rotation or position of the camera, you simply move the DummyCamera into the desired position and rotation and check Update Orientation. Optionally, the camera trigger has a focus point that can be moves so in front of the DummyCamera, so that the camera is properly centered when transitioned.

![Camera Trigger Setup](/assets/images/KeepersOfTheTrees/CameraTriggerSetup.gif)
<figcaption class="caption">Camera trigger setup</figcaption>

- Camera Trigger Event, specifies the requirements needed to trigger a camera transitions
  - On All Players, transition occurs when all players are in the trigger box simultaneously
  - On Any Player, transition occurs when any player enters the trigger box for the first time
  - On All Players Entered Once, transition occurs once all players have entered the trigger box at least one time, but potentially at different times
- Update Orientation
  - When set, sets the camera position and rotation to match the dummy camera relative to the focus point
  - When not set, only the camera's behavior options are changed to match the dummy camera
- Use Manual Follow Distance
  - When set, the camera Follow Distance will be set using the dummy camera's manually set follow distance
  - When not set, the co-op camera's follow distance unchanged
- IsTemporary
  - When set, the camera will revert to the state before entering as soon as the conditions are no longer met
- Dummy Camera
  - Component with the same "Camera Options" as in the co-op camera
  - Once trigger, the co-op camera options will match the dummy camera

### Respawn and Checkpoint System

Lastly, I also conceived a respawn and checkpoint system that would be best suited for a local co-op game and camera system. Inspired by LittleBigPlanet, our respawn system allows for a player to die, but not immediately be respawned.

{% include youtubePlayer.html id=page.youtubeIdRespawnSystem %}

Instead, in order to bring their partner back, the surviving player can either continue on potentially reaching the next checkpoint, or backtrack to the last checkpoint passed. Of course when both players die, the players will simply respawn together at the currently active checkpoint. This ensures that the players will always respawn near each other, preventing camera breaking issues when players respawned far off screen.

{% include youtubePlayer.html id=page.youtubeIdCheckpointSystem %}
