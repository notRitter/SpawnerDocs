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
  * Use `odd numbers`. Boundary needs a center tile, which requires odd numbers.
* **height** - Height of the boundary in tiles.
  * Use `odd numbers`. Boundary needs a center tile, which requires odd numbers.
* **thickness** - Boundary wall thickness in tiles.
  * `1` → Default value.
  * `2` → grows the boundary outward by 1 extra ring of tiles.
  * Larger values increase thickness further.
* **name** - String name used to reference this boundary.
* **eventId** - Determines the boundary anchor:
  * `0` → Boundary follows the **player**
  * Any number >0 → Boundary follows **event with that eventId**
  * `-1` → Boundary anchored to **x,y coordinates** (requires `centerx`, `centery`)
* **expandBy** - Expands boundary thickness on the player’s movement axis to encourage forward-facing spawns.
  * This was created for a project which required the constant spawning of ABS enemies around the player.
    * It increases the probability of spawns ahead of the player, while still allowing spawns to the sides and back.
    * **Be mindful not to overlap connected Spawn & Unspawn boundaries when expanding.**
    * For most use cases keep this set to `0`
* **maxEvents** - Maximum number of active events the boundary may spawn at once.
* **centerx** - (Only when `eventId === -1`) X-coordinate center.
* **centery** - (Only when `eventId === -1`) Y-coordinate center.

---

### Example One: Spawn Boundary Outside Of The Screen Anchored To Player

```js
const width = Graphics.width / $gameMap.tileWidth() + 4; // 2nd ring of tiles outside of the screen
const height = Graphics.height / $gameMap.tileHeight() + 4; // 2nd ring of tiles outside of the screen
const thickness = 2; // Setting 2 provides 1 extra outer ring of tiles.
const name = "Static Spawn"; // name works as your boundary ID
const eventId = 0; // 0: player, >0: eventId, -1: x,y anchor
const expandBy = 0; // Boundary doesn't expand
const maxEvents = 200; // Boundary is limited to 200 actively spawned events

Ritter.Boundary.createSpawnerBoundary(width, height, thickness, name, eventId, expandBy, maxEvents);
```

Creates a spawn boundary named "Static Spawn" tracking **The Player** which is 2 tiles distance outside of the screen, with 2-tile thickness. (Starts at the 1st ring of tiles outside of the screen and covers 2 total rings thickness)

---

### Example Two: Unspawn Boundary Outside Of The Screen Anchored To Player

```js
const width = Graphics.width / $gameMap.tileWidth() + 8; // 4th ring of tiles outside of the screen
const height = Graphics.height / $gameMap.tileHeight() + 8; // 4th ring of tiles outside of the screen
const thickness = 2; // Setting 2 provides 1 extra outer ring of tiles.
const name = "unspawn"; // name works as your boundary ID
const eventId = 0; // 0 achors boundary to player

Ritter.Boundary.createSpawnerBoundary(width, height, thickness, name, eventId);
```

Creates a unspawn boundary named "unspawn" tracking **the Player** 4 tiles distance outside of the screen, with 2-tile thickness.

**Note:** Notice the consideration paid to the `width + thickness` & `height + thickness` from Example One. This unspawn boundary will be used for the "Static Spawn" Boundary. So this boundary is positioned right outside of Example One.

Earlier we assigned these values to the `Static Spawn` Boundary.

```js
const width = Graphics.width / $gameMap.tileWidth() + 4;
const height = Graphics.height / $gameMap.tileHeight() + 4;
const thickness = 2;
```
Proper minimum unspawn boundary distance for Event Streaming.
 * `Spawn Boundary Width + Spawn Boundary Thickness + 2`
 * `Spawn Boundary Height + Spawn Boundary Thickness + 2`
   * `+ 2` means unspawn boundary will be touching the spawn boundary.
   * Increase value for more distance.

For this type of boundary setup you'll always want your unspawn boundary outside of your spawn boundary.

---

```js
const width = 15; // 15 tiles width
const height = 15; // 15 tiles height
const thickness = 1; // 1 thickness is lowest value.
const name = "unspawn"; // name works as your boundary ID
const eventId = 0; // achors boundary to player

Ritter.Boundary.createSpawnerBoundary(width, height, thickness, name, eventId);
```

Creates a unspawn boundary named "unspawn" tracking **the Player** which is 15 width x 15 height with normal thickness.

```js
const width = 11;
const height = 11;
const thickness = 1;
const name = "spawn";
const eventId = 0;
const expandBy = 2;
const maxEvents = 30;
const centerx;
const centery;

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

**Add Auto Handler To "Static Spawn" Boundary**
```js
const name = "Static Spawn"; // Name of the Boundary we are adding the auto handler to
const spawnMap = 29; // Spawn Map ID
const maps = [2,4,6,8]; // List of Map IDs this auto boundary will operate on
const type = "FillOn"; // types: "SpawnOn", "SpawnIn", "FillOn", "FillIn", "UnspawnOn", "UnspawnIn"
const wait = 10; // We won't be using wait time in this example, but we'll set a value anyway.
const enabled = true; // This will ensure the auto boundary is enabled upon creation.
const boundaries = false; // This is used for unspawn boundaries, set to false when working with spawn boundaries.
const updateMode = "Movement"; // This will cause the boundary system to spawn events every time the player moves to a new tile.

Ritter.Boundary.addAutoHandler(name, spawnMap, maps, type, wait, enabled, boundaries, updateMode);
```


---

# 3. Boundary Initialization

## `Ritter.Boundary.initBoundaryEvents(boundaryName)`

Preloads all saved events inside or on a boundary at map load.

Only needed when using:

* `SpawnOn`
* `FillOn`

For `SpawnIn` or `FillIn`, initialization is not required.

Recommended usage: place in an Autorun event that erases itself.

---

# 4. Activating & Deactivating Boundaries

## `Ritter.Boundary.activate(boundaryName)`

Turns a boundary **on**, allowing it to spawn or unspawn events.

## `Ritter.Boundary.deactivate(boundaryName, unspawnAll)`

Turns a boundary **off**.

### Parameters

* **boundaryName** - Name of the boundary.
* **unspawnAll** - `true`/`false` - Whether to immediately unspawn all events belonging to that boundary.

---

# 5. Enabling & Disabling Events

## `Ritter.Boundary.enableEvent(spawnMap, eventId)`

Allows an event to be spawned by auto boundaries.

## `Ritter.Boundary.disableEvent(spawnMap, eventId)`

Prevents an event from being spawned (existing saved versions still restore normally).

---

# 6. Editing Boundary Properties

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

# 7. Manual Boundary Spawning

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

# 8. Manual Boundary Unspawning

## `Ritter.unspawnEventOnBoundary(boundaryName)`

Unspawns any events located **on the boundary edge**.

## `Ritter.unspawnEventInBoundary(boundaryName)`

Unspawns events **inside** the boundary.

### Example

```js
Ritter.unspawnEventInBoundary("unspawn");
```
