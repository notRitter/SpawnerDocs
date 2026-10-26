---
title: Script Calls
parent: Boundary Documentation
nav_order: 1
---

# Boundary System: Script Call Documentation

These calls require both plugins, with BoundarySystem above EventSpawner. Run them from map-ready gameplay. Source/template IDs and map coordinates in examples must be adapted to your project.

* [Creating Boundaries](#1-creating-boundaries)
* [Automatic Boundary Processing](#2-automatic-boundary-processing)
* [Boundary Initialization](#3-boundary-initialization)
* [Activating & Deactivating](#4-activating--deactivating-boundaries)
* [Enabling & Disabling Events](#5-enabling--disabling-events)
* [Editing Properties](#6-editing-boundary-properties)
* [Manual Spawning](#7-manual-boundary-spawning)
* [Manual Unspawning](#8-manual-boundary-unspawning)
* [Preloading and BEID Control](#9-preloading-and-beid-control)
* [Saved-data Removal and Boundary Deletion](#10-saved-data-removal-and-boundary-deletion)

## 1. Creating Boundaries

<details open markdown="block">
<summary>Show/Hide Section</summary>

```js
Ritter.Boundary.createSpawnerBoundary(width, height, thickness, name,
    eventId, expandBy, maxEvents, centerx, centery);
```

| Argument | Meaning |
| --- | --- |
| `width`, `height` | Tile dimensions; odd sizes provide a straightforward center. Even sizes are supported but have asymmetric integer anchoring. |
| `thickness` | Boundary edge thickness; use at least `1`. Increasing it grows the outer footprint. |
| `name` | Unique boundary name; do not rename a boundary after assigning persistent entities. |
| `eventId` | `0` for player, a positive runtime ID for an event anchor, or `-1` for a static center. |
| `expandBy` | Player-direction expansion when applicable; default `0`. Use `0` for ordinary persistent streaming unless needed. |
| `maxEvents` | Positive spawn/restoration capacity for a spawn boundary. Default `0` blocks capacity-limited spawning; it is not unlimited. Unspawn handlers do not use the spawn cap. |
| `centerx`, `centery` | Required static center coordinates when `eventId` is `-1`; omit for player/event anchors. |

Large player-anchored boundaries that extend beyond both screen dimensions center using the visible map area. Their center need not equal the player's tile near map edges.

Creation sets geometry and saved-event storage. Configure an auto handler next to establish map/type settings, even if the handler starts disabled for manual control. The low-level companion `createBoundary` is for standalone coordinate tools and is not a substitute for Spawner boundary setup.

```js
Ritter.Boundary.createSpawnerBoundary(11, 9, 1, "NPCs", 0, 0, 100);
Ritter.Boundary.addAutoHandler("NPCs", 2, [1], "FillIn", 10, true, false, "Movement");
```

Static example:

```js
Ritter.Boundary.createSpawnerBoundary(5, 5, 1, "Garden", -1, 0, 20, 8, 10);
Ritter.Boundary.addAutoHandler("Garden", 2, [1], "FillIn", 10, false, false, "WaitTime");
```

Use a larger outer unspawn boundary for streamed populations. Size each axis independently and allow space for thickness/expansion. Check actual tile coverage rather than using a width-plus-height formula as a radius.

</details>

## 2. Automatic Boundary Processing

<details open markdown="block">
<summary>Show/Hide Section</summary>

```js
Ritter.Boundary.addAutoHandler(name, spawnMap, maps, type, wait,
    enabled, boundaries, updateMode);
```

| Argument | Meaning |
| --- | --- |
| `name` | Existing Spawner boundary. |
| `spawnMap` | Numeric template map ID for spawn handlers; `0` is sufficient for unspawn-only handlers. |
| `maps` | Gameplay map IDs, such as `[1, 3]`. Configure matching template rules if using generated fresh spawns. |
| `type` | `SpawnOn`, `SpawnIn`, `FillOn`, `FillIn`, `UnspawnOn`, or `UnspawnIn`. |
| `wait` | Frame interval used by `WaitTime`. It is not a cooldown for Movement mode. |
| `enabled` | Initial activation on configured maps. Later `activate`/`deactivate` controls the running boundary. |
| `boundaries` | `false` for spawn handlers; flat array of exact owning-boundary names for unspawn handlers. Legacy single nested-array command data is also accepted. |
| `updateMode` | `"WaitTime"` by default, or `"Movement"`. Pass the exact spelling. |

Movement processing is triggered by player steps and jump sweep handling. WaitTime processing is driven by map updates. All six types already existed in the published baseline; the explicit per-handler mode argument is new in v2.1.

A handler checks saved/preloaded entries before generating fresh random template events. Preload-only setups can use an otherwise empty fresh-spawn rule table. To generate new random events, configure the template's first-page [BoundaryEventSetup metadata](plugincommands.html#boundary-event-setup).

```js
Ritter.Boundary.createSpawnerBoundary(15, 13, 2, "NPC Cleanup", 0, 0, 0);
Ritter.Boundary.addAutoHandler("NPC Cleanup", 0, [1], "UnspawnOn", 10,
    true, ["NPCs"], "Movement");
```

</details>

## 3. Boundary Initialization

```js
Ritter.Boundary.initBoundaryEvents(boundaryName);
```

For an **active spawn boundary**, restores eligible saved clones/preloads throughout its interior up to `maxEvents`, including entries not yet on a FillOn/SpawnOn edge. It does not generate a fresh random population for every empty tile. It has no effect while that boundary is inactive.

```js
Ritter.Boundary.activate("NPCs");
Ritter.Boundary.initBoundaryEvents("NPCs");
```

Use it after creating a population when immediate interior availability is wanted. Do not add repeated refreshes to compensate for save loading: previously live persistent boundary entities are restored automatically, including engine-requested same-map rebuilding. Dormant entries remain dormant unless streaming or explicit initialization selects them.

## 4. Activating & Deactivating Boundaries

```js
Ritter.Boundary.activate(boundaryName);
Ritter.Boundary.deactivate(boundaryName, unspawnAll);
```

`deactivate(name, false)` stops updates and leaves existing live events in place. `deactivate(name, true)` also unspawns owned live events, retaining saved entities while temporary entities expire. Reactivation does not by itself instantiate every dormant entity; initialize explicitly if wanted. Activation remains subject to configured map/geometry/cap conditions for automatic processing.

## 5. Enabling & Disabling Events

```js
Ritter.Boundary.enableEvent(spawnMap, eventId);
Ritter.Boundary.disableEvent(spawnMap, eventId);
```

These affect template eligibility for generated auto-boundary spawns after template metadata is initialized. They are not a switch for one BEID and do not delete existing saved clones or preloads. Do not assume runtime template enable/disable state is a persistent quest flag; store quest state separately.

## 6. Editing Boundary Properties

```js
Ritter.Boundary.editSpawnerBoundary(boundaryName, property, value);
```

Use numeric values and keys `width`, `height`, `thickness`, `expandBy`, `centerx`, `centery`, `maxEvents`, or `waittime`. Property matching is case-insensitive. Editing geometry invalidates its tile cache. Do not change `name`, ownership lists, saved storage, or BEID fields through arbitrary property editing.

```js
Ritter.Boundary.editSpawnerBoundary("NPCs", "maxEvents", 120);
Ritter.Boundary.editSpawnerBoundary("NPCs", "width", 13);
```

Changing the footprint/cap does not promise immediate relocation or deletion of existing live events. Test the paired unspawn coverage after geometry changes.

## 7. Manual Boundary Spawning

```js
Ritter.spawnEventOnBoundary(mapId, eventId, regions, boundaryName);
Ritter.spawnEventInBoundary(mapId, eventId, regions, boundaryName);
Ritter.spawnFillOnBoundary(mapId, eventId, regions, boundaryName);
Ritter.spawnFillInBoundary(mapId, eventId, regions, boundaryName);
```

Use numeric template map/event IDs and an array of region IDs. The first pair attempts one event; the Fill pair attempts valid matching tiles while capacity permits. These four-argument wrappers create temporary boundary events; they do not expose a saved-event flag or return each newly created entity to the caller. For persistent placement use a preload and, when needed, `restoreByBEID` below.

## 8. Manual Boundary Unspawning

```js
Ritter.unspawnEventOnBoundary(boundaryName);
Ritter.unspawnEventInBoundary(boundaryName);
```

Pass a configured **unspawn boundary**. It removes live events on/in its tiles only when their owning spawn boundary is in its exact target list. It does not clear every map event, every boundary, or dormant records.

For a specific live runtime event:

```js
Ritter.unspawnBoundaryEvent(eventId, removeSave);
```

`Ritter.unspawnBoundaryEvent` is the boundary-specific path. Prefer `Ritter.unspawnEvent` when child-custody cascading is required; it routes boundary-owned events through boundary persistence.

The historical namespace remains available as a compatibility adapter:

```js
Ritter.Boundary.unspawnBoundaryEvent(eventId, removeSave);
```

It validates live boundary ownership/membership and delegates to `Ritter.unspawnEvent`, including child cleanup. `removeSave` defaults to `false`. It returns `undefined`, including when no valid live boundary event is found. Both ID-based forms require a current runtime event ID, not a BEID.

## 9. Preloading and BEID Control

Preloads create persistent saved records without immediately creating a live `Game_Event` or sprite. Use them from map-ready gameplay with an existing configured spawn boundary and valid numeric template IDs. Placement belongs to the **current gameplay map**, which must be included in the boundary's handler maps for normal streaming.

* [Preload at Coordinates](#preload-at-coordinates)
* [Preload by Region](#preload-by-region)
* [Name-based Preload Wrappers](#name-based-preload-wrappers)
* [Use the Returned BEID](#use-the-returned-beid)

### Preload at Coordinates

<details open markdown="block">
<summary>Show/Hide Section</summary>

```js
Ritter.Boundary.preloadSavedEvent(boundary, spawnMapId, spawnEventId, x, y);
```

| Argument | Meaning |
| --- | --- |
| `boundary` | Existing Spawner boundary object from `Ritter.Boundary.getBoundary(name)`, not a name string. Check that it exists first. |
| `spawnMapId` | Numeric source map ID containing the template event. |
| `spawnEventId` | Numeric event ID on that template map. Neither argument resolves names. |
| `x`, `y` | Integer tile coordinates on the current gameplay map. Supply an in-bounds location. |

Returns a plain preload record containing `_BEID`, or `null` if that boundary/map's saved-event tile already contains a preload or saved clone. It does not return a runtime event ID or live event. This is a saved-storage placement check, not a passability or live-event collision check. The helper does not fully validate template IDs or coordinates; a returned record is not proof that its template can later spawn.

### Example: Capture a coordinate preload

Assume `"Static Spawn"` is configured for the current map and template map 2/event 1 exists:

```js
const boundary = Ritter.Boundary.getBoundary("Static Spawn");
if (boundary) {
    const preload = Ritter.Boundary.preloadSavedEvent(boundary, 2, 1, 8, 10);
    if (preload) {
        const beid = preload._BEID;
        $gameVariables.setValue(1, beid);
        $gameVariables.setValue(2, $gameMap.mapId());
    }
}
```

Variable 1 holds the persistent BEID; variable 2 holds its owning gameplay map ID. The owning boundary name must also be retained, here as `"Static Spawn"` in later calls. Reserve these variables for this example in your project.

</details>

### Preload by Region

<details open markdown="block">
<summary>Show/Hide Section</summary>

```js
Ritter.Boundary.preloadSavedEventRegion(boundary, spawnMapId, spawnEventId, regions);
```

The first three arguments match coordinate preloading. `regions` must be an **array of numeric region IDs**, such as `[1]` or `[1, 3]`. The lower-level helper sorts this array in place; pass a fresh array when retaining your own ordering matters.

Selects one matching tile across the **current gameplay map**, including outside the boundary's current footprint. It does not fill a region or limit placement to the boundary edge/interior. Returns the preload record on success, or `null` when no candidate tile is available or the selected saved-event tile is occupied when insertion is attempted. Region selection uses a cached candidate list, so always check the returned result.

### Example: Capture a region preload and its BEID

```js
const boundary = Ritter.Boundary.getBoundary("Static Spawn");
if (boundary) {
    const preload = Ritter.Boundary.preloadSavedEventRegion(boundary, 2, 1, [1]);
    if (preload) {
        const beid = preload._BEID;
        $gameVariables.setValue(1, beid);
        $gameVariables.setValue(2, $gameMap.mapId());
        Ritter.setSelfVariable("Static Spawn", $gameMap.mapId(), beid, "met", false);
    }
}
```

Paint region 1 on the gameplay map before running this example. Setting a self variable does not instantiate the preload.

</details>

### Name-based Preload Wrappers

```js
Ritter.preloadEvent(boundaryName, spawnMapId, spawnEventId, x, y);
Ritter.preloadEventRegion(boundaryName, spawnMapId, spawnEventId, regions);
```

These wrappers accept a boundary **name** and return the same preload record or `null` from the lower-level helper. A missing/unknown boundary name logs a warning and returns `undefined`. Unlike the lower-level region helper, `Ritter.preloadEventRegion` accepts either a numeric region ID or an array. The wrappers do not add general template/coordinate validation.

```js
const preloaded = Ritter.preloadEvent("NPCs", 2, 1, 8, 10);
if (preloaded) {
    $gameVariables.setValue(22, preloaded._BEID);
    Ritter.setSelfVariable("NPCs", $gameMap.mapId(), preloaded._BEID, "met", false);
}
```

All four preload calls are synchronous in MV and MZ. Check `if (preload)` before reading `_BEID`. A failed attempt leaves game variables from earlier attempts unchanged; clear your output variable first if you use it as a success indicator. Invalid lower-level arguments, such as a missing boundary object or a scalar `regions`, are not converted to `null` and can throw.

Call once per intended new entity. Repeating successful creation on every map entry can add unwanted persistent entities. Reacquire entries after streaming or loading instead of retaining a preload object as a durable handle. See [preload streaming and save behavior](preloadevents.html).

### Use the Returned BEID

BEIDs are scoped by **boundary name and gameplay map ID**. They are not temporary runtime event IDs, and must not be passed to `$gameMap.event()` or `Ritter.unspawnEvent()`.

Read and update the stored entity without forcing it to spawn:

```js
const beid = $gameVariables.value(1);
const mapId = $gameVariables.value(2);
const entry = Ritter.getBEIDEntry("Static Spawn", mapId, beid);
if (entry && entry.event()) {
    Ritter.setSelfVariable("Static Spawn", mapId, beid, "met", true);
}
```

For deliberate immediate restoration on its owning current map:

```js
const beid = $gameVariables.value(1);
const mapId = $gameVariables.value(2);
if (beid > 0 && $gameMap.mapId() === mapId) {
    const live = Ritter.restoreByBEID("Static Spawn", beid);
    if (live) {
        // live.eventId() is its current runtime ID, not its BEID.
        live.setOpacity(160);
    }
}
```

`restoreByBEID` returns the live event, including an already-live instance, or `undefined` when the entry/entity/scene cannot be resolved. Template or ownership errors can still throw. It bypasses normal automatic geometry/cap selection; choose placement deliberately.

```js
// On the owning gameplay map, retain state while unspawning a live entity.
Ritter.unspawnByBEID("Static Spawn", $gameVariables.value(1), false);
```

`unspawnByBEID` returns `undefined`. With `false` it retains saved state and leaves dormant/preloaded records alone. With `true` it permanently removes the matching saved identity, including a dormant/preloaded record. Both BEID control calls use the current map; they have no map-ID argument. See [BEID Database](beiddatabase.html) for representation checks.

## 10. Saved-data removal and boundary deletion

```js
Ritter.Boundary.removeSaves(boundaryName, gameplayMapId, templateEventId);
Ritter.Boundary.removeSaveXy(x, y, boundaryName, gameplayMapId);
Ritter.Boundary.deleteBoundary(boundaryName);
```

`removeSaves` filters dormant saved/preload records by **template event ID** within the boundary/map; it does not choose a runtime ID. `removeSaveXy` removes dormant records on that tile (map defaults to current). Both update matching BEID records. They are not APIs for moving live entities.

Deleting a Spawner boundary deactivates/unspawns it and removes the owning boundary object. Treat its persistent population as no longer available. Use deliberate game logic and backups when permanently removing saved world content.
