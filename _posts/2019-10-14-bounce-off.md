---
headerImage: true
image: /assets/images/BounceOff/StartScreen.png
title: "Bounce Off"
author: LoganHarvell
date: 2019-10-14 10:00
description: A real-time, head-to-head competitive game written in 68K assembly for the EASy68K emulator.
tag:
- Assembly
- Game
layout: post
category: project
---

![BounceOff](/assets/images/BounceOff/BounceOff.gif)

## Summary

Bounce Off is a real-time, head-to-head competitive game where two players use momentum-based controls to maneuver a ball around the screen, aiming to pickup randomly spawning points before the other player. Each player will bounce off any and all surfaces, including each other while maintaining momentum. The first player to reach 9 points wins the game.

This game was written from scratch in 68K assembly and runs on the EASy68K emulator. You can find the [source code](https://github.com/LoganTHarvell/BounceOff) on GitHub.

Highlight features written for this project include:

- bitmap drawing
- floating-point arithmetic
- time-based movement
- collision
- random number generation
- seven-segment display
