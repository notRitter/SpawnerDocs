---
title: Preload Boundary Events
parent: Boundary Documentation
nav_order: 4
---

# Preloaded Boundary Events

## Overview

The **Preload System** is a core feature of the Ritter Ultimate Event Spawner’s **Boundary System**. It allows boundary events to be **prepared in advance** as lightweight metadata objects, instead of immediately spawning full RPG Maker events on the map.

Preloaded boundary events:

* Do **not** exist as active map events
* Do **not** consume event slots or processing time
* Exist only as **metadata entries** inside the Boundary’s **Saved Event Database**

When a **Spawn Boundary** later reaches the tile associated with a preload entry, the system converts that metadata into a real, fully functional map event.

Once spawned, the event **persists normally** and is tracked via the Boundary Event ID (BEID) system.

---

## What Is a Preload Object?

A **preload object** is a saved boundary event entry that represents an event *before* it is spawned.

Instead of storing a live event instance, the Boundary System stores **instructions and metadata** describing:

* *What* event should be spawned
* *Where* it should spawn
* *What* BEID the spawned event will have

These preload objects live inside the boundary’s **Saved Boundary Event Database** and are automatically resolved when needed.

> **Important:** Preload objects are part of the Boundary System’s internal state and should not be manually modified.

---

## How Preloading Works (High-Level Flow)

1. A boundary saves an event using **preload mode**
2. The system creates a **preload metadata object** tied to specific tile coordinates
3. No map event is spawned at this time
4. When a **spawn boundary overlaps the stored tile**, the system:

   * Creates the real map event
   * Assigns it a unique BEID
   * Links it back to the boundary database
5. The event persists after spawning and behaves like a normal boundary event

This approach allows very large maps to exist with hundreds or thousands of potential events without performance penalties.

---

## Preload Object Structure

Below is an example of a preload object as seen in the developer console:

![PreloadObject](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PreloadObjectExample.png)

The preload object contains the following properties:

### `isPreloadObj`

* **Type:** Boolean
* **Purpose:**

  * `true` → This entry is a preload metadata object
  * `false` → This entry represents an already-spawned event

This flag allows the Boundary System to distinguish between pending and active boundary events.

---

### `spawnEventId`

* **Type:** Number
* **Description:**
  The **event ID of the template event** located on the **spawn map**.

This is the source event that will be cloned when the preload object is resolved.

---

### `spawnMap`

* **Type:** Number
* **Description:**
  The **map ID** containing the template event referenced by `spawnEventId`.

This allows the Boundary System to correctly locate and clone the event when spawning occurs.

---

### `_BEID`

* **Type:** String
* **Description:**
  The **Boundary Event ID (BEID)** assigned to this boundary event.

The BEID is:

* Unique within the boundary system
* Used to track the event across spawn/unspawn cycles
* Required for persistence and BEID database lookups

Once the event is spawned, this BEID becomes the authoritative identifier for that event.

---

## Coordinate Binding

Each preload object is bound to **specific map coordinates**:

* Either a manually specified tile
* Or the tile associated with a **region ID** used during preload creation

When a spawn boundary overlaps the tile tied to the preload object:

* The preload object is resolved
* The event is spawned at the correct location

Until that happens, the preload object remains dormant and lightweight.

---

## Persistence Behavior

Preloaded boundary events are designed to be **persistent**:

* Once spawned, the event behaves like any other boundary-managed event
* The event is tracked via its BEID
* Unspawning and respawning respects the saved boundary event state

This ensures that preload-based events integrate seamlessly with:

* Saved Boundary Events
* BEID database lookups
* Boundary persistence logic

---

## ⚠️ Important Warnings

> **Do not modify preload objects manually.**

The properties of preload objects are **managed internally** by the Boundary System. Changing them may:

* Break event spawning
* Cause BEID mismatches
* Corrupt boundary persistence

If you need to change how or where an event spawns, recreate the preload entry using the proper Boundary System tools instead.

---

## When Should You Use Preloading?

Preloading is ideal when:

* Designing **large or open maps**
* Preparing events that should spawn only when approached
* Managing high event counts without performance loss
* Ensuring clean persistence for boundary-managed events

For most projects using the Boundary System, **preloading should be the default approach** for saved boundary events.

---

## Summary

* Preloading creates **metadata-only boundary event entries**
* No real map event exists until a spawn boundary reaches the tile
* Preload objects live in the boundary’s saved event database
* Once spawned, events persist normally via BEID tracking
* Preload object properties are internal and should not be edited

This system allows the Boundary System to scale efficiently while maintaining accurate event persistence.
