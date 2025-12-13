---
title: Auto Boundary Types
parent: Boundary Documentation
nav_order: 7
---

# Auto Boundary Types & Behaviors

## Overview

The Boundary System supports multiple **boundary types**, each defining *how* and *where* events are spawned or unspawned when a boundary is evaluated.

Boundary types determine:

* Which tiles are checked
* Whether events are spawned or unspawned
* How saved boundary events are restored
* How boundary thickness affects behavior

Each boundary type has a distinct visual footprint and behavior pattern. The sections below describe each type in detail. Diagrams are provided for visual reference.

---

## Common Boundary Properties

All boundary types share a common set of configuration properties:

* **Type** - Determines the boundary’s behavior
* **Width / Height** - Size of the boundary region
* **Thickness** - Number of tiles the boundary extends outward from its core shape

> **Note:** Thickness always expands *outward* from the base boundary shape and does not reduce the interior area.

---

## Fill On - Spawn Boundary
![FillOnBoundaryType](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/Introduction/FillOnBoundary.png)
* **Type:** `FillOn`
* **Width:** 17
* **Height:** 7
* **Thickness:** 1

### Behavior

The **Fill On** boundary evaluates only the **outer edge** of the boundary shape.

When processed, it:

* Attempts to spawn events on **every tile along the boundary edge**
* Restores any saved boundary events found on those edge tiles
* Spawns new events that match the configured Boundary Event rules

### Thickness

* Thickness extends the evaluated edge **outward**
* Additional rings of edge tiles are included as thickness increases

### Use Cases

* Perimeter-based spawning
* Border guards or environmental edges
* Triggers that activate only when crossing a boundary line

---

## Fill In - Spawn Boundary
![FillInBoundary](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/Introduction/FillInBoundary.png)
* **Type:** `FillIn`
* **Width:** 17
* **Height:** 7
* **Thickness:** 1

### Behavior

The **Fill In** boundary evaluates **every tile inside** the boundary region.

When processed, it:

* Attempts to spawn events on **all interior tiles**
* Restores saved boundary events found within the region
* Spawns new events that meet the boundary’s spawn conditions

### Thickness

* Thickness expands the boundary **outward**, increasing the total evaluated area

### Use Cases

* Area-based population spawning
* Zones filled with NPCs or interactables

---

## Spawn On - Spawn Boundary
![SpawnOnBoundary](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/Introduction/SpawnOnBoundary.png)
* **Type:** `SpawnOn`
* **Width:** 17
* **Height:** 7
* **Thickness:** 2

### Behavior

The **Spawn On** boundary attempts to spawn **a single event** per evaluation.

When processed, it:

* Restores any saved boundary events found on the **edge tiles**
* Selects a random valid tile on the **boundary edge**
* Attempts to spawn one event that meets the boundary’s spawn conditions on that tile


### Thickness

* Thickness expands the boundary edge outward
* The random selection pool increases accordingly

### Use Cases

* Randomized encounters
* Controlled spawning with limited density

---

## Spawn In - Spawn Boundary

**Type:** `SpawnIn`

*(Image: Spawn In boundary visualization)*

### Behavior

The **Spawn In** boundary attempts to spawn **a single event** per evaluation within the boundary interior.

When processed, it:

* Selects a random valid tile **inside** the boundary region
* Attempts to spawn one event on that tile
* Restores any saved boundary events found within the boundary
* Spawns new events that meet the boundary’s spawn conditions

### Thickness

* Thickness expands the interior region outward
* The random selection pool increases accordingly

### Use Cases

* Random encounters within an area
* Low-density population spawning
* Dynamic interior event placement

---

## Unspawn On - Unspawn Boundary

**Type:** `UnspawnOn`

*(Image: Unspawn On boundary visualization)*

### Behavior

The **Unspawn On** boundary evaluates only the **boundary edge** and removes events found there.

When processed, it:

* Unspawns events located on the boundary edge
* Preserves saved boundary event data unless explicitly destroyed

### Thickness

* Thickness expands the evaluated edge outward

### Use Cases

* Cleaning up events when exiting an area
* Despawning perimeter-based encounters
* Managing world density dynamically

---

## Unspawn In - Unspawn Boundary

**Type:** `UnspawnIn`

*(Image: Unspawn In boundary visualization)*

### Behavior

The **Unspawn In** boundary evaluates **all tiles inside** the boundary region.

When processed, it:

* Unspawns all events found within the boundary
* Leaves saved boundary event data intact for future restoration

### Thickness

* Thickness expands the region outward

### Use Cases

* Clearing entire zones
* Resetting populated areas
* Managing large-scale event lifecycle transitions

---

## Summary

| Boundary Type | Action  | Evaluated Area           |
| ------------- | ------- | ------------------------ |
| FillOn        | Spawn   | Boundary edge            |
| FillIn        | Spawn   | Interior tiles           |
| SpawnOn       | Spawn   | One random edge tile     |
| SpawnIn       | Spawn   | One random interior tile |
| UnspawnOn     | Unspawn | Boundary edge            |
| UnspawnIn     | Unspawn | Interior tiles           |

Each boundary type serves a distinct role in controlling event lifecycle behavior. Choosing the correct boundary type is essential for predictable spawning, clean unspawning, and efficient boundary evaluation.

This page serves as a reference for understanding how each boundary type behaves and how visual boundary shapes translate into in-game logic.
