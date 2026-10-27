---
title: Script Calls
parent: Boundary Documentation
nav_order: 1
---

# Boundary System Script Calls

The Boundary System provides script calls for creating boundaries, controlling automatic spawning, preloading persistent events, and managing saved event populations.

These script calls require both **Ritter Event Spawner** and **Ritter Boundary System**, with the Boundary System placed above Event Spawner in the Plugin Manager.

Run these calls during gameplay when the map is ready. Replace the boundary names, map IDs, event IDs, and coordinates in the examples with values appropriate for your project.

## Quick Navigation

1. [Creating Boundaries](#1-creating-boundaries)
2. [Automatic Boundary Processing](#2-automatic-boundary-processing)
3. [Boundary Initialization](#3-boundary-initialization)
4. [Activating and Deactivating Boundaries](#4-activating-and-deactivating-boundaries)
5. [Enabling and Disabling Events](#5-enabling-and-disabling-events)
6. [Editing Boundary Properties](#6-editing-boundary-properties)
7. [Manual Boundary Spawning](#7-manual-boundary-spawning)
8. [Manual Boundary Unspawning](#8-manual-boundary-unspawning)
9. [Preloading and BEID Control](#9-preloading-and-beid-control)
10. [Saved Data Removal and Boundary Deletion](#10-saved-data-removal-and-boundary-deletion)

---

## 1. Creating Boundaries

Use `createSpawnerBoundary()` to create a boundary that can manage event spawning and unspawning.

```js
Ritter.Boundary.createSpawnerBoundary(
    width, height, thickness, name,
    eventId, expandBy, maxEvents, centerx, centery
);
```

### Arguments

| Argument | Description |
|---|---|
| `width` | Boundary width in tiles. |
| `height` | Boundary height in tiles. |
| `thickness` | Thickness of the boundary's outer edge. Use at least `1`. |
| `name` | A unique name used to identify the boundary. |
| `eventId` | The event or player that the boundary follows. |
| `expandBy` | Additional expansion based on the player's movement direction. Defaults to `0`. |
| `maxEvents` | Maximum number of active events allowed by the boundary's automatic spawning and restoration. |
| `centerx` | X coordinate for a static boundary. |
| `centery` | Y coordinate for a static boundary. |

### Choosing What the Boundary Follows

The `eventId` argument determines how the boundary is positioned.

| Value | Behavior |
|---|---|
| `0` | Follow the player. |
| Positive event ID | Follow the specified runtime event. |
| `-1` | Remain at a fixed location using `centerx` and `centery`. |

For most player-centered streaming systems, use `0`.

For a stationary area, such as a garden or fixed enemy spawn zone, use `-1` and provide the center coordinates.

### Example: Creating a Player-Following Boundary

```js

const name = "NPCs";
const width = 11;
const height = 9;
const thickness = 1;
const eventId = 0; // 0 = Player, -1 = Static, or a runtime event ID
const expandBy = 0;
const maxEvents = 100;
const centerx = 0; // Used when eventId is -1
const centery = 0; // Used when eventId is -1

Ritter.Boundary.createSpawnerBoundary(
    width, height, thickness, name,
    eventId, expandBy, maxEvents, centerx, centery
);

```

This creates a boundary named `"NPCs"` that follows the player.

Its dimensions are `11 × 9` tiles, with an edge thickness of `1` and an automatic event capacity of `100`.

The boundary exists, but it still needs an automatic handler if you want it to process spawning or unspawning automatically.

### Example: Creating a Static Boundary

```js

const name = "Garden";
const width = 5;
const height = 5;
const thickness = 1;
const eventId = -1; // Static boundary
const expandBy = 0;
const maxEvents = 20;
const centerx = 8;
const centery = 10;

Ritter.Boundary.createSpawnerBoundary(
    width, height, thickness, name,
    eventId, expandBy, maxEvents, centerx, centery
);

```

This creates a `5 × 5` boundary centered at coordinates `(8, 10)`.

Unlike a player-following boundary, it remains in that location.

### Important Notes

- Odd-numbered widths and heights provide a straightforward center. Even sizes are also supported, but their tile alignment is slightly asymmetric.
- Increasing `thickness` expands the outer boundary footprint.
- `maxEvents` defaults to `0`, which does **not** mean unlimited spawning. A capacity of `0` prevents capacity-limited spawning.
- Avoid renaming a boundary after assigning persistent saved events to it.
- Large player-following boundaries that exceed both screen dimensions may use the visible map area for positioning, so their center is not always exactly the player's tile near map edges.

For automatic spawning, you'll normally create a boundary and then configure its handler using the next section.

---

## 2. Automatic Boundary Processing

Automatic handlers determine **what a boundary does**, which maps it operates on, and when it processes events.

```js
Ritter.Boundary.addAutoHandler(
    name, spawnMap, maps, type, wait,
    enabled, boundaries, updateMode
);
```

### Arguments

| Argument | Description |
|---|---|
| `name` | Name of an existing Spawner boundary. |
| `spawnMap` | Template map ID used for generating events. Use `0` for an unspawn-only handler. |
| `maps` | Array of gameplay map IDs where the handler operates, such as `[1, 3]`. |
| `type` | The type of spawning or unspawning the boundary performs. |
| `wait` | Frame interval used with `"WaitTime"` processing. |
| `enabled` | Whether the handler starts enabled on its configured maps. |
| `boundaries` | Names of the spawn boundaries targeted by an unspawn handler. Use `false` for spawn handlers. |
| `updateMode` | `"Movement"` or `"WaitTime"`. Defaults to `"WaitTime"`. |

### Available Handler Types

| Type | Behavior |
|---|---|
| `SpawnOn` | Attempts to spawn one event along the boundary's edge. |
| `SpawnIn` | Attempts to spawn one event inside the boundary. |
| `FillOn` | Attempts to populate eligible tiles along the boundary's edge. |
| `FillIn` | Attempts to populate eligible tiles inside the boundary. |
| `UnspawnOn` | Unspawns matching events along the boundary's edge. |
| `UnspawnIn` | Unspawns matching events inside the boundary. |

For a more detailed explanation of these behaviors, see [Boundary Types](boundarytypes.html).

### Update Modes

**Movement**

```js
"Movement"
```

Processes the boundary when the player moves or jumps.

This is useful for boundaries that follow the player and manage nearby populations as the player explores.

**WaitTime**

```js
"WaitTime"
```

Processes the boundary at the specified frame interval.

For example, a `wait` value of `60` represents a 60-frame interval.

The `wait` argument is not a movement-mode cooldown.

### Example: Creating an Automatic NPC Boundary

```js

const boundaryName = "NPCs";
const width = 11;
const height = 9;
const thickness = 1;
const eventId = 0; // 0 = Player, -1 = Static, or an event ID
const expandBy = 0;
const maxEvents = 100;
const centerx = 0;
const centery = 0;

// Automatic Handler Settings
const spawnMapId = 2;
const gameplayMaps = [$gameMap.mapId()];
const type = "FillIn";
const wait = 10;
const enabled = true;
const boundaries = false;
const updateMode = "Movement";

// Create Boundary
Ritter.Boundary.createSpawnerBoundary(
    width, height, thickness, boundaryName,
    eventId, expandBy, maxEvents, centerx, centery
);

// Add Automatic Handler
Ritter.Boundary.addAutoHandler(
    boundaryName, spawnMapId, gameplayMaps,
    type, wait, enabled, boundaries, updateMode
);

```

This creates a player-following boundary and assigns a `FillIn` handler that processes as the player moves.

The handler can restore eligible saved events and generate new events according to its configured template rules.

### Example: Creating an Unspawn Boundary

```js

const boundaryName = "NPC Cleanup";
const width = 15;
const height = 13;
const thickness = 2;
const eventId = 0; // Follow player
const expandBy = 0;
const maxEvents = 0;
const centerx = 0;
const centery = 0;

// Automatic Handler Settings
const spawnMapId = 0; // Not needed for unspawning
const gameplayMaps = [1];
const type = "UnspawnOn";
const wait = 10;
const enabled = true;
const boundaries = ["NPCs"]; // Boundaries to unspawn
const updateMode = "Movement";

// Create Boundary
Ritter.Boundary.createSpawnerBoundary(
    width, height, thickness, boundaryName,
    eventId, expandBy, maxEvents, centerx, centery
);

// Add Automatic Handler
Ritter.Boundary.addAutoHandler(
    boundaryName, spawnMapId, gameplayMaps,
    type, wait, enabled, boundaries, updateMode
);

```

This creates a larger boundary that follows the player and unspawns events belonging to `"NPCs"` when they reach its edge.

The `boundaries` argument must contain the exact names of the owning spawn boundaries you want to target.

You can target multiple spawn boundaries:

```js
["NPCs", "Wildlife", "Resources"]
```

A flat array is recommended. Legacy command data containing a single nested array is also supported.

### Important Notes

Automatic spawn handlers check for eligible saved and preloaded events before generating fresh ones.

If you want automatic generation of new events, configure the template's [Boundary Event Setup](plugincommands.html#boundary-event-setup) information on page 1.

If you're only restoring events that you've already preloaded, automatic generation rules aren't required.

For streamed populations, a common setup uses a smaller spawn boundary inside a larger unspawn boundary. Size each axis to provide sufficient separation, taking boundary thickness and expansion into account.

---

## 3. Boundary Initialization

Use `initBoundaryEvents()` when you want a boundary to restore eligible saved events immediately.

```js
Ritter.Boundary.initBoundaryEvents(boundaryName);
```

### Example

```js
Ritter.Boundary.activate("NPCs");
Ritter.Boundary.initBoundaryEvents("NPCs");
```

This activates the `"NPCs"` boundary and initializes eligible saved events within its interior.

Initialization can restore both preloaded events and previously spawned events that have been saved.

It respects the boundary's configured `maxEvents` capacity.

### Important Notes

- The boundary must be active for initialization to work.
- Initialization processes eligible saved events within the boundary's area.
- It can restore events that aren't currently on the edge used by a `FillOn` or `SpawnOn` handler.
- It does not generate an entirely new random population for every empty tile.
- It does not spawn every saved event across the entire gameplay map.

**Game loading already restores saved boundary events that were active when the game was saved.** You don't need to initialize those events again just because a save was loaded.

For more information, see [Preloaded Boundary Events](preloadevents.html).

---

## 4. Activating and Deactivating Boundaries

You can activate or deactivate boundaries at any time.

```js
Ritter.Boundary.activate(boundaryName);
Ritter.Boundary.deactivate(boundaryName, unspawnAll);
```

### Activating a Boundary

```js
Ritter.Boundary.activate("NPCs");
```

Enables the boundary's automatic processing on its configured maps.

Activation doesn't automatically restore every dormant saved event. Use `initBoundaryEvents()` if you want to initialize an eligible population immediately.

### Deactivating a Boundary

```js
Ritter.Boundary.deactivate("NPCs", false);
```

Stops the boundary's automatic processing but leaves its currently spawned events in place.

To deactivate the boundary and unspawn its active events:

```js
Ritter.Boundary.deactivate("NPCs", true);
```

With `true`, owned live events are unspawned. Persistent saved events retain their saved data, while temporary events are removed.

### Choosing the Right Option

| Call | Result |
|---|---|
| `activate(name)` | Enables automatic processing. |
| `deactivate(name, false)` | Stops processing but leaves live events in place. |
| `deactivate(name, true)` | Stops processing and unspawns owned live events. |

These calls are useful when changing game areas, controlling encounters, or temporarily suspending an event population.

---

## 5. Enabling and Disabling Events

You can enable or disable individual event templates used by automatic boundary spawning.

```js
Ritter.Boundary.enableEvent(spawnMap, eventId);
Ritter.Boundary.disableEvent(spawnMap, eventId);
```

### Example: Disabling an Enemy Template

```js
Ritter.Boundary.disableEvent(2, 5);
```

This disables template event `5` from spawn map `2` for new automatic generation.

To allow it again:

```js
Ritter.Boundary.enableEvent(2, 5);
```

### Important Notes

These calls control **template eligibility for newly generated automatic spawns** after the template's boundary metadata has been initialized.

They do not:

- Delete existing saved events
- Remove previously created preloads
- Disable a particular persistent event by BEID
- Automatically change the state of events already spawned

For persistent quest or NPC information, use [Self Variables](selfvariables.html) instead.

---

## 6. Editing Boundary Properties

You can change certain boundary settings after creating the boundary.

```js
Ritter.Boundary.editSpawnerBoundary(boundaryName, property, value);
```

### Supported Properties

| Property | Description |
|---|---|
| `width` | Boundary width. |
| `height` | Boundary height. |
| `thickness` | Boundary edge thickness. |
| `expandBy` | Directional expansion amount. |
| `centerx` | Static center X coordinate. |
| `centery` | Static center Y coordinate. |
| `maxEvents` | Automatic event capacity. |
| `waittime` | Wait interval used for timed processing. |

Property names are case-insensitive.

### Example: Increasing Event Capacity

```js
Ritter.Boundary.editSpawnerBoundary(
    "NPCs",
    "maxEvents",
    120
);
```

This changes the boundary's maximum automatic event capacity to `120`.

### Example: Changing Boundary Size

```js
Ritter.Boundary.editSpawnerBoundary(
    "NPCs",
    "width",
    13
);
```

This changes the boundary's width to `13` tiles.

### Important Notes

Use numeric values for these properties.

Changing a boundary's size or capacity does not automatically relocate or delete events that are already spawned.

If you change the dimensions of a spawn boundary, check that its associated unspawn boundary still covers the intended area.

This function is not intended for renaming boundaries or modifying saved event identities.

---

## 7. Manual Boundary Spawning

You can manually request spawning through a boundary without configuring an automatic handler.

The Boundary System provides four script calls:

```js
Ritter.spawnEventOnBoundary(mapId, eventId, regions, boundaryName);

Ritter.spawnEventInBoundary(mapId, eventId, regions, boundaryName);

Ritter.spawnFillOnBoundary(mapId, eventId, regions, boundaryName);

Ritter.spawnFillInBoundary(mapId, eventId, regions, boundaryName);
```

### Arguments

| Argument | Description |
|---|---|
| `mapId` | Map ID containing the event template. |
| `eventId` | Event ID of the template on that map. |
| `regions` | Array of eligible region IDs. |
| `boundaryName` | Name of the boundary used for spawning. |

### Spawning One Event

To attempt to spawn one event along the boundary's edge:

```js
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];
const boundaryName = "NPCs";

Ritter.spawnEventOnBoundary(spawnMapId, spawnEventId, regions, boundaryName);
```

To attempt to spawn one event inside the boundary:

```js
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];
const boundaryName = "NPCs";

Ritter.spawnEventInBoundary(spawnMapId, spawnEventId, regions, boundaryName);
```

### Filling a Boundary

To attempt to populate eligible tiles along the boundary's edge:

```js
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];
const boundaryName = "NPCs";

Ritter.spawnFillOnBoundary(spawnMapId, spawnEventId, regions, boundaryName);
```

To attempt to populate eligible tiles inside the boundary:

```js
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];
const boundaryName = "NPCs";

Ritter.spawnFillInBoundary(spawnMapId, spawnEventId, regions, boundaryName);
```

The `FillOn` and `FillIn` calls attempt eligible tiles while the boundary has available capacity.

### Important Notes

These manual four-argument spawning calls create **temporary boundary events**.

They do not provide an argument for making those events persistent, and they do not return each newly spawned event to the caller.

If you want to prepare an individually saved event with a persistent BEID, use [Preloading](preloadevents.html) instead.

---

## 8. Manual Boundary Unspawning

You can manually process unspawning through a configured unspawn boundary.

```js
Ritter.unspawnEventOnBoundary(boundaryName);

Ritter.unspawnEventInBoundary(boundaryName);
```

### Example: Unspawning Along an Edge

```js
Ritter.unspawnEventOnBoundary("NPC Cleanup");
```

This processes matching events along the `"NPC Cleanup"` boundary's edge.

### Example: Unspawning Inside a Boundary

```js
Ritter.unspawnEventInBoundary("NPC Cleanup");
```

This processes matching events inside the boundary.

### Important Notes

These calls expect the name of a configured **unspawn boundary**.

An unspawn boundary only targets live events belonging to the spawn boundaries listed in its configuration.

It does not indiscriminately remove every event on the map, and it does not delete dormant saved event records.

### Unspawning a Specific Runtime Event

If you already know the runtime event ID of a live boundary event, you can use:

```js
Ritter.unspawnBoundaryEvent(eventId, removeSave);
```

For example:

```js
Ritter.unspawnBoundaryEvent(25, false);
```

This uses the Boundary System's event-specific unspawning path.

The `removeSave` argument determines whether persistent saved data should be removed.

| `removeSave` | Behavior |
|---|---|
| `false` | Preserve saved event data when applicable. |
| `true` | Remove the event's persistent saved data. |

If your use case requires child-event cleanup and cascading, prefer the main Event Spawner call:

```js
Ritter.unspawnEvent(eventId, removeSave);
```

This routes boundary-owned events through their boundary persistence handling.

### Compatibility Script Call

The older namespace remains available:

```js
Ritter.Boundary.unspawnBoundaryEvent(eventId, removeSave);
```

This compatibility call validates live boundary ownership and delegates through `Ritter.unspawnEvent()`, including child cleanup.

Its `removeSave` argument defaults to `false`.

It returns `undefined`, including when no valid live boundary event is found.

**Important:** These runtime-ID calls use the event's current RPG Maker event ID, not its BEID.

For persistent events identified by BEID, use `Ritter.unspawnByBEID()` in the next section.

---

## 9. Preloading and BEID Control

Preloading allows you to create a saved boundary event **without making it appear on the map immediately**.

Each successfully created preload receives a persistent Boundary Event ID (BEID).

This allows you to identify, update, restore, and remove individual saved events throughout their lifetime.

Before preloading, create and configure the spawn boundary that will own the event.

### Preload at Coordinates

```js
Ritter.preloadEvent(
    boundaryName,
    spawnMapId,
    spawnEventId,
    x,
    y
);
```

#### Arguments

| Argument | Description |
|---|---|
| `boundaryName` | Name of the existing spawn boundary that will own the event. |
| `spawnMapId` | Map ID containing the event template. |
| `spawnEventId` | Event ID of the template. |
| `x` | X coordinate on the current gameplay map. |
| `y` | Y coordinate on the current gameplay map. |

#### Example: Preloading an NPC

```js
const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;

const preload = Ritter.preloadEvent(
    boundaryName,
    spawnMapId,
    spawnEventId,
    x,
    y
);
```

This prepares a saved event at `(8, 10)` on the current gameplay map.

It does not create a live event or sprite yet.

### Preload by Region

```js
Ritter.preloadEventRegion(
    boundaryName,
    spawnMapId,
    spawnEventId,
    regions
);
```

Instead of choosing coordinates manually, this function attempts to preload one event at a randomly selected tile matching the supplied region IDs.

#### Example

```js
const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];

const preload = Ritter.preloadEventRegion(
    boundaryName,
    spawnMapId,
    spawnEventId,
    regions
);
```

This attempts to create **one preload** at a tile marked with region ID `1` or `3` on the current gameplay map.

The selection can include matching tiles outside the boundary's current footprint.

Calling the function once does not fill every matching region tile.

The name-based region call accepts either a numeric region ID or an array of region IDs.

### Preload Return Values

Both preload functions return the newly created preload entry when successful.

If no suitable placement is available, they return `null`.

If the supplied boundary name is missing or unknown, the name-based calls warn and return `undefined`.

Always check the result before accessing its properties.

### Use the Returned BEID

A successful preload entry contains its persistent BEID in `_BEID`.

```js

const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;

// Game Variables to Store the BEID Reference
const beidVariableId = 1;
const mapIdVariableId = 2;

// Create Preload
const preload = Ritter.preloadEvent(
    boundaryName, spawnMapId, spawnEventId, x, y
);

// Store BEID Reference
if (preload) {
    $gameVariables.setValue(beidVariableId, preload._BEID);
    $gameVariables.setValue(mapIdVariableId, $gameMap.mapId());
}

```

In this example:

- Game variable `1` stores the BEID.
- Game variable `2` stores the gameplay map ID.
- `"NPCs"` is the owning boundary name.

Together, the boundary name, gameplay map ID, and BEID identify the saved event.

A BEID is **not** a regular runtime event ID. Do not pass it directly to `$gameMap.event()`.

### Updating a Preloaded Event

You can use [Self Variables](selfvariables.html) to store information on a preloaded event without spawning it.

For example:

```js

const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;

// Self Variable Settings
const varId = "hasMetPlayer";
const value = false;

// Create Preload
const preload = Ritter.preloadEvent(
    boundaryName, spawnMapId, spawnEventId, x, y
);

// Assign Self Variable
if (preload) {
    Ritter.setSelfVariable(
        boundaryName,
        $gameMap.mapId(),
        preload._BEID,
        varId,
        value
    );
}

```

The event can retain this value when it eventually spawns, unspawns, and returns.

### Restoring an Event by BEID

Use the following call to restore a specific saved event:

```js
Ritter.restoreByBEID(boundaryName, BEID);
```

#### Example

```js
const boundaryName = "NPCs";
const mapId = $gameVariables.value(2);
const beid = $gameVariables.value(1);

if (beid > 0 && $gameMap.mapId() === mapId) {
    const event = Ritter.restoreByBEID(
        boundaryName,
        beid
    );

    if (event) {
        event.setOpacity(160);
    }
}
```

This attempts to restore the saved event on its owning gameplay map.

On success, `restoreByBEID()` returns the live event object, including when the event was already spawned.

If restoration cannot be completed, it may return `undefined`. Invalid saved data can also produce an error.

#### Important Notes

Explicit BEID restoration operates independently of normal boundary streaming geometry and automatic event capacity.

Use it when you deliberately want to bring a particular saved event into play, such as during a cutscene or scripted encounter.

It is not intended as a replacement for normal population streaming.

### Unspawning an Event by BEID

Use the following call to unspawn or permanently remove a saved boundary event:

```js
Ritter.unspawnByBEID(boundaryName, BEID, removeSave);
```

#### Example: Unspawn but Keep Saved Data

```js
Ritter.unspawnByBEID("NPCs", 5, false);
```

If the event is currently spawned, this unspawns it while preserving its saved state.

If the event is already dormant or preloaded, its saved entry remains stored.

#### Example: Permanently Remove the Saved Event

```js
Ritter.unspawnByBEID("NPCs", 5, true);
```

Setting `removeSave` to `true` permanently removes the saved identity, including when the event is dormant or still preloaded.

This also removes its associated persistent information.

`unspawnByBEID()` returns `undefined`.

Both BEID control functions operate on the **current gameplay map** and do not take a map ID argument.

For more information, see the [BEID Database](beiddatabase.html).

### Boundary-object Preload Calls

If you already have a boundary object, you can use these lower-level helpers:

```js
Ritter.Boundary.preloadSavedEvent(
    boundary, spawnMapId, spawnEventId, x, y
);

Ritter.Boundary.preloadSavedEventRegion(
    boundary, spawnMapId, spawnEventId, regions
);
```

You can retrieve a boundary object by name:

```js
const boundary = Ritter.Boundary.getBoundary("NPCs");
```

#### Example: Preloading by Region with a Boundary Object

```js

const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];

// Game Variables to Store the BEID Reference
const beidVariableId = 1;
const mapIdVariableId = 2;

// Get Boundary Object
const boundary = Ritter.Boundary.getBoundary(boundaryName);

// Create Preload
if (boundary) {
    const preload = Ritter.Boundary.preloadSavedEventRegion(
        boundary, spawnMapId, spawnEventId, regions
    );

    // Store BEID Reference
    if (preload) {
        $gameVariables.setValue(beidVariableId, preload._BEID);
        $gameVariables.setValue(mapIdVariableId, $gameMap.mapId());
    }
}

```

These helpers return the preload entry on success or `null` when placement is unavailable.

Unlike the name-based wrapper, the boundary-object region helper requires an **array** of numeric region IDs.

It also sorts the supplied region array in place, so use a copy if your game needs to preserve the original array order.

These lower-level helpers do not perform the same missing-boundary-name checks as the name-based calls. Invalid arguments may throw errors.

All of these preload calls are synchronous in RPG Maker MV and MZ.

### Preloading Tips

- Use valid numeric template map and event IDs.
- Use coordinates within the current gameplay map.
- Preloading does not perform every passability or live-event collision check that may matter when the event eventually spawns.
- Check the returned entry before accessing `_BEID`.
- A failed preload does not automatically clear an old BEID stored in a game variable.
- Run preload setup once for each intended new persistent event, rather than recreating the same population every time the player visits a map.

For a complete walkthrough, see [Preloaded Boundary Events](preloadevents.html).

---

## 10. Saved Data Removal and Boundary Deletion

The Boundary System provides script calls for removing saved event records and deleting boundaries.

```js
Ritter.Boundary.removeSaves(
    boundaryName,
    gameplayMapId,
    templateEventId
);

Ritter.Boundary.removeSaveXy(
    x, y, boundaryName, gameplayMapId
);

Ritter.Boundary.deleteBoundary(boundaryName);
```

### Removing Saved Events by Template

```js
Ritter.Boundary.removeSaves(
    "NPCs",
    1,
    5
);
```

This removes matching dormant saved events and preloads belonging to the specified boundary and gameplay map, filtered by their **template event ID**.

The final argument is the source template event ID, not a runtime event ID or BEID.

### Removing Saved Events at a Location

```js
Ritter.Boundary.removeSaveXy(
    8,
    10,
    "NPCs",
    1
);
```

This removes matching dormant saved records at coordinates `(8, 10)` for the specified boundary and gameplay map.

The gameplay map ID defaults to the current map when omitted.

Both saved-data removal calls update the corresponding BEID records.

These functions are for removing stored event records, not moving live events.

### Deleting a Boundary

```js
Ritter.Boundary.deleteBoundary("NPCs");
```

This deletes the specified Spawner boundary.

Deleting a boundary deactivates and unspawns it, then removes its boundary object.

**Important:** Treat a deleted boundary's persistent event population as no longer available.

If you need to permanently remove a boundary and its associated saved world content, make sure that is the intended result.

For games using external save storage, keep appropriate backups before making irreversible changes to persistent event populations.

See [Saved Boundary Events](savedboundaryevents.html) for more information about persistence and backups.

---

## Final Notes

For most projects, the typical workflow is:

1. Create a spawn boundary using `createSpawnerBoundary()`.
2. Configure its behavior with `addAutoHandler()`.
3. Create a larger unspawn boundary to manage events leaving the active area.
4. Preload persistent events or configure templates for automatic generation.
5. Use BEIDs and Self Variables when individual events need to remember information.
6. Let the Boundary System manage the active population as the player explores.

You can also use the individual script calls whenever you need more direct control.

For additional information and examples, see:

- [Boundary Types](boundarytypes.html)
- [Boundary Plugin Commands](plugincommands.html)
- [Preloaded Boundary Events](preloadevents.html)
- [BEID Database](beiddatabase.html)
- [Boundary Event Self Variables](selfvariables.html)
- [Saved Boundary Events](savedboundaryevents.html)
