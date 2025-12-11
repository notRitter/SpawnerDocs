---
title: Installation
parent: Guides
nav_order: 3
---

# Plugin Commands

## Spawn Event

![Plugin Command Image Goes Here]

* **mapId**: The ID or name of the map containing the event.
* **eventId**: The ID or name of the event to spawn.
* **x, y**: Coordinates to place the event on the map.
* **save**: `true` to save for restoration on map re-entry, `false` for temporary.
* **spawnOnEvents**: `true` to allow spawning on top of other events.
* **EventId -> Game Variable**: Game Variable ID to store the EventId.
* **Setter Type**: How to store EventId in the Game Variable.

  * **Set Value As Integer**: Stores EventId as an integer.
  * **Push Value As Array**: Pushes EventId into the variable as an array.

## Spawn Event Region

![Plugin Command Image Goes Here]

* **mapId**: The ID of the map containing the event.
* **eventId**: The ID of the event to spawn.
* **regions**: Region ID(s) to randomly place the event. Use an array for multiple IDs, e.g., `[4,8,15,16,23,42]`.
* **save**: `true` to save for restoration, `false` for temporary.
* **spawnOnEvents**: `true` to allow spawning on top of other events.
* **EventId -> Game Variable**: Game Variable ID to store the EventId.
* **Setter Type**: How to store EventId in the Game Variable.

  * **Set Value As Integer**
  * **Push Value As Array**

## Spawn Event Terrain Tag

![Plugin Command Image Goes Here]

* **mapId**: The ID of the map containing the event.
* **eventId**: The ID of the event to spawn.
* **tags**: Terrain Tag ID(s) to randomly place the event. Use an array for multiple tags, e.g., `[4,8,15,16,23,42]`.
* **save**: `true` to save for restoration, `false` for temporary.
* **spawnOnEvents**: `true` to allow spawning on top of other events.
* **EventId -> Game Variable**: Game Variable ID to store the EventId.
* **Setter Type**: How to store EventId in the Game Variable.

  * **Set Value As Integer**
  * **Push Value As Array**

## Transform Event

![Plugin Command Image Goes Here]

* **eventId**: ID of the event on the map to transform.
* **mapId**: Spawn map ID containing the template event.
* **spawnId**: ID of the template event on the spawn map.

## Unspawn Event

![Plugin Command Image Goes Here]

```javascript
Ritter.unspawnEvent(eventId, removeSave)
```

* **eventId**: ID of the event to unspawn.
* **removeSave**: `true` to remove from saved events, `false` or empty to retain.

## Unspawn Event XY

![Plugin Command Image Goes Here]

* **x, y**: Coordinates to check for the event.
* **removeSave**: `true` to remove from saved events, `false` or empty to retain.

## Unspawn All

![Plugin Command Image Goes Here]

* **removeSave**: `true` to delete all saved events, leave empty to retain.
