---
title: Saved Boundary Events
parent: Boundary Documentation
nav_order: 3
---

# Saved Boundary Events

## Overview

**Saved Boundary Events** are the foundation of event persistence in the Ritter Ultimate Event Spawner’s **Boundary System**.

A saved boundary event represents an event that is:

* Permanently tracked by the Boundary System
* Associated with a unique **Boundary Event ID (BEID)**
* Accessible at any time through the **BEID Database**
* Fully persistent whether the event is currently spawned or unspawned

Unlike traditional RPG Maker events, saved boundary events are **not tied to a single eventId**. Instead, they are tracked intelligently across spawn and unspawn cycles using BEIDs.

---

## Why Saved Boundary Events Need BEID

When using the Boundary System, an event’s `eventId` is **not stable**:

* It can change when events are dynamically spawned
* It may be reassigned after unspawning and respawning
* This is necessary for event streaming

Because of this, the Boundary System introduces the **Boundary Event ID (BEID)**:

* A persistent, unique identifier
* Independent of map eventIds
* Stable across the entire lifetime of a boundary event

Saved boundary events use BEIDs to ensure that:

* Event state is never lost
* Events can be found even when unspawned
* Persistence logic remains reliable and deterministic

(See **BEID Database Documentation** for details.)

---

## Core Characteristics

Saved boundary events have the following defining traits:

* **Persist forever** unless explicitly removed
* Retain **all event data and state**
* Continue to exist even when not spawned
* Are tracked by BEID, not eventId
* Can be accessed through the BEID Database at any time

Spawning and unspawning only affects the **physical presence** of the event on the map and never its identity or stored data.

---

## Coordinate-Based Saving

Saved boundary events are stored using **map tile coordinates**:

* Each saved event is bound to an `(x, y)` tile location
* Multiple saved events may exist on the **same tile**
* There is no limit to how many events can be saved per tile

When a spawn boundary passes over a tile:

* All saved boundary events associated with that tile are processed
* Every saved event on that tile is spawned in a **single boundary pass**

This allows dense or layered event setups without requiring multiple boundaries or complex logic.

---

## Boundary-Level Event Databases

Each spawn boundary maintains its **own saved events database**:

* Saved events belong to a specific boundary
* BEIDs are unique within the boundary system
* Boundaries do not share saved event databases

This design ensures:

* Clean separation between boundary-managed areas
* Predictable event ownership

---

## Spawning and Unspawning Behavior

Saved boundary events transition cleanly between states:

### When Spawned

* A real map event is created
* A temporary `eventId` is assigned
* The event is assigned it's permanent BEID

### When Unspawned

* The map event is removed
* All event data and state remain saved
* The BEID entry remains active and accessible

At no point is the saved boundary event lost or invalidated.

---

## Event State Retention

Saved boundary events retain:

* Self switches
* Self variables
* Custom runtime data
* Script-attached metadata

The only value that changes across spawn cycles is the **map eventId**, which is why BEIDs are required for intelligent tracking.

---

## Accessing Saved Boundary Events

Saved boundary events can be accessed:

* While spawned
* While unspawned
* Before initial spawn
* After multiple spawn/unspawn cycles

Access is always performed via the **BEID Database**, not by eventId.

This allows systems such as:

* Persistent NPC logic
* Quest state tracking
* Dynamic world changes
* Cross-map references

(See **BEID Database Documentation** for usage details.)

---

## Relationship to Preloaded Events

Preloaded events are a **special case** of saved boundary events:

* They begin life as metadata-only entries
* They are stored in the same saved event database
* They tell the boundary to spawn the event when needed
* Once spawned, they behave exactly like other saved boundary events

From that point onward, preload-origin events follow the same persistence and BEID rules.

---

## Best Practices

* Treat saved boundary events as **permanent world entities**
* Always interact with them using BEIDs
* Avoid relying on eventIds for long-term logic
* Use BEID to get events current eventId
* Use preloading for large or performance-sensitive maps

Saved boundary events are designed to scale safely and predictably across large projects.

---

## Summary

* Saved boundary events are permanently persistent
* Each has a unique BEID
* Events are stored by tile coordinates
* Multiple events can exist on a single tile
* Spawning/unspawning does not affect identity or data
* BEIDs enable intelligent tracking beyond eventIds

Saved boundary events form the backbone of the Boundary System’s persistence model.
