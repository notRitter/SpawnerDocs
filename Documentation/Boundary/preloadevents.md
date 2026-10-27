---
title: Preload Boundary Events
parent: Boundary Documentation
nav_order: 4
---

# Preloaded Boundary Events

**Preloading** allows you to prepare saved boundary events before they need to appear on the map.

For example, imagine a village containing 100 NPCs. You might want every NPC to have its own identity, location, and saved information, but you don't want all 100 NPCs running at the same time.

With preloading, you can prepare those NPCs in advance. The Boundary System can then spawn them when they're needed and unspawn them when they're no longer nearby.

This is useful for creating persistent NPCs, enemies, resources, treasure chests, and other objects throughout your game world.

**Preloaded events are saved, but they aren't active events yet.** They don't occupy runtime event slots, execute event commands, or create sprites until they're spawned.

---

## Creating a Preloaded Event

Before preloading events, you need to [create and configure a spawn boundary](scriptcalls.html#1-creating-boundaries).

The following example assumes you've already created a boundary named `"NPCs"` on the current gameplay map.

Use this script call to preload an event at a specific location:

```js
Ritter.preloadEvent(boundaryName, spawnMapId, spawnEventId, x, y);
```

### Arguments

| Argument | Description |
|---|---|
| `boundaryName` | The name of the spawn boundary that will own the event. |
| `spawnMapId` | The map ID containing the event's template. |
| `spawnEventId` | The event ID of the template on that map. |
| `x` | The X coordinate where the event should appear. |
| `y` | The Y coordinate where the event should appear. |

### Example: Preloading an NPC

```js
const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;

Ritter.preloadEvent(
    boundaryName,
    spawnMapId,
    spawnEventId,
    x,
    y
);
```

This prepares a saved NPC using event `1` from template map `2`.

The NPC is assigned to coordinates `(8, 10)` on the **current gameplay map**, not the template map.

The event doesn't appear immediately. Instead, its preload information is stored so the Boundary System can spawn it later.

You can preload as many individual events as your game requires by calling the function for each intended event.

---

## Preloading Events in Regions

You can also preload an event at a randomly selected tile belonging to one or more region IDs.

```js
Ritter.preloadEventRegion(
    boundaryName,
    spawnMapId,
    spawnEventId,
    regions
);
```

### Example: Preloading an NPC in a Region

```js
const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const regions = [1, 3];

Ritter.preloadEventRegion(
    boundaryName,
    spawnMapId,
    spawnEventId,
    regions
);
```

This attempts to create **one preloaded event** at a randomly selected tile marked with region ID `1` or `3` on the current gameplay map.

To use this feature, paint the desired region IDs onto your map using RPG Maker's Region editing tool.

### Important Notes

- Each call attempts to create one preload, not one preload per matching tile.
- Region selection uses matching tiles across the current gameplay map, including locations outside the boundary's current footprint.
- The event will remain preloaded until it is eligible to be spawned or is explicitly restored.

This is particularly useful when you want to distribute persistent NPCs, creatures, or resources across a map without manually choosing every coordinate.

---

## Keeping a Preloaded Event's BEID

Every successfully created preload receives a **Boundary Event ID (BEID)**.

A BEID identifies the same saved event throughout its lifetime, even after it has been spawned, unspawned, and restored.

When you call `Ritter.preloadEvent()`, the function returns the newly created **preload entry**.

That entry contains its assigned BEID in the `_BEID` property.

### Example: Saving a Preloaded NPC's BEID

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

if (preload) {
    $gameVariables.setValue(1, preload._BEID);
    $gameVariables.setValue(2, $gameMap.mapId());
}
```

If preloading succeeds:

- Game variable `1` stores the new NPC's BEID.
- Game variable `2` stores the gameplay map ID where the NPC belongs.

The owning boundary name is `"NPCs"`.

Together, the **boundary name, gameplay map ID, and BEID** identify this particular saved event.

You can use these values later to retrieve its BEID Database entry, update its Self Variables, or interact with it when it's spawned.

**Important:** A BEID is not the same as a regular RPG Maker event ID. Don't pass a BEID directly into `$gameMap.event()`.

For more information, see the [BEID Database](beiddatabase.html).

---

## Assigning Self Variables to Preloaded Events

You can assign [Self Variables](selfvariables.html) to an event immediately after preloading it.

The event doesn't need to be spawned first.

This allows you to give individual NPCs their own quest progress, dialogue states, or other custom information before the player ever encounters them.

### Example: Preparing an NPC's Quest Data

```js
const boundaryName = "NPCs";

const preload = Ritter.preloadEvent(
    boundaryName,
    2,
    1,
    8,
    10
);

if (preload) {
    const mapId = $gameMap.mapId();
    const beid = preload._BEID;

    Ritter.setSelfVariable(
        boundaryName,
        mapId,
        beid,
        "questStage",
        0
    );

    Ritter.setSelfVariable(
        boundaryName,
        mapId,
        beid,
        "hasMetPlayer",
        false
    );
}
```

This prepares an NPC with two Self Variables:

| Self Variable | Starting Value |
|---|---|
| `questStage` | `0` |
| `hasMetPlayer` | `false` |

When the NPC eventually spawns, those values are already associated with its saved identity.

You can also update the NPC's Self Variables while it remains preloaded or after it has been unspawned.

For more examples, see [Boundary Event Self Variables](selfvariables.html).

---

## When Do Preloaded Events Spawn?

Preloading an event doesn't automatically make it appear.

Instead, the event remains stored until the Boundary System brings it into play.

When a spawn boundary is active, it can restore eligible saved events according to its configured area and event limit.

For example, you might have hundreds of NPCs preloaded throughout a large map, but only allow a smaller number to be active around the player.

As the player moves through the world, the Boundary System can spawn nearby events and unspawn events that are no longer needed.

### What Happens After a Preload Spawns?

Once a preloaded event is spawned, it becomes a regular active boundary event with its own runtime event ID.

It also retains its original BEID and any Self Variables assigned to it.

When the event is later unspawned, its saved state is preserved for future restoration.

This allows persistent events to maintain their identities and information as the player explores.

For more information about how different boundaries work, see [Boundary Types](boundarytypes.html).

---

## Initializing Preloaded Events

Sometimes you may want a boundary to populate its area immediately instead of waiting for its normal streaming behavior.

You can do this using:

```js
Ritter.Boundary.initBoundaryEvents(boundaryName);
```

### Example: Activating and Initializing a Boundary

```js
const boundaryName = "NPCs";

Ritter.Boundary.activate(boundaryName);
Ritter.Boundary.initBoundaryEvents(boundaryName);
```

This activates the boundary and initializes eligible saved events within its area, up to its configured event limit.

The initialization process can restore preloaded events and previously saved events that are eligible for that boundary.

### Important Notes

**Initialization does not spawn every saved event on the map.**

It works within the boundary's configured area and available event capacity.

For example, if you've preloaded 500 NPCs across a large map but your active boundary allows only 30 events, initialization won't spawn all 500 NPCs.

Instead, it processes eligible saved events within the boundary's area, respecting the configured limit.

This is useful when setting up an initial population for a village, dungeon, or other area.

---

## Restoring a Specific Event

If you want to restore one particular saved event instead of initializing a boundary population, you can use its BEID.

```js
Ritter.restoreByBEID(boundaryName, BEID);
```

### Example

```js
const boundaryName = "NPCs";
const beid = $gameVariables.value(1);

Ritter.restoreByBEID(boundaryName, beid);
```

This attempts to restore the specified saved event on the **current gameplay map** at its stored location.

Unlike normal boundary streaming, explicit BEID restoration can operate independently of the boundary's area and automatic event limit.

Use it when you specifically want an individual saved event to appear, and make sure its location and the current active event population are appropriate.

For more information, see [Restoring Events by BEID](beiddatabase.html#restoring-events-by-beid).

---

## Placement and Preloading Tips

Here are a few things to keep in mind when preparing persistent event populations.

### Use Valid Templates and Coordinates

Make sure the template map and event IDs point to an existing event template.

When preloading at specific coordinates, use locations within the current gameplay map's boundaries.

Preloading doesn't perform every passability or live-event collision check that might matter when the event eventually appears.

### Check That Preloading Succeeded

A successful preload call returns the newly created preload entry.

Always check the result before attempting to use its `_BEID`.

```js
const preload = Ritter.preloadEvent("NPCs", 2, 1, 8, 10);

if (preload) {
    const beid = preload._BEID;
    console.log("Created preload with BEID:", beid);
}
```

If the preload cannot be created, don't assume that a new BEID was assigned.

If you're storing BEIDs in game variables, remember that a failed preload won't automatically clear values left over from an earlier attempt.

### Avoid Creating Duplicate Populations

Preloading is intended to prepare persistent events, not recreate the same population every time the player visits a map.

For example, if you preload 50 villagers when starting a new game, you generally shouldn't run the same preload setup again every time the player enters their village.

Those villagers already have saved identities and can be restored through the Boundary System.

Use your own initialization conditions to ensure each intended persistent event is created only when needed.

### Preloads Don't Need to Spawn Before Being Saved

A preloaded event can remain in storage without ever appearing on the map.

It doesn't need to spawn once before becoming persistent.

Its saved identity and information can be preserved through game saves and loaded again later.

---

## Preloads and Game Saves

Preloaded events are part of the Boundary System's persistent event data.

Their identities and stored information are retained through successful game saves.

When loading a saved game:

- Events that were active when the game was saved are restored through the normal save-loading process.
- Other saved events, including preloads that have never appeared, remain available for boundary streaming or explicit restoration.
- Saved events retain their BEIDs and associated Self Variables.

If you're using external save storage, remember to keep the necessary external save files together with your game's regular save data.

For details, see [External Save Backups and Transfers](savedboundaryevents.html#external-save-backups-and-transfers).

---

## Summary

Preloading allows you to prepare persistent events throughout your game world without making them all active at once.

With preloaded boundary events, you can:

- Prepare NPCs, enemies, resources, and other persistent objects in advance
- Place events at specific coordinates or randomly selected region tiles
- Assign each event a persistent BEID
- Store custom information using Self Variables
- Allow boundaries to spawn events when they're needed
- Restore specific saved events directly by BEID
- Preserve preloaded events through game saves, even if they've never spawned

**Preloading prepares the event. The Boundary System decides when it needs to be active.**

This makes it possible to create large populations of persistent events while keeping the number of actively running events manageable.
