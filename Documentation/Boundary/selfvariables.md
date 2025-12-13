---
title: BEID Self Variables
parent: Boundary Documentation
nav_order: 6
---

# Boundary Event Self Variables

## Overview

**Boundary Event Self Variables** provide a powerful way to store **persistent, per-event data** directly on boundary-managed events.

They function similarly in spirit to RPG Maker’s self switches, but are far more flexible:

* Each boundary event can store **any number of self variables**
* Variables persist across spawn and unspawn cycles
* Data remains accessible whether the event is spawned or unspawned
* Values are tracked using the event’s **BEID**, not `eventId`

This makes self variables ideal for complex, stateful boundary events.

---

## What Are Self Variables?

A **self variable** is a custom data value attached directly to a specific boundary event.

Unlike:

* Game variables (shared across the entire game)

Boundary self variables are:

* Scoped to a **single boundary event**
* Persistent for the lifetime of that event
* Independent of spawning state

Each boundary event maintains its own internal self variable storage.

---

## Persistence & Lifecycle

Boundary event self variables:

* Exist as part of the saved boundary event data
* Persist even when the event is unspawned
* Remain intact when the event is respawned
* Are available immediately after preload resolution

Because they are tied to BEIDs, self variables remain stable even though the event’s map `eventId` may change.

---

## Setting Self Variables

Use the following script call to set a self variable on a boundary-managed event:

```js
Ritter.setSelfVariable(boundaryName, mapId, BEID, varId, value);
```

### Parameters

* `boundaryName` — Name of the spawn boundary that owns the event
* `mapId` — Map ID the event belongs to
* `BEID` — Boundary Event ID of the event
* `varId` — Self variable identifier (number or string)
* `value` — Value to store (any valid JavaScript type)

When a self variable is set:

* The value is stored directly on the boundary event
* The data persists automatically
* The value is available even if the event is unspawned

---

## Getting Self Variables

Use the following script call to retrieve a self variable from a boundary-managed event:

```js
Ritter.getSelfVariable(boundaryName, mapId, BEID, varId);
```

### Parameters

* `boundaryName` — Name of the spawn boundary that owns the event
* `mapId` — Map ID the event belongs to
* `BEID` — Boundary Event ID of the event
* `varId` — Self variable identifier (number or string)

### Return Value

* Returns the stored value for the given self variable
* Returns `undefined` if the variable does not exist or the event cannot be resolved

This function works regardless of whether the event is spawned or unspawned.

---

## Supported Value Types

Boundary event self variables are not limited to numbers.

Common and supported value types include:

* **Numbers**

  * Counters
  * Progress values
  * State flags

* **Booleans**

  * Interaction toggles
  * Completion states

* **Strings**

  * Dialogue state keys
  * Behavior modes

* **Arrays**

  * Collected item IDs
  * Interaction history

* **Objects**

  * Complex state data
  * Custom runtime metadata

Because self variables are stored directly on the event, they can hold any data type supported by JavaScript.

---

## Common Use Cases

Boundary event self variables are especially useful for:

* Persistent NPC dialogue states
* Tracking per-event quest progress
* Remembering player interactions
* Storing randomized behavior results
* Managing complex event logic across spawns

They eliminate the need for large numbers of global variables and prevent cross-event state collisions.

---

## Preload Compatibility

If a boundary event is preloaded and not yet spawned:

* Self variables can still be set
* Self variables can still be read

In this case, the data is stored on the preload object and automatically carried forward when the event is spawned.

This ensures seamless behavior from preload through full event activation.

---

## Best Practices

* Use self variables instead of global variables for per-event state
* Treat self variable IDs as event-specific namespaces
* Avoid relying on map `eventId` for persistent logic
* Use descriptive IDs to keep logic readable

---

## Summary

* Boundary event self variables provide persistent per-event storage
* Data is tied to BEIDs, not eventIds
* Variables persist across spawn and unspawn cycles
* Any JavaScript-supported data type may be stored
* Self variables work with spawned, unspawned, and preloaded events

Boundary Event Self Variables enable rich, scalable, and maintainable event logic within the Boundary System.
