---
title: Script Calls
parent: Spawner Documentation
nav_order: 3
---

# Script Calls

The Event Spawner provides script calls for spawning, transforming, unspawning, and restoring events in RPG Maker MV and MZ.

These calls allow you to control spawned events directly through JavaScript, making them useful for event commands, custom systems, and plugins.

Run spawning and transformation calls during gameplay when the map is ready.

**Important Notes:**

- Map coordinates use tiles, not pixels.
- Template map and event IDs must refer to valid templates.
- Spawning calls are synchronous.
- If spawning fails, no event may be returned. Always check the result before accessing its properties or event ID.

## Quick Navigation

- [Spawn Event](#spawn-event)
- [Spawn Event by Region](#spawn-event-by-region)
- [Spawn Event by Terrain Tag](#spawn-event-by-terrain-tag)
- [Spawn Child](#spawn-child)
- [Transform Event](#transform-event)
- [Unspawn Event](#unspawn-event)
- [Unspawn Event by Coordinates](#unspawn-event-by-coordinates)
- [Unspawn All Events](#unspawn-all-events)
- [Restore a Base Saved Event](#restore-a-base-saved-event)

---

## Spawn Event

Use `Ritter.spawnEvent()` to spawn an event at specific coordinates on the current gameplay map.

```js
Ritter.spawnEvent(mapId, eventId, x, y, save, spawnOnEvents);
```

### Arguments

| Argument | Description |
|---|---|
| `mapId` | The template map ID or map name. |
| `eventId` | The template event ID or event name on the template map. |
| `x` | Destination X coordinate on the current map. |
| `y` | Destination Y coordinate on the current map. |
| `save` | Whether the event should be saved for later map visits. Defaults to `false`. |
| `spawnOnEvents` | Whether the event can spawn on tiles occupied by other events. Defaults to `false`. |

### Example: Spawning a Saved NPC

```js
// Spawn Settings
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;
const save = true;
const spawnOnEvents = false;

// Game Variable for the Spawned Event ID
const eventIdVariable = 21;

// Spawn Event
const npc = Ritter.spawnEvent(
    spawnMapId, spawnEventId, x, y, save, spawnOnEvents
);

// Store Runtime Event ID
if (npc) {
    $gameVariables.setValue(eventIdVariable, npc.eventId());
}
```

This example spawns event `1` from template map `2` at coordinates `(8, 10)` on the current gameplay map.

Because `save` is set to `true`, the event is retained as a base saved event for future map visits.

If spawning succeeds, its runtime event ID is stored in RPG Maker game variable `21`.

You can change `eventIdVariable` to whichever game variable you want to use.

### Example: Spawning by Template Name

You can also use template map and event names instead of numeric IDs.

```js
// Spawn Settings
const spawnMapName = "SpawnMap";
const spawnEventName = "Slime";
const x = 12;
const y = 10;
const save = false;
const spawnOnEvents = false;

// Spawn Event
const slime = Ritter.spawnEvent(
    spawnMapName, spawnEventName, x, y,
    save, spawnOnEvents
);
```

This attempts to spawn the event named `"Slime"` from the template map named `"SpawnMap"`.

Since `save` is `false`, this is a temporary spawned event.

**Tip:** Use unique map and event names when spawning by name. If multiple entries have the same name, the first matching entry is used.

### Important Notes

A successful call returns the spawned `Game_Event` object.

If spawning fails, the function may return `undefined`.

Setting `spawnOnEvents` to `true` allows placement on tiles containing other events, but it does not allow spawning on the player's tile or outside the map.

**Base saved events do not receive BEIDs.** They use a different persistence system from the Boundary System's saved events.

For more information, see [Saved Events](savedevents.html).

---

## Spawn Event by Region

Use `Ritter.spawnEventRegion()` to spawn an event at a randomly selected tile belonging to one or more region IDs.

```js
Ritter.spawnEventRegion(mapId, eventId, regions, save, spawnOnEvents);
```

### Arguments

| Argument | Description |
|---|---|
| `mapId` | Template map ID or name. |
| `eventId` | Template event ID or name. |
| `regions` | A region ID or array of eligible region IDs. |
| `save` | Whether the spawned event should be saved. Defaults to `false`. |
| `spawnOnEvents` | Whether spawning on other events is allowed. Defaults to `false`. |

### Example: Spawning an NPC in a Region

```js
// Spawn Settings
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];
const save = false;
const spawnOnEvents = false;

// Game Variable for the Spawned Event ID
const eventIdVariable = 21;

// Spawn Event
const event = Ritter.spawnEventRegion(
    spawnMapId, spawnEventId, regions,
    save, spawnOnEvents
);

// Store Runtime Event ID
if (event) {
    $gameVariables.setValue(eventIdVariable, event.eventId());
}
```

This attempts to spawn one event at a randomly selected tile marked with region ID `1` or `3` on the current map.

You can specify a single region ID:

```js
const regions = 1;
```

Or multiple region IDs:

```js
const regions = [1, 2, 3];
```

### Important Notes

Each call attempts to spawn **one event**, not one event per matching tile.

The system selects a candidate location and checks whether spawning is allowed there. If placement is unavailable, the call may fail rather than repeatedly searching until it succeeds.

Make sure you've painted the intended region IDs onto your gameplay map.

---

## Spawn Event by Terrain Tag

Use `Ritter.spawnEventTerrainTag()` to spawn an event at a randomly selected tile with a matching terrain tag.

```js
Ritter.spawnEventTerrainTag(mapId, eventId, tags, save, spawnOnEvents);
```

### Arguments

| Argument | Description |
|---|---|
| `mapId` | Template map ID or name. |
| `eventId` | Template event ID or name. |
| `tags` | A terrain tag or array of eligible terrain tags. |
| `save` | Whether the event should be saved. Defaults to `false`. |
| `spawnOnEvents` | Whether spawning on other events is allowed. Defaults to `false`. |

### Example: Spawning an Event on Specific Terrain

```js
// Spawn Settings
const spawnMapId = 2;
const spawnEventId = 1;
const tags = [1, 2];
const save = false;
const spawnOnEvents = false;

// Game Variable for the Spawned Event ID
const eventIdVariable = 21;

// Spawn Event
const event = Ritter.spawnEventTerrainTag(
    spawnMapId, spawnEventId, tags,
    save, spawnOnEvents
);

// Store Runtime Event ID
if (event) {
    $gameVariables.setValue(eventIdVariable, event.eventId());
}
```

This attempts to spawn one event on a tile with terrain tag `1` or `2`.

Terrain tags are configured in RPG Maker's Tileset settings.

Standard RPG Maker terrain tags range from `0` through `7`.

### Important Notes

Terrain tags are different from region IDs.

Terrain-based spawning uses the terrain tags assigned to tiles, rather than the region markings painted onto the map.

**Matching a terrain tag does not automatically mean the tile is passable.**

For more information about placement and collision handling, see [Spatial Lookup & Placement](spatial.html).

---

## Spawn Child

<text color="secondary" weight="medium" size="xs">New in Event Spawner v2.1</text>

Use `Ritter.spawnChild()` to spawn an event that belongs to another spawned event.

```js
Ritter.spawnChild(
    parentEventId, mapId, eventId,
    x, y, save, spawnOnEvents
);
```

### Arguments

| Argument | Description |
|---|---|
| `parentEventId` | Runtime event ID of the parent on the current map. |
| `mapId` | Template map ID or name for the child. |
| `eventId` | Template event ID or name for the child. |
| `x` | Child's destination X coordinate. |
| `y` | Child's destination Y coordinate. |
| `save` | Whether the child should be saved. Defaults to `false`. |
| `spawnOnEvents` | Whether the child may spawn on other events. Defaults to `false`. |

### Example: Spawning a Parent and Child

```js
// Parent Settings
const parentMapId = 2;
const parentTemplateId = 1;
const parentX = 8;
const parentY = 10;
const saveParent = true;

// Child Settings
const childMapId = 2;
const childTemplateId = 2;
const childX = 9;
const childY = 10;
const saveChild = false;
const spawnOnEvents = false;

// Spawn Parent
const parent = Ritter.spawnEvent(
    parentMapId, parentTemplateId,
    parentX, parentY, saveParent
);

// Spawn Child
if (parent) {
    const child = Ritter.spawnChild(
        parent.eventId(),
        childMapId,
        childTemplateId,
        childX,
        childY,
        saveChild,
        spawnOnEvents
    );
}
```

This creates a parent event and then attempts to spawn a child associated with that parent.

The child's parent is identified using the parent's current runtime event ID.

### How Parent and Child Ownership Works

When you unspawn a parent through the Spawner's public unspawn system, its owned children are also cleaned up.

If you unspawn a child individually, its ownership link to the parent is removed.

This is useful for events that create or control other events, such as:

- Enemies that summon minions
- NPCs that spawn companions
- Objects that generate smaller objects
- Interactive events that manage temporary effects

### Important Notes

The parent must be an available event on the current gameplay map.

If the parent is unavailable or the child's placement fails, no child event may be returned.

Parent-child ownership is managed through runtime event relationships. It is not a permanent BEID-based relationship that automatically survives arbitrary boundary streaming cycles.

---

## Transform Event

Use `Ritter.transformEvent()` to replace an existing event's template data with another event template.

```js
Ritter.transformEvent(eventId, mapId, spawnId);
```

### Arguments

| Argument | Description |
|---|---|
| `eventId` | Runtime event ID of the event to transform. |
| `mapId` | Template map ID containing the replacement event. |
| `spawnId` | Template event ID to transform into. |

### Example: Transforming an Event

```js
// Transformation Settings
const eventId = 1000;
const spawnMapId = 2;
const spawnEventId = 2;

// Transform Event
Ritter.transformEvent(
    eventId, spawnMapId, spawnEventId
);
```

This transforms runtime event `1000` using event template `2` from map `2`.

You can use transformations to change an event into another type without manually creating a replacement event.

For example, an enemy could transform into a stronger form, or an interactive object could change into a different event template.

### Important Notes

- Transformation uses **numeric IDs** for all three arguments.
- The event's template data and engine event/page state are reinitialized.
- Don't assume arbitrary custom runtime state will be preserved through transformation.
- The public script call does not return the transformed event object.

If you need to access the event after transforming it, retrieve it again:

```js
const event = $gameMap.event(eventId);
```

Avoid keeping references to old event page or template data across a transformation.

---

## Unspawn Event

Use `Ritter.unspawnEvent()` to remove a spawned event using its current runtime event ID.

```js
Ritter.unspawnEvent(eventId, removeSave);
```

### Arguments

| Argument | Description |
|---|---|
| `eventId` | Runtime event ID of the event to unspawn. |
| `removeSave` | Whether retained saved data should also be removed. Defaults to `false`. |

### Example: Unspawning an Event

```js
// Unspawn Settings
const eventId = 1000;
const removeSave = false;

// Unspawn Event
Ritter.unspawnEvent(eventId, removeSave);
```

This unspawns runtime event `1000`.

If the event has persistent saved data, setting `removeSave` to `false` retains that information when applicable.

### Example: Unspawning and Removing Saved Data

```js
// Unspawn Settings
const eventId = 1000;
const removeSave = true;

// Unspawn Event and Remove Saved Data
Ritter.unspawnEvent(eventId, removeSave);
```

This removes the event and its retained saved data.

### How Unspawning Works

The result depends on the type of spawned event.

| Event Type | Behavior |
|---|---|
| Temporary spawned event | Removed and its runtime event ID becomes available for recycling. |
| Base saved event | Can retain its saved data and reserved runtime event ID. |
| Boundary saved event | Retains its BEID when saved, but releases its runtime event ID for recycling. |

### Important Notes

**Use the Spawner's unspawn calls when removing spawned events.**

RPG Maker's regular Erase Event command hides an event, but it does not perform the Spawner's saved-state and runtime ID recycling cleanup.

For saved events, the `removeSave` argument determines whether their persistent information should be retained or removed.

---

## Unspawn Event by Coordinates

You can also unspawn an event by specifying its map coordinates.

This uses a different form of `Ritter.unspawnEvent()`.

```js
Ritter.unspawnEvent(x, y, removeSave);
```

### Example: Unspawning an Event at a Tile

```js
// Unspawn Settings
const x = 8;
const y = 10;
const removeSave = false;

// Unspawn Event at Coordinates
Ritter.unspawnEvent(x, y, removeSave);
```

This attempts to unspawn an event at coordinates `(8, 10)` on the current gameplay map.

### Important Notes

If multiple events occupy the same tile, this call removes the first matching event selected by the system.

It does **not** automatically unspawn every overlapping event.

If you need to remove a particular event, use its runtime event ID instead.

For persistent boundary events, you can also use their BEIDs through the Boundary System.

### Understanding the Two Unspawn Formats

Both calls use the same function name:

```js
// Unspawn by Runtime Event ID
Ritter.unspawnEvent(eventId, removeSave);

// Unspawn by Map Coordinates
Ritter.unspawnEvent(x, y, removeSave);
```

The function determines which form you're using from the supplied arguments.

**When unspawning by runtime ID, use a boolean for `removeSave`.** A numeric second argument can cause the function to interpret the call as coordinate-based unspawning.

---

## Unspawn All Events

Use `Ritter.unspawnAll()` to process spawned events on the current gameplay map.

```js
Ritter.unspawnAll(removeSave);
```

### Example

```js
// Unspawn Settings
const removeSave = false;

// Unspawn All
Ritter.unspawnAll(removeSave);
```

This processes eligible spawned events on the current map.

Set `removeSave` to `true` if you also want matching retained saved data removed as part of unspawning.

### Important Notes

`unspawnAll()` processes non-erased spawned events whose runtime IDs are at or above the Event Spawner's configured starting event ID.

It does not:

- Purge every saved event throughout the game world
- Remove all dormant boundary records or preloads
- Automatically remove base saved records that are already unspawned
- Include transformed editor events below the configured starting ID

If you need to unspawn a specific event outside that range, use `Ritter.unspawnEvent()` directly.

**Think of this as an operation on the current map's spawned event population, not a complete saved-data reset.**

---

## Restore a Base Saved Event

Use `Ritter.restoreUnspawnedEvent()` to restore a previously unspawned **base saved event**.

```js
Ritter.restoreUnspawnedEvent(eventId);
```

### Example: Restoring a Saved NPC

```js
// Restore Settings
const eventId = 1000;

// Restore Event
Ritter.restoreUnspawnedEvent(eventId);

// Retrieve Restored Event
const event = $gameMap.event(eventId);
```

This attempts to restore a base saved event on the current gameplay map using its reserved runtime event ID.

### Important Notes

Base saved events retain their runtime event ID for later restoration.

This is different from saved boundary events, which use persistent BEIDs and can receive different runtime event IDs when restored.

The restore function:

- Uses a numeric runtime event ID.
- Operates on the current gameplay map.
- Does not provide name-based or coordinate-based lookup.
- Does not return the restored event object directly.

After calling it, you can retrieve the event through `$gameMap.event(eventId)` if restoration succeeded.

### Restoring Boundary Saved Events

If you're working with the Boundary System's persistent saved events, use BEID restoration instead:

```js
Ritter.restoreByBEID(boundaryName, BEID);
```

This restores a saved boundary event using its persistent BEID rather than a previously occupied runtime event ID.

See [Boundary Script Calls](Boundary/scriptcalls.html#9-preloading-and-beid-control) for more information.

---

## Final Notes

The Event Spawner provides several ways to create and manage events, depending on what your game needs.

| Goal | Script Call |
|---|---|
| Spawn at specific coordinates | `Ritter.spawnEvent()` |
| Spawn on a matching region tile | `Ritter.spawnEventRegion()` |
| Spawn on a matching terrain tile | `Ritter.spawnEventTerrainTag()` |
| Spawn an event owned by another event | `Ritter.spawnChild()` |
| Transform an existing event | `Ritter.transformEvent()` |
| Unspawn by runtime event ID | `Ritter.unspawnEvent()` |
| Unspawn by coordinates | `Ritter.unspawnEvent()` |
| Unspawn the current spawned population | `Ritter.unspawnAll()` |
| Restore a base saved event | `Ritter.restoreUnspawnedEvent()` |

For persistent boundary events, preloading, BEIDs, and automatic population streaming, see the [Boundary System Script Calls](Boundary/scriptcalls.html).

For more information about event placement and runtime ID management, see [Spatial Lookup & Placement](spatial.html) and [Saved Events](savedevents.html).
