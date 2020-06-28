---
headerImage: false
title: "Adaptive Training Simulator"
author: LoganHarvell
date: 2020-04-18 16:30
description: Developing an adaptive training simulator for the Federal Law Enforcement Training Center.
tag:
- UE4
- C++
layout: post
category: project
---

## Summary

The adaptive training simulator is a prototype driving simulator where players drive a police vehicle to locations specified by GPS while completing various tasks. Tasks were periodic and included:

- Visual tasks, where players must find a target at the given location
- Auditory tasks, where players must quickly respond to cues from the police radio
- Cognitive tasks, where players must re-confirm the shortest route on the GPS

The project was created for the Federal Law Enforcement Training Center (FLETC) and developed by an external team of seven people using Unreal Engine 4 (UE4). Its purpose was to serve as a proof-of-concept for an adaptive training simulator that could utilize biometric feedback provided by proprietary hardware to allow for difficulty conditions to be adjusted during a session.

In this case, difficulty conditions would be controlled by three values sent over a TCP connection by researchers who would be tracking the biometric data live from a trainig subject. Specifically, the prototype had three conditions affecting difficulty, one each for testing overload in the three key testing areas.

- Visual, using increasing levels of rain to obscure visibility of the road
- Auditory, using increasing levels of radio static to hinder the ability to hear the police chatter cues
- Cognitive, using increasing numbers of incorrect routes when confirming the shortest GPS route

## Responsibilities

As one of two programmers attached to the project, my responsibilities largely fell on creating the underlying data structures and algorithms to support the navigation system. Specifically, I designed and implemented a `Navigation` component attached to the player, along with `RouteNode` actors placed in the level that formed the basis for a weighted, directed graph.

Combined these classes supported a system that enabled targets to be set and waypoints to be calculated that would take the player from their current location to the target along the path with the shortest amount of travel time. This was accomplished using an A* pathing algorithm.

When a target is set, the `RouteNode` instances closest to the player and the current target are calculated. Then using the pathing algorithm, the shortest route is calculated. Additionally, less efficient routes are calculated for providing alternate routes during the cognitive task during play.
