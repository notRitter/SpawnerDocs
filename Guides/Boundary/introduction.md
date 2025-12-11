---
title: Introduction
parent: Boundary System Guides
nav_order: 1
---

# Boundary System Introduction
![Download the Plugin](https://images.squarespace-cdn.com/content/638d8e0d08f7d34f01db55f5/aa21069d-389b-454f-8884-5398efee3571/Boundary+Visuals+Lossy.gif?content-type=image%2Fgif)
*Boundaries visualized using the Ritter_BoundaryVisualizer.js Developer Extension Plugin*

* **2 Thickness "FillOn" Boundary** shown in **Green**
* **2 Thickness "UnspawnOn" Boundary** shown in **Red**

These examples are demonstrated on-screen, but boundaries can be created off-screen at any size.

---

## Overview

The **Ritter Boundary System** allows you to dynamically **spawn** and **unspawn** events based on:

* The player’s location
* The position of any event
* Any coordinate you choose on the map

Boundaries are created via simple script calls or plugin commands and are stored under a name you define. You can reference, move, resize, or modify them at any time. Create as many boundaries as needed—each with unique shapes, sizes, and behaviors.

---

## What a Boundary Is

A **boundary** is an invisible zone the engine responds to. As its anchor moves, the boundary moves with it, automatically handling event population inside it.

You can anchor a boundary to:

* **The player**
* **Any event**
* **A static (x, y) location**

This gives you precise control over when events should appear or be removed from the map.

---

## What Boundaries Are Used For

Because boundaries can spawn **any type of event**, they enable highly dynamic, scalable, and performance-safe systems. Common uses include:

* Spawning enemies just outside player view for endless encounters
* Loading NPCs, creatures, or gathering nodes as the player approaches
* Adding environmental objects such as debris, interactables, or obstacles
* Creating temporary triggers or transfer points
* Building “living” maps full of dynamic activity
* Preloading events into the Boundary Saved Events Database for fine-grained control

**In short:** If an event doesn’t need to exist 24/7, it can be boundary-managed.

Events without external references to their eventId can safely be spawned, recycled, and unspawned.

This enables large, event-heavy maps with smooth, consistent performance.

---

## Boundary Types

Below are the primary boundary types used to control event spawning.

### **Fill On - Spawn Boundary**
![FillOnBoundary](/Assets/BoundarySpawner/Introduction/FillOnBoundary.png)
* **Type:** `FillOn`
* **Width:** 17
* **Height:** 7
* **Thickness:** 1

Behavior:

* Attempts to spawn events on every tile that is **on the boundary edge**.
* Restores any saved events found on those tiles.
* Spawns events that match your BoundaryEventSetup rules (legacy mode).
* **Thickness** extends the boundary outward.

---

### **Fill In - Spawn Boundary**
![FillInBoundary](/Assets/BoundarySpawner/Introduction/FillInBoundary.png)
* **Type:** `FillIn`
* **Width:** 17
* **Height:** 7
* **Thickness:** 1

Behavior:

* Attempts to spawn events on every tile **inside** the boundary.
* Restores saved events on checked tiles.
* Spawns events that meet BoundaryEventSetup conditions.
* Thickness extends outward as shown in later examples.

---

### **Spawn On - Spawn Boundary**
![SpawnOnBoundary](/Assets/BoundarySpawner/Introduction/SpawnOnBoundary.png)
* **Type:** `SpawnOn`
* **Width:** 17
* **Height:** 7
* **Thickness:** 2

Behavior:

* Attempts to spawn one event on a random tile that is **on the boundary edge**.
* Restores all saved events owned by this boundary found on those tiles on the boundary edge.
* Spawns events that match your BoundaryEventSetup rules (legacy mode).
* **Thickness** extends the boundary outward.

---

## Automatic Recycling & Event Limits

Each boundary can have a **maximum number of events** it’s allowed to keep active.

For example, a boundary with a limit of **40** will:

* Spawn events until it reaches 40 active
* Unspawn events automatically as they leave the unspawn boundary
* Recycle newly freed event slots for future spawns

This guarantees:

* Stable, predictable performance
* No buildup of inactive or unnecessary events
* Efficient population management

Your maps maintain controlled event populations automatically.

---

## How Boundaries Fit Into Your Workflow

The Boundary System integrates tightly with the Event Spawner:

With a simple setup; such as a **spawn boundary** just outside the player’s screen and an **unspawn boundary** a bit further you gain fully automated event streaming:

* Automatic spawning & unspawning based on player movement
* Persistent events that restore saved data
* Clean population cycling
* Scalable systems for open worlds
* Preloaded events via XY or RegionId

Because every permanent event has a performance cost, even erased events, boundaries prevent unnecessary overhead.

You may design maps with hundreds or thousands of potential events, while only **40–100** ever exist at one time.

---

## A Simple Tool with Massive Impact

The Ritter Boundary System delivers:

* Higher performance
* Larger, more dynamic maps
* Cleaner event organization
* Automated event recycling
* Scalable world structures

It keeps your event count low while allowing your world to feel full of life.
