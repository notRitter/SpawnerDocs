---
title: Script Calls
parent: Spawner Documentation
nav_order: 3
---

# Script Calls

## Spawn Event
Spawns or recycles an event on the game map on to x,y coordinates by creating a copy of a template event located on a spawn map.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-event)

```javascript
Ritter.spawnEvent(mapId, eventId, x, y, save, spawnOnEvents)
```

* **mapId**: The ID or name of the map containing the event.
* **eventId**: The ID or name of the event to spawn.
* **x**: X coordinate to place the event on the map.
* **y**: Y coordinate to place the event on the map.
* **save**: `true` to save the event for restoration on re-entering the map, `false` for a temporary event.
* **spawnOnEvents**: `true` to allow spawning on top of other events, `false` otherwise.

---

### Examples

```javascript
// Spawn event and save
Ritter.spawnEvent(42, 15, 8, 16, true)

// Spawn temporary event
Ritter.spawnEvent(42, 15, 8, 16)

// Spawn using names
Ritter.spawnEvent("SpawnMap", "Slime", 8, 16)

// Store spawned event for further use
let spawnEvent = Ritter.spawnEvent(42, 15, 8, 16, true, true);
let eventId = spawnEvent._eventId;
$gameVariables.setValue(1, eventId);
```

---

## Spawn Event by Region
Spawns or recycles an event on the game map on to a random tile marked with the provided regionId(s) by creating a copy of a template event located on a spawn map.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-event-region)
```javascript
Ritter.spawnEventRegion(mapId, eventId, regions, save, spawnOnEvents)
```

* **regions**: Region IDs to randomly place the event. Use an array for multiple options, e.g., `[4, 8, 15]`.

### Examples

```javascript
// Spawn on a specific region and save
Ritter.spawnEventRegion(4, 8, 15, true)

// Spawn on random region from list temporarily
Ritter.spawnEventRegion(4, 8, [15,16,23,42])

// Store spawned event
let spawnEvent = Ritter.spawnEventRegion(42, 15, [8,4], true, true);
let eventId = spawnEvent._eventId;
$gameVariables.setValue(1, eventId);
```

---

## Spawn Event by Terrain Tag
Spawns or recycles an event on the game map on to a random tile marked with the provided terrain tag(s) by creating a copy of a template event located on a spawn map.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-event-terrain-tag)
```javascript
Ritter.spawnEventTerrainTag(mapId, eventId, tags, save, spawnOnEvents)
```

* **tags**: Terrain Tag IDs to randomly place the event. Use an array for multiple options.

### Examples

```javascript
// Spawn on Terrain Tag 2 and save
Ritter.spawnEventTerrainTag(4, 8, 2, true)

// Spawn on multiple Terrain Tags temporarily
Ritter.spawnEventTerrainTag(4, 8, [1,2,3,4])

// Store spawned event
let spawnEvent = Ritter.spawnEventTerrainTag(4, 8, [15,16], true, true);
let eventId = spawnEvent._eventId;
$gameVariables.setValue(23, eventId);
```

---

## Transform Event
Transforms an event on the game map on into a copy of a template event located on a spawn map.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#transform-event)

```javascript
Ritter.transformEvent(eventId, mapId, spawnId)
```

* **eventId**: ID of the event on the map to transform.
* **mapId**: ID of the spawn map containing the template event.
* **spawnId**: ID of the template event on the spawn map.

### Example

```javascript
Ritter.transformEvent(1010, 4, 8)
```

---

## Unspawn Event
Unspawns an event on the game map using it's `eventId` and sets the unspawned `Game_Event` object to be **recycled** for future spawns.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#unspawn-event)

```javascript
Ritter.unspawnEvent(eventId, removeSave)
```

* **removeSave**: `true` to remove the event from saved events, `false` or empty to retain.

### Examples

```javascript
// Unspawn event 1000
Ritter.unspawnEvent(1000)
```

```javascript
// Unspawn event 1000 and remove save data
Ritter.unspawnEvent(1000, true)
```

---

## Unspawn Event by Coordinates
Unspawns an event on the game map using `x`,`y` coordinates and sets the unspawned `Game_Event` object to be **recycled** for future spawns.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#unspawn-event-xy)

```javascript
Ritter.unspawnEvent(x, y, removeSave)
```

* **x, y**: Coordinates to check for an event to unspawn.
* **removeSave**: `true` to delete saved event, `false` or empty to retain.

### Examples

```javascript
// Unspawn event at coordinates 4,8
Ritter.unspawnEvent(4, 8)
```

```javascript
// Unspawn event at coordinates 15,16 and remove save data
Ritter.unspawnEvent(15, 16, true)
```

---

## Unspawn All Events
Unspawns all event on the game map sets the unspawned `Game_Event` objects to be **recycled** for future spawns.
 * [Plugin Command Available For MZ](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#unspawn-event)

```javascript
Ritter.unspawnAll(removeSave)
```

* **removeSave**: `true` to delete all saved events, leave empty to retain.

### Examples

```javascript
// Unspawn all events
Ritter.unspawnAll()
```

```javascript
// Unspawn all spawned events and remove save data
Ritter.unspawnAll(true)
```

---

## Last Spawned Event
Obsolete method to get the last spawned events `eventId`

```javascript
$gameMap._lastSpawnEventId
```

* Returns the ID of the most recently spawned event. **(Obsolete, use stored Game_Event object method instead)**
