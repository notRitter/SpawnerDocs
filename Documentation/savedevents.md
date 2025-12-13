---
title: Saved Events
parent: Spawner Documentation
nav_order: 5
---

# Saved Events

## Overview

**Saved Events** provide a simple, manual persistence system for events spawned using the **Ritter Ultimate Event Spawner**.

These saved events:

* Retain their original **eventId**
* Are managed directly on the map
* Can be unspawned and restored explicitly through script calls

---

## Core Characteristics

Saved events have the following properties:

* Retain the same `eventId` for their entire lifetime
* Can be unspawned and restored manually
* Persist unless explicitly destroyed

---

## Saving Events

When an event is spawned as a **saved event**:

* The event’s full state is preserved
* The event remains associated with its `eventId`
* The event can be safely unspawned and restored later

Saved events remain accessible as long as their saved data is retained.

---

## Unspawning Saved Events

Saved events may be unspawned using:

```js
Ritter.unspawnEvent(eventId);
```

By default, this:

* Unspawns the event
* Preserves its saved data
* Leaves the erased event instance on the map

This allows the event to be restored later without data loss.

---

## Destroying Saved Event Data

If you want to permanently remove a saved event and recycle it, use:

```js
Ritter.unspawnEvent(eventId, true);
```

When `removeSave` is set to `true`:

* The saved event data is destroyed
* The unspawned event is added to the **event recycling pool**
* The eventId becomes available for reuse

This ensures that event slots are not wasted.

---

## Recycling Behavior

The Event Spawner includes an internal **unspawned event pool**:

* Destroyed saved events are returned to the pool
* Future spawns may reuse recycled eventIds
* This reduces map event bloat and improves performance

If `removeSave` is `false`, the erased event:

* Remains on the map
* Is **not** recycled
* Preserves its saved state

---

## Restoring Saved Events

Unspawned Saved events can be restored at any time using:

```js
Ritter.restoreUnspawnedEvent(eventId);
```

When called:

* The previously unspawned saved event is restored to the map
* The original `eventId` is preserved
* All saved state (self switches, variables, runtime data) is reapplied

This function only applies to **Saved Events** that were unspawned **without** destroying their saved data.


---

## State Retention

Saved events retain:

* Event pages and commands
* Self switches
* Self variables
* Runtime state

As long as saved data is not destroyed, the event resumes exactly where it left off.

---

## Summary

* Saved events retain their eventIds
* Events can be unspawned and restored manually
* `removeSave = true` destroys saved data and recycles the event
* `removeSave = false` preserves state and identity

Saved Events provide a simple, efficient persistence system for spawned events.
