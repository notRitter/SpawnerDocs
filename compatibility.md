---
title: Compatibility Guide
nav_order: 6
published: false
---

# Developer Compatibility Guide

Ritter Ultimate Event Spawner v2.1 supports RPG Maker MV and MZ and provides dynamic event spawning, recycling, saved events, and optional boundary streaming.

This guide is intended for **RPG Maker plugin developers** who want to make their plugins compatible with dynamically spawned events.

It covers the most important integration considerations, common RPG Maker methods, and practical examples.

**Note:** This guide is not a list of tested third-party plugins. Compatibility depends on the specific plugins, versions, and features being used.

When creating compatibility patches, place them **below the plugins whose methods they modify** in Plugin Manager.

## 1. Event IDs and Recycling

RPG Maker normally assigns event IDs to events placed in the map editor. The Event Spawner also creates events dynamically, allocating and recycling runtime event IDs as needed.

Different types of spawned events handle their IDs differently.

| Event Type | Runtime ID Behavior |
|---|---|
| **Temporary Event** | Its runtime ID can be recycled after unspawning. |
| **Base Saved Event** | Its runtime ID is reserved for later restoration. |
| **Saved Boundary Event** | Uses a persistent BEID. Its runtime ID can change when restored. |
| **Preloaded Boundary Event** | Has a persistent BEID but may not have a live runtime ID yet. |

### Why This Matters

Suppose a plugin stores a reference to event `1000`.

That event is later unspawned, and another event receives the same runtime ID.

If the plugin continues using the old ID, it may accidentally interact with a completely different event.

**Runtime event IDs should not be treated as permanent identities for temporary or boundary events.**

For saved boundary events, use their persistent identity:

```js
const boundaryName = "NPCs";
const mapId = $gameMap.mapId();
const beid = 5;
```

A saved boundary event is identified by its boundary name, gameplay map ID, and BEID.

The same BEID number can exist under different boundaries or on different maps.

For more information, see the [BEID Database](Documentation/Boundary/beiddatabase.html).

## 2. Event Lifecycle and Plugin Hooks

The Event Spawner can create new events, recycle old runtime slots, and restore previously saved events.

These operations don't always follow the same initialization path.

This is important for plugins that extend RPG Maker's event methods.

### Game_Event.initialize

Many RPG Maker plugins use `Game_Event.initialize()` to configure custom event properties.

```js
const _Game_Event_initialize = Game_Event.prototype.initialize;

Game_Event.prototype.initialize = function(mapId, eventId) {
    _Game_Event_initialize.call(this, mapId, eventId);

    // Initialize your plugin's runtime data.
    this._myPluginActive = false;
};
```

This is useful for newly created and recycled events.

However, saved events may be restored without calling `Game_Event.initialize()` again. Fresh spawning can also involve repeated initialization.

**For compatibility:**

- Make initialization safe to repeat.
- Avoid registering the same event multiple times.
- Don't reset persistent information when a saved event is restored.
- Make sure restored events can be recognized without relying exclusively on `initialize()`.

### Game_Event.erase

When events are unspawned, plugins may need to release their own temporary resources.

```js
const _Game_Event_erase = Game_Event.prototype.erase;

Game_Event.prototype.erase = function() {

    // Clean up your plugin's temporary data.
    this._myPluginActive = false;

    _Game_Event_erase.call(this);
};
```

Only clean up information owned by your plugin.

**Important:** RPG Maker's `erase()` method is not a replacement for the Event Spawner's unspawn system.

If your plugin needs to remove a spawned event, use the Spawner's public API:

```js
const eventId = 1000;
const removeSave = false;

Ritter.unspawnEvent(eventId, removeSave);
```

For persistent boundary events, use BEID-based unspawning.

During boundary cleanup, the Spawner may clear the old runtime object's `_BEID` and `_parentBoundary` fields before `erase()` runs. Don't rely on those fields still being available in your erase hook.

### Restoring Runtime Information

Saved events may return without running the normal event constructor or initializer.

If your plugin maintains a cache or registry of active events, make sure it can recognize restored events.

A common RPG Maker integration point is `Scene_Map.onMapLoaded()`:

```js
const _Scene_Map_onMapLoaded = Scene_Map.prototype.onMapLoaded;

Scene_Map.prototype.onMapLoaded = function() {
    _Scene_Map_onMapLoaded.call(this);

    // Refresh your plugin's runtime event information.
    $gameMap.events().forEach(function(event) {
        if (!event._erased) {
            // Register or refresh this live event.
        }
    });
};
```

This example demonstrates a map-load refresh. It does not replace the need to handle events spawned or unspawned while the map remains active.

Keep runtime registration safe to repeat, and avoid unnecessary full-map scans every frame.

## 3. Sprite Compatibility

The Event Spawner can recycle character sprites when events are unspawned and new events are created.

This matters for plugins that attach additional visual elements to event sprites, such as:

- Name labels
- Health bars
- Status icons
- Custom effects

If these elements aren't cleaned up correctly, they may remain attached when a sprite is reused for another event.

### Sprite_Character.setCharacter

`Sprite_Character.setCharacter()` is a useful place to handle custom sprite attachments when the underlying character changes.

```js
const _Sprite_Character_setCharacter =
    Sprite_Character.prototype.setCharacter;

Sprite_Character.prototype.setCharacter = function(character) {

    // Remove a custom label owned by your plugin.
    if (this._myPluginLabel) {
        if (this._myPluginLabel.parent) {
            this._myPluginLabel.parent.removeChild(this._myPluginLabel);
        }

        this._myPluginLabel.destroy({
            children: true,
            texture: false,
            baseTexture: false
        });

        this._myPluginLabel = null;
    }

    _Sprite_Character_setCharacter.call(this, character);

    // Create or refresh your plugin's visual elements as needed.
};
```

This example assumes `_myPluginLabel` is a sprite created and owned by your plugin.

The old label is removed before the character sprite is assigned to another event.

**Important Notes:**

- Only remove or destroy resources owned by your plugin.
- Don't destroy the main character sprite or shared character textures.
- Handle cleanup when the map scene is destroyed, not just when a sprite is rebound.
- Test sprite recycling during animations, balloons, and movement effects.

RPG Maker MV and MZ manage some animation and effect resources differently, so test both engines if your plugin supports both.

## 4. Spatial Lookup and Collision

When the Boundary System is installed, the Event Spawner provides a spatial lookup system for finding events at specific map coordinates.

```js
Ritter.Spatial.eventsAt(x, y);
```

This returns event objects associated with the specified tile on the current gameplay map.

### Example: Finding Events on a Tile

```js
const x = 8;
const y = 10;

const events = Ritter.Spatial.eventsAt(x, y);

events.forEach(function(event) {
    console.log(event.eventId());
});
```

### Supporting Projects Without the Boundary System

If your plugin supports both the base Event Spawner and the Boundary System, check whether the spatial API is available.

```js
const x = 8;
const y = 10;

let events;

if (Ritter.Spatial && Ritter.Spatial.eventsAt) {
    events = Ritter.Spatial.eventsAt(x, y);
} else {
    events = $gameMap.eventsXy(x, y);
}
```

This uses Ritter's spatial lookup when available and RPG Maker's normal event lookup otherwise.

### Important Notes

Spatial lookup identifies events occupying a tile. It does not automatically determine whether an event can move, collide, or spawn there.

Your plugin should still apply its own rules for through settings, priority, triggers, and other conditions.

The spatial system tracks normal event positioning, movement, jumping, and erasure.

If your plugin directly modifies internal coordinates such as `_x` or `_y`, it may bypass normal spatial updates.

**Prefer RPG Maker's normal event positioning and movement methods.**

For custom movement systems, low-level `Ritter.Spatial.track(event)` and `Ritter.Spatial.untrack(event)` methods are available, but these are implementation-level interfaces.

Avoid directly modifying the spatial grid's internal containers.

See [Spatial Lookup & Placement](Documentation/spatial.html) for more information.

## 5. Saved Boundary Event Compatibility

Saved boundary events can be live, dormant, or preloaded.

| State | Description |
|---|---|
| **Live** | The event is active on the map and can run event pages, movement, and graphics. |
| **Dormant** | The event has been unspawned, but its persistent information remains stored. |
| **Preloaded** | The event is prepared for spawning but hasn't appeared yet. |

### Working with Offline Events

Dormant and preloaded events should not be treated as ordinary live map events.

A dormant saved event may have a `Game_Event` prototype, but it isn't currently participating in map processing.

A preload is a metadata object rather than a live `Game_Event`.

Don't call movement, collision, sprite, or interpreter methods on these offline representations.

### Using Self Variables

You can update persistent information without bringing the event live.

```js
const boundaryName = "NPCs";
const mapId = $gameMap.mapId();
const beid = 5;
const varId = "questStage";
const value = 3;

Ritter.setSelfVariable(
    boundaryName, mapId, beid, varId, value
);
```

This works with saved boundary entities, including dormant and preloaded events.

For changes to movement or appearance, work with a verified live event instead.

Directly changing an offline object's coordinates does not automatically update its saved tile location or BEID bookkeeping.

### Avoiding Stale References

A boundary event may receive a different runtime ID when it returns.

Don't permanently cache its old runtime event object, sprite, or event ID.

Instead, store its boundary name, gameplay map ID, and BEID, then resolve the current entity when needed.

This also matters for plugins that store event references for movement routes or interpreter commands.

The Event Spawner handles supported engine-owned references during restoration, but it cannot automatically update arbitrary references maintained by another plugin.

See [BEID Database](Documentation/Boundary/beiddatabase.html) and [Self Variables](Documentation/Boundary/selfvariables.html).

## 6. VisuStella Events & Movement Core

Ritter Ultimate Event Spawner includes compatibility handling for **VisuStella Events & Movement Core**.

When using both systems, configure the load order as follows:

```text
VisuStella Events & Movement Core
Ritter_BoundarySystem
Ritter_EventSpawner
```

The Boundary System must remain above Event Spawner.

### Compatibility Behavior

Ritter includes safeguards for dynamic event lookup and runtime ID allocation when working alongside Events & Movement Core.

These help with:

- Recognizing dynamically spawned events alongside normal map events.
- Avoiding runtime event IDs already occupied by another system.
- Refreshing relevant event caches after spawning, unspawning, and restoration.
- Preventing erased recycling objects from being treated as active events.
- Detecting conflicting live event IDs rather than silently replacing an event.

Ritter does not modify VisuStella's plugin files or take ownership of its private spawned-event records.

**Important:** The two systems maintain their own event-spawning and persistence data. Events created by one system are not automatically managed by the other's saved-event features.

Test the specific versions and movement features used by your project, especially when combining spawning, event movement, page changes, and saved games.

## 7. Common Compatibility Problems

The following are common issues to check when integrating other plugins with the Event Spawner.

| Problem | Possible Cause | What to Check |
|---|---|---|
| An action affects the wrong NPC | A recycled runtime ID was reused. | Resolve the current event or use its BEID. |
| Name labels or effects remain behind | A sprite was recycled without cleaning up custom attachments. | Check sprite rebinding and cleanup. |
| Another plugin can't detect spawned events | Its event cache doesn't include dynamically created events. | Refresh its cache when events spawn or unspawn. |
| A plugin crashes when accessing a saved event | A dormant or preloaded event was treated as live. | Check the event's state before calling engine methods. |
| Loading a saved game causes errors | A plugin accessed map or sprite data too early. | Review map-loading and initialization hooks. |
| Movement routes behave incorrectly after restoration | An old event reference or ID was retained. | Reacquire the current event and verify movement-route handling. |
| Events are registered multiple times | Initialization or restoration repeats registration. | Make registration safe to repeat. |

### Testing Your Integration

When developing compatibility with the Event Spawner, test the features your plugin actually uses.

Useful scenarios include:

- Spawning and unspawning events.
- Recycling runtime event IDs.
- Restoring base saved events.
- Streaming persistent boundary events.
- Loading saved games with spawned events.
- Testing with and without the Boundary System, if applicable.

**The most important rule is that runtime event IDs are not always permanent event identities.**

For persistent boundary events, use BEIDs. For runtime-only systems, make sure your plugin handles event creation, recycling, restoration, and cleanup correctly.

## Related Documentation

- [Spawner Script Calls](Documentation/scriptcalls.html)
- [Spatial Lookup & Placement](Documentation/spatial.html)
- [Saved Events](Documentation/savedevents.html)
- [BEID Database](Documentation/Boundary/beiddatabase.html)
- [Boundary Self Variables](Documentation/Boundary/selfvariables.html)
- [Saved Boundary Events](Documentation/Boundary/savedboundaryevents.html)
