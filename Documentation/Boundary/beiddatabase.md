---
title: BEID Database
parent: Boundary Documentation
nav_order: 5
---

# BEID Database

## Overview

The **BEID Database** is the core persistence and lookup system that powers the Ritter Ultimate Event Spawner’s **Boundary System**.

It provides a stable, reliable way to:

* Track boundary-managed events across spawn and unspawn cycles
* Access events at any time, regardless of whether they are currently spawned
* Decouple event identity from limitations of RPG Maker `eventId` values

Every **Saved Boundary Event** and **Preloaded Event** is represented by a **BEID Entry** inside a boundary’s BEID Database.

---

## What Is a BEID?

A **BEID (Boundary Event ID)** is a unique identifier assigned to a boundary-managed event.

Unlike `eventId`, a BEID:

* Never changes
* Is persistent for the lifetime of the event
* Can be used even when the event is not spawned

BEIDs are the authoritative way to identify and interact with boundary events.

---

## Boundary-Scoped Databases

Each **spawn boundary** maintains its own **BEID Database**:

* BEID entries belong to a specific boundary
* Databases are not shared between boundaries
* BEIDs are guaranteed to be unique within their boundary system

This design ensures clean separation, predictable ownership, and zero cross-boundary interference.

---

## BEID Entry

A **BEID Entry** is a metadata record representing a single saved boundary event.

It exists whether the event is:

* Fully spawned on the map
* Temporarily unspawned
* Still in preload state

Below is an example of a BEID Entry as viewed in the developer console:

![BEIDEntry](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/BEIDEntry.png)

---

## BEID Entry Properties

The following properties are commonly present on a BEID Entry:

### `BEID`

* **Type:** Number
* **Description:**
  The unique Boundary Event ID assigned to this event.

---

### `boundaryName`

* **Type:** String
* **Description:**
  The name of the spawn boundary that owns this event.

---

### `mapId`

* **Type:** Number
* **Description:**
  The map where this boundary event logically belongs.

---

### `spawnMap`

* **Type:** Number
* **Description:**
  The map ID containing the template event used to spawn this boundary event.

---

### `spawnId`

* **Type:** Number
* **Description:**
  The event ID of the template event on the spawn map.

---

### `spawned`

* **Type:** Boolean
* **Description:**
  Indicates whether the event is currently spawned on the map.

---

### `spawnedEventId`

* **Type:** Number
* **Description:**
  The current `eventId` of the spawned map event.

This value is temporary and may change across spawn cycles.

---

### `lastKnownX` / `lastKnownY`

* **Type:** Number
* **Description:**
  The last known tile coordinates of the event.

These values allow the Boundary System to correctly restore event position.

---

### `event()`

* **Type:** Function
* **Description:**
  Returns the event entity associated with this BEID.

This is the primary way to interact with boundary-managed events.

---

## Accessing a BEID Entry

Use the following API call to retrieve a BEID Entry:

```js
Ritter.getBEIDEntry(boundaryName, mapId, BEID);
```

### Parameters

* `boundaryName` – Name of the spawn boundary
* `mapId` – Map ID the event belongs to
* `BEID` – Boundary Event ID

If the BEID exists, the corresponding BEID Entry is returned.

---

## Accessing the Event via `event()`

Once you have a BEID Entry, call:

```js
const entry = Ritter.getBEIDEntry("Static Spawn", 28, 29720);
const evt = entry.event();
```

The `event()` function will:

* Return the live map event if the event is spawned
* Return the saved boundary event data if unspawned
* Return a preload object if the event has not spawned yet

This allows **safe, unified access** regardless of state.

![BEIDEntryEvent](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/BEIDEntry_event.gif)

---

## Preload Awareness

In some cases, `event()` may return a **preload object** instead of a live event.

![PreloadObjExample](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PreloadObjectExample.png)

You can detect this by checking:

```js
if (evt.isPreloadObj === true) {
  // Event is still in preload state
}
```

* `true` → Preload object (not yet spawned)
* `undefined` → Real event entity

This makes it possible to write logic that works correctly across preload, spawn, and unspawn phases.

---

## Persistence Guarantees

The BEID Database guarantees that:

* BEID Entries always exist for saved boundary events
* Events can be accessed even when unspawned
* Event identity is never lost
* Spawn/unspawn cycles are transparent to user logic

This enables advanced systems such as:

* Persistent NPC behaviors
* Quest state management
* World changes that survive map transitions

---

## Relationship to Saved & Preloaded Events

* **Saved Boundary Events** always have BEID Entries
* **Preloaded Events** begin as metadata-only BEID Entries
* Once spawned, preload events become full saved boundary events

All boundary-managed events are unified under the BEID Database.

---

## Best Practices

* Always store references using BEIDs, not eventIds
* Use `event()` instead of caching event references
* Check for preload objects when working before initial spawn
* Treat BEIDs as permanent identifiers

---

## Summary

* The BEID Database tracks all boundary-managed events
* Each spawn boundary maintains its own database
* BEID Entries exist whether events are spawned or not
* `event()` provides unified access to event entities
* Preload, spawn, and unspawn states are handled transparently

The BEID Database is the backbone of the Boundary System’s persistence and intelligence.
