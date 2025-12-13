---
title: Spawn Maps & Templates
parent: Spawner Documentation
nav_order: 4
---

# Spawn Maps & Templates

## Overview

**Spawn Maps** and **Template Events** are a foundational concept of the Ritter Ultimate Event Spawner.

Instead of placing large numbers of real events directly onto gameplay maps, the Event Spawner allows developers to:

* Define events once on dedicated **spawn maps**
* Treat those events as reusable **templates**
* Dynamically spawn copies of those templates onto gameplay maps

This approach dramatically simplifies event management, improves maintainability, and enables powerful persistence systems when combined with boundaries.

---

## What Is a Spawn Map?

A **spawn map** is a normal RPG Maker map that is designated for holding **template events**.

Key characteristics of spawn maps:

* They are **not intended for player access**
* They exist purely as data sources
* Events placed on them act as templates, not live gameplay events

You may create **as many spawn maps as you like**, allowing you to organize templates by:

* System type (NPCs, enemies, objects)
* Area or biome
* Gameplay purpose

---

## Template Events

Each event placed on a spawn map is treated as a **template event**.

Template events:

* Are never interacted with directly by the player
* Are never moved or modified at runtime
* Serve as the source data for spawned events

When an event is spawned, the Event Spawner:

* Copies the template event’s pages, commands, and settings
* Creates a new event instance on the target map
* Assigns it a runtime `eventId`

The original template remains unchanged.

---

## Why Templates Matter

Using template events provides major workflow advantages.

### Centralized Editing

Instead of editing many identical events across multiple maps:

* Update the template event **once**
* All newly spawned copies will reflect the changes

This is especially useful for:

* NPC dialogue updates
* Behavior or logic fixes
* Balancing changes
* Trigger events or interactables

---

### Reduced Map Clutter

Gameplay maps can remain:

* Clean
* Lightweight
* Easy to navigate in the editor

All complexity lives in spawn maps, not gameplay maps.

---

### Flexible Spawning

Template events can be spawned as:

* **Temporary events** (exist only while spawned, removed on map change)
* **Saved events** (persistent across map change and save/load game)

This flexibility allows you to mix dynamic and persistent content.

---

## Multiple Spawn Maps

Projects are not limited to a single spawn map.

Common patterns include:

* One spawn map per system (NPCs, enemies, interactables)
* One spawn map per region or chapter
* Separate spawn maps for temporary vs persistent templates

The Event Spawner allows you to reference:

* Any spawn map
* Any template event on that map

at the time of spawning.

---

## Example Workflow

A typical workflow using spawn maps looks like this:

1. Create a new map to act as a spawn map
2. Add the mapId to the plugin parameters Spawn Map Ids
3. Place one or more template events on the map
4. Configure event pages, logic, and commands normally
5. Use the Event Spawner to spawn copies of the template onto gameplay maps

---

## Updating Template Events

When changes to the event are required:

* Modify the template event on the spawn map in the editor

---

## Best Practices

* Keep spawn maps organized and clearly named
* Treat template events as read-only at runtime
* Avoid placing gameplay logic directly on gameplay maps when templates suffice
* Always remember to properly manage your event count by recycling

---

## Summary

* Spawn maps hold reusable template events
* Templates are copied when events are spawned
* Multiple spawn maps are fully supported
* Editing templates simplifies large-scale changes

Spawn Maps & Templates form the backbone of scalable, maintainable event-driven projects.
