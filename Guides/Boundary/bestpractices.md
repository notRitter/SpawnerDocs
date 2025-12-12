---
title: Best Practices
parent: Boundary System Guides
nav_order: 2
---

# Boundary System Best Practices Guide

**Ritter Ultimate Event Spawner & Boundary System**
**Best Practices Guide (Modern BEID + Preloaded Saved Events Edition)**
*For RPG Maker MV/MZ Developers - Beginner to Intermediate Level*

---

## Introduction

The Ritter Ultimate Event Spawner and Boundary System provide a modern, efficient way to build **dynamic, high‑performance RPG Maker maps** without relying on large numbers of editor‑placed events.

The Boundary System helps your maps:

* Load events only when needed
* Unload events when they leave range
* Maintain persistent identity (via BEID)
* Keep event counts low and predictable
* Run smoothly even in large, complex worlds

This guide explains the recommended best practices for working with boundaries, BEID identity, and **preloaded Saved Boundary Events**.

---

## 1. Why Performance Depends on Event Count

RPG Maker MV/MZ slows down when too many events **exist on the map**, regardless of what they’re doing.

To maintain top performance:

* Keep editor-placed events to a minimum
* Let boundaries spawn dynamic content when needed

A typical high-performance setup:

```
0–10 editor events
+ a rotating population of boundary-managed events
```

This keeps performance smooth even on low-end hardware.

---

## 2. The Boundary System: A Persistent Event Manager

Boundaries continually manage a population of dynamic events. They are **not** simple spawners - they are *persistent event managers*.

A boundary:

* Defines a region around the player or an anchor
* Decides which events should spawn/unspawn
* Maintains a **Saved Boundary Events Database**
* Assigns **BEIDs** to saved/persistent events
* Recycles unspawned events
* Restores events exactly as they were

When designed correctly, your game world remains active and populated without overloading the engine.

---

## 3. Saved Boundary Events (Recommended Best Practice)

Modern best practice:
**Use Preloaded Saved Boundary Events as your primary workflow.**

Ideal for:

* NPCs
* Enemies
* Resources & gathering nodes
* Loot objects
* World objects
* Ambient decorations
* Persistent interactables
* Player transfer events
* Trigger events

---

### 3.1 What Are Saved Boundary Events?

A Saved Boundary Event:

* Has a permanent **BEID**
* Lives in the boundary’s saved event database
* Can spawn/unspawn freely
* Maintains its state between appearances
* Is persistent as long as the boundary exists
* Recycles it's eventId, it can change upon spawn/unspawn

Saved events behave like real RPG Maker events, just managed efficiently.

---

## 4. Preloaded Saved Boundary Events

Preloading means adding entries to the boundary’s saved events database **before** the boundary spawns them for the first time.

It does **not** mean spawning them early.

### How Preloaded Entries Work

1. You insert a preload object into the boundary’s database.
2. When the boundary needs an event there:

   * It detects the preload entry
   * Spawns it for the first time
3. After first spawn, it becomes a full Saved Boundary Event:

   * Persistent
   * Recoverable
   * Restorable

This workflow is the most flexible and efficient way to populate your maps.

---

## 5. BEID: Persistent Event Identity
![FillOnBoundary](/Assets/BoundarySpawner/BEIDEntry_event.gif)

Every Saved Boundary Event has a permanent **BEID**, defined by:

```
BoundaryName,
MapID,
BEIDIndex
```

Example:

```
"Static Spawn", 3, 7
```

BEID identifies an event regardless of whether it is:

* Spawned
* Unspawned
* Not yet spawned for the first time

### Accessing BEID Entries

```
Ritter.getBEIDEntry("BoundaryName", mapId, BEID)
```

Returns the BEID entry itself.

```
Ritter.getBEIDEntry("BoundaryName", mapId, BEID).event()
```

Returns:

* The active Game_Event (if spawned)
* The unspawned saved event data
* The preload object (if not yet spawned)

This gives full control over events at all times.

---

## 6. Recommended Map Design Practices

### 6.1 Keep editor events minimal

Use editor events only for:

* System or scene triggers
* Map-level logic

All population objects (Transfer Events, Trigger events, NPCs, enemies, nodes, props) should be boundary-spawned.

### 6.2 Use Region IDs for placement

Recommended for defining:

* Enemy zones
* Resource spawn areas
* Environmental decoration
* Safe/no-spawn zones
* Rare spawn positions

### 6.3 Use both Spawn and Unspawn boundaries

Always pair:

* **Spawn boundary** (inner)
* **Unspawn boundary** (slightly larger)

This ensures:

* Clean population cycling
* Stable event counts
* Consistent BEID behavior

### 6.4 Moderate event density

Recommended `maxEvents` per map:

* **Small:** 10–20
* **Medium:** 20–40
* **Large:** 40–80
* **Very large/open-world:** 80–200

---

## 7. Why Preloaded Saved Events Are Best Practice

Preloaded Saved Boundary Events offer:

* ✔ Persistent identity (BEID)
* ✔ Perfect restoration
* ✔ No duplication
* ✔ Works whether spawned or unspawned
* ✔ Engine-friendly performance
* ✔ Predictable event counts
* ✔ Long-term stability
* ✔ Automatic event recycling
* ✔ Safe map transfers
* ✔ Ideal for simulations or open worlds

This is the recommended workflow for any persistent or repeatable gameplay system.

---

## 8. When To Use Other Spawn Methods

Temporary or non-saved spawns are still appropriate for:

* Cutscene props
* One-time events
* Scripted encounters
* Short-lived effects
* Debug or dev utilities
* Cinematic control events

These methods remain fully supported but are not recommended for continuous world population.

---

## 9. Using the Boundary Visualizer

The Boundary Visualizer is recommended for:

* Understanding spawn & unspawn coverage
* Seeing boundary thickness
* Debugging unexpected spawns
* Verifying region placement
* Checking where saved events will activate

Beginners should keep it enabled while learning.

---

## 10. Final Best Practice Workflow

* ✔ Keep editor events minimal
* ✔ Use Spawn + Unspawn boundaries
* ✔ Use regions to guide placement
* ✔ Populate with Preloaded Saved Boundary Events
* ✔ Let BEID handle persistence
* ✔ Let boundaries handle spawning, unspawning, and restoring

Your maps will stay efficient, scalable, and alive, with stable performance across long play sessions.
