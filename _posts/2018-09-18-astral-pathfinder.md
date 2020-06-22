---
headerImage: true
image: /assets/images/AstralPathfinder/Icon.png
title: "Astral Pathfinder"
author: LoganHarvell
date: 2018-09-18 21:30
description: A galaxy conquest, resource management game written in C++ with SDL2 frameworks.
tag:
- Game
- C++
- SDL2
layout: post
category: project
---

## Summary

![New Ownership Hierarchy](/assets/images/AstralPathfinder/PrototypeMenu.png)
<figcaption class="caption">Command line prototype.</figcaption>

Astral Pathfinder initially began as a text-based game inspired by on an old resource management simulation called [Hammurabi](http://www.hammurabigame.com/hammurabi-game.php "Hammurabi"). It was conceived as the final project for Introduction to C Programming in Fall 2015, thus it was developed in C as a command line application. The source code can be found [here](https://github.com/LoganTHarvell/Astral-Pathfinder-Command-Line-Prototype).

![New Ownership Hierarchy](/assets/images/AstralPathfinder/PrototypeGameplay.png)
<figcaption class="caption">Text-based gameplay.</figcaption>

By Spring 2018, the project was entirely reinvented from scratch as a [real-time application](https://github.com/LoganTHarvell/Astral-Pathfinder) developed in C++ using SDL2 and associated frameworks as the semester project for AI for Game Programming. The game evolved into a space conquest simulator.

## Development

![New Ownership Hierarchy](/assets/images/AstralPathfinder/Menu.png)
<figcaption class="caption">Astral Pathfinder.</figcaption>

As the creator of the project, I handled conception and design and acted as the project lead, responsible for managing, planning, and writing documentation for Astral Pathfinder. As a developer, I worked on data structures, logic, and aritficial intelligence, and wrote most of the supporting engine, including an interface created to use Lua as a configuration language for data-driven development.

![New Ownership Hierarchy](/assets/images/AstralPathfinder/Gameplay.png)
<figcaption class="caption">Real-time gameplay.</figcaption>

As a collaborator, Ian Holdeman led the development of the user interface (UI) for Astral Pathfinder. He handled the development of most screens and all UI elements. Additionally, he worked on rendering and input handling, including both UI and player controls.

![New Ownership Hierarchy](/assets/images/AstralPathfinder/WinScreen.png)
<figcaption class="caption">Win screen.</figcaption>

![New Ownership Hierarchy](/assets/images/AstralPathfinder/LoseScreen.png)
<figcaption class="caption">Lose screen.</figcaption>

![New Ownership Hierarchy](/assets/images/AstralPathfinder/Scoreboard.png)
<figcaption class="caption">High-score board.</figcaption>

Astral Pathfinder was developed for MacOS as a Cocoa application. However, by using SDL2 and platform agnostic code, the project should be easily portable for other platforms.
