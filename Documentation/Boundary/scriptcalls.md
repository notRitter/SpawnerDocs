---
title: Script Calls
parent: Boundary Documentation
nav_order: 1
---

# Boundary System - Script Call Documentation

**Ritter Ultimate Event Spawner & Boundary System**
**Script Call API (Boundary Spawner System)**

This section provides clean, modern, fully-formatted documentation for all **Boundary System Spawner Script Calls**, including creation, automation, spawning, unspawning, editing, and setup parameters.

---

# 1. Creating Boundaries

## `Ritter.Boundary.createSpawnerBoundary(width, height, thickness, name, eventId, expandBy, maxEvents, centerx, centery)`

Creates a Boundary used for spawning or unspawning events.

### Parameters

* **width** - Width of the boundary in tiles.
* **height** - Height of the boundary in tiles.
* **thickness** - Boundary wall thickness in tiles.
* **name** - String name used to reference this boundary.
* **eventId** - Determines the boundary anchor:

  * `0` → Boundary follows the **player**
  * Any number >0 → Boundary follows **event with that eventId**
  * `-1` → Boundary anchored to **x,y coordinates** (requires `centerx`, `centery`)
* **expandBy** - Expands boundary thickness on the player’s movement axis to encourage forward-facing spawns.
* **maxEvents** - Maximum number of active events the boundary may spawn at once.
* **centerx** - (Only when `eventId === -1`) X-coordinate center.
* **centery** - (Only when `eventId === -1`) Y-coordinate center.

### Examples

```js
Ritter.Boundary.createSpawnerBoundary(11, 11, 1, "spawn", 0, 2, 30);
```

Creates an 11×11 player-centered boundary named **spawn**, expands by 2 tiles, max 30 spawned events.

```js
Ritter.Boundary.createSpawnerBoundary(15, 15, 1, "unspawn", 0, 0, 0);
```

Creates a 15×15 player boundary used for unspawning only.

```js
Ritter.Boundary.createSpawnerBoundary(5, 5, 2, "eventBoundary", 50, 0, 5);
```

Creates a boundary tracking **event 50**, with 2-tile thickness and max 5 spawned events.

```js
Ritter.Boundary.createSpawnerBoundary(5, 5, 1, "xyBoundary", -1, 0, 20, 50, 50);
```

Creates a boundary centered at **(50,50)**.

---

# 2. Automatic Boundary Processing

## `Ritter.Boundary.addAutoHandler(name, spawnMap, maps, type, wait, enabled, boundaries, updateMode)`

Enables a boundary to automatically spawn/unspawn events without manual script calls.

### Parameters

* **name** - Boundary name.
* **spawnMap** - Spawn map ID to pull template events from.
* **maps** - List of map IDs this auto boundary may operate on.
* **type** - Auto mode type: 
  * `"SpawnOn"` - [Spawn on](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/boundarytypes.html#spawn-on---spawn-boundary) boundary edge
  * `"SpawnIn"` - [Spawn inside boundary](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/boundarytypes.html#spawn-in---spawn-boundary)
  * `"FillOn"` - [Fill entire boundary edge with events](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/boundarytypes.html#fill-on---spawn-boundary)
  * `"FillIn"` - [Fill entire boundary interior](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/boundarytypes.html#fill-in---spawn-boundary)
  * `"UnspawnOn"` - [Unspawn events on the boundary edge](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/boundarytypes.html#unspawn-on---unspawn-boundary)
  * `"UnspawnIn"` - [Unspawn events inside the boundary](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/boundarytypes.html#unspawn-in---unspawn-boundary)
* **wait** - Frames between auto spawn/unspawn cycles.
* **enabled** - Whether the auto handler starts enabled.
* **boundaries** - *(Unspawn only)* Array of boundary names this unspawn boundary is allowed to unspawn events from.
  * For **spawn boundaries** set this value to `false` in script calls
* **updateMode** - Whether the auto boundary triggers on wait time or player movement
  * `"WaitTime"` - Auto Boundary will use wait time (frames)
  * `"Movement"` - Auto Boundary will trigger when the player lands on a new tile

### Examples:

```js
const name = "Static Spawn"; // Name of the Boundary we are adding the auto handler to
const spawnMap = 29; // Spawn Map ID
const maps = [2,4,6,8]; // List of Map IDs this auto boundary will operate on
const type = "FillOn"; // Will use FillOn boundary type, ideal for event streaming
const wait = 10; // We won't be using wait time in this example, but we'll set a value anyway.
const enabled = true; // This will ensure the auto boundary is enabled upon creation.
const boundaries = false; // This is used for unspawn boundaries, set to false when working with spawn boundaries.
const updateMode = "Movement"; // This will cause the boundary system to spawn events every time the player moves to a new tile.

Ritter.Boundary.addAutoHandler(name, spawnMap, maps, type, wait, enabled, boundaries, updateMode);
```


---

# 3. Boundary Event Setup (Spawn Map Event Metadata)

This plugin command is placed on **Page 1** of any event used by auto boundaries. It executes no code - it is metadata only.

### Properties

* **Boundary List** - Array of boundary names this event is allowed to spawn on.
* **Map List** - Valid game map IDs for spawning the event.
* **RegionId List** - Allowed region IDs.
* **Enabled By Default?** - Whether this event is initially spawnable.
* **Saved Event?** - Whether this event becomes a Saved Boundary Event.

Saved Boundary Events behave differently than normal saved events because:

* They do **not** retain eventId when unspawned (to prevent buildup)
* They are **recycled** when not in use
* All state is still preserved on spawn/unspawn

---

# 4. Boundary Initialization

## `Ritter.Boundary.initBoundaryEvents(boundaryName)`

Preloads all saved events inside or on a boundary at map load.

Only needed when using:

* `SpawnOn`
* `FillOn`

For `SpawnIn` or `FillIn`, initialization is not required.

Recommended usage: place in an Autorun event that erases itself.

---

# 5. Activating & Deactivating Boundaries

## `Ritter.Boundary.activate(boundaryName)`

Turns a boundary **on**, allowing it to spawn or unspawn events.

## `Ritter.Boundary.deactivate(boundaryName, unspawnAll)`

Turns a boundary **off**.

### Parameters

* **boundaryName** - Name of the boundary.
* **unspawnAll** - `true`/`false` - Whether to immediately unspawn all events belonging to that boundary.

---

# 6. Enabling & Disabling Events

## `Ritter.Boundary.enableEvent(spawnMap, eventId)`

Allows an event to be spawned by auto boundaries.

## `Ritter.Boundary.disableEvent(spawnMap, eventId)`

Prevents an event from being spawned (existing saved versions still restore normally).

---

# 7. Editing Boundary Properties

## `Ritter.Boundary.editSpawnerBoundary(boundaryName, param, value)`

Modifies boundary settings at runtime.

### Parameters

* **boundaryName** - Name of the boundary.
* **param** - String key of the property to edit.
* **value** - New value.

### Param keys

* `"width"`
* `"height"`
* `"thickness"`
* `"expandby"`
* `"centerx"`
* `"centery"`
* `"maxevents"`
* `"waittime"`

### Examples

```js
Ritter.Boundary.editSpawnerBoundary("spawn", "width", 25);
Ritter.Boundary.editSpawnerBoundary("spawn", "height", 17);
```

---

# 8. Manual Boundary Spawning

## `Ritter.spawnEventOnBoundary(mapId, eventId, regions, boundaryName)`

Spawns an event on a region **along the boundary edge**.

## `Ritter.spawnFillOnBoundary(mapId, eventId, regions, boundaryName)`

Spawns an event on **every valid tile** along the boundary edge.

## `Ritter.spawnEventInBoundary(mapId, eventId, regions, boundaryName)`

Spawns an event on a **random valid tile inside** the boundary.

## `Ritter.spawnFillInBoundary(mapId, eventId, regions, boundaryName)`

Spawns events on **every valid tile inside** the boundary.

### Shared Parameters

* **mapId** - Spawn map ID
* **eventId** - Template event ID
* **regions** - Array of region IDs
* **boundaryName** - Target boundary

### Example

```js
Ritter.spawnEventInBoundary(4, 8, [15, 16], "spawn");
```

---

# 9. Manual Boundary Unspawning

## `Ritter.unspawnEventOnBoundary(boundaryName)`

Unspawns any events located **on the boundary edge**.

## `Ritter.unspawnEventInBoundary(boundaryName)`

Unspawns events **inside** the boundary.

### Example

```js
Ritter.unspawnEventInBoundary("unspawn");
```
