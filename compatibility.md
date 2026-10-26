---
title: Compatibility Guide
nav_order: 6
---

# Developer Integration Guide

This guide explains how to adapt RPG Maker **MV/MZ** code to Ritter Ultimate Event Spawner v2.1 and its Boundary integration. It is **not a list of tested third-party plugins**, and the examples do not certify any named plugin combination.

Load an integration patch after the plugins whose methods it aliases. Test the exact engine/plugin versions used by your game.

## Identity and representation

| Object/identifier | Lifetime and use |
| --- | --- |
| Runtime `eventId` | Current-map slot. Temporary and boundary slots can be recycled for unrelated entities. Base retained saves reserve their slot, subject to map-update collision handling. |
| `(boundaryName, mapId, BEID)` | Persistent saved/preloaded boundary identity. Keep the whole tuple. Temporary boundary events may have no BEID. |
| Live `Game_Event` | Installed in `$gameMap._events`; can run pages/movement and own a character sprite. |
| Dormant saved clone | Stored persistent state; it can have a Game_Event prototype without being a live map event. |
| Preload object | Plain metadata with `isPreloadObj === true`; no Game_Event methods or sprite. |

Reacquire [BEID entries and entities](Documentation/Boundary/beiddatabase.html) after streaming, transfers, and loads. Entries themselves are rebuilt and are not permanent handles. An old map-event object can be recycled in place; keeping the same JavaScript reference is not proof it still represents the same entity.

## Actual lifecycle paths

| Transition | Relevant execution |
| --- | --- |
| Fresh base/boundary spawn | Construct/stage template data, `newData`/`initialize`, install in map/list, then create/rebind sprite. A fresh spawn can initialize more than once. |
| Recycled fresh entity | Reuse erased event slot, apply new template with `newData`/`initialize`; unrelated entity-owned state must not leak. |
| Boundary unspawn | Snapshot saved state/self switches, update BEID/storage, then teardown, move off-map, clear boundary identity on the old runtime object, and `erase()`. |
| Saved boundary respawn | Clone the saved Game_Event, assign runtime ID, restore switches, refresh page, update BEID/Spatial, create/rebind sprite. It does **not** call full `Game_Event.initialize` for that saved state. |
| Base saved restore | Reinstall the retained saved event and rebind sprite; do not assume constructor/initialize reruns. |
| Save/load | Deserialize/hydrate templates and records, rebuild runtime tables/BEIDs, then create the map scene/sprites. Initialization hooks alone cannot rebuild another plugin's registries. |
| Same-map rebuild | Previously live persistent boundary entities are saved and restored before character-sprite creation; runtime IDs and interpreter targets can change. |

Erasing an engine event alone is not the same as Spawner unspawn. Integrations that remove a spawned entity should use `Ritter.unspawnEvent`, or BEID control for persistent boundary entities, so ownership/pools are maintained.

## Event lifecycle hooks and cleanup

Use **idempotent** initialization/cleanup: repeated initialization, erase, or rebinding must not register twice or dispose another entity's resources.

`Game_Event.initialize` is useful for defaults on newly created/recycled entities, but is not a universal “entity became live” hook. After restoration, reconcile your runtime registry from live map events at a defined lifecycle point. Do a bounded rebuild on map/load transitions rather than a global scan every frame.

`Game_Event.erase` is useful for releasing your transient resources. It does not cover scene-only sprite destruction, nor does it prove a persistent entity was permanently destroyed. During Spawner teardown, `_BEID` and `_parentBoundary` may already be cleared before `erase()` runs. Do not derive your cleanup key from those fields at that point; retain your own transient registration token or verify the registered object.

The following example maintains **only a transient runtime registry**. It is not a persistence store and does not cover live registration for every spawn path by itself:

```js
(function() {
    var runtime = Object.create(null);
    function key(event) { return event._mapId + ":" + event.eventId(); }

    function reconcile() {
        runtime = Object.create(null);
        $gameMap.events().forEach(function(event) {
            if (!event._erased && event.x >= 0 && event.y >= 0) {
                runtime[key(event)] = event;
            }
        });
    }

    var erase = Game_Event.prototype.erase;
    Game_Event.prototype.erase = function() {
        var token = key(this);
        if (runtime[token] === this) delete runtime[token];
        // Release only resources owned by your plugin; keep persistent data.
        erase.apply(this, arguments);
    };

    var onMapLoaded = Scene_Map.prototype.onMapLoaded;
    Scene_Map.prototype.onMapLoaded = function() {
        onMapLoaded.apply(this, arguments);
        reconcile();
    };

    // In your own spawn adapter, register each successfully returned live event.
    // For automatic streaming, integrate the current spawn/restore methods too,
    // or resolve a BEID on demand instead of maintaining a registry.
})();
```

If you wrap `Game_Map.spawnEvent`, `spawnBoundaryEvent`, `restoreUnspawnedEvent`, or `respawnSavedBoundaryEvent`, preserve arguments/return values and inspect the actual result **after** the original call. Transforming an event can change its template while keeping the object/ID, so caches of page, note, image, or collision data need refresh too. These are version-specific integration surfaces, not promises that their internals will never change.

## Sprite creation, removal, and rebinding

`Spriteset_Map.createCharacters` creates scene-specific character sprites for loaded events. `Spriteset_Map.spawnedEvent` reuses a cached sprite by removing old array/parent references, ending MV-owned effects, calling `setCharacter(event)`, and reinserting it. Dormant/preload records have no active character sprite.

Use `Sprite_Character.setCharacter` for rebind cleanup and your sprite's normal scene-destruction path for final cleanup. An event-erase hook alone does not cover opening menus, scene reconstruction, or loading. Remove only your own attachments; do not destroy shared character bitmaps/textures or the cached character sprite owned by the engine/Spawner.

```js
(function() {
    var setCharacter = Sprite_Character.prototype.setCharacter;
    Sprite_Character.prototype.setCharacter = function(character) {
        var owned = this._myAddonLabel;
        if (owned && owned.parent) owned.parent.removeChild(owned);
        if (owned && typeof owned.destroy === "function") {
            owned.destroy({ children: true, texture: false, baseTexture: false });
        }
        Object.defineProperty(this, "_myAddonLabel", {
            value: null, writable: true, configurable: true
        });
        setCharacter.apply(this, arguments);
        // Attach the new label idempotently at your normal ready/update point.
    };
})();
```

MV animation sprites and MZ animation/effect management have different owners. Preserve the engine's removal methods and completion lists. Test recycling during animations, flashes, balloons, and route/animation waits rather than merely checking that the character image appears.

## Cached references and invalidation

* Key current-map caches by map and runtime ID, and verify their value still represents the expected entity before use.
* For a persistent boundary entity, store its tuple and resolve it at the time of the operation. Do not keep a raw event/entry/sprite across asynchronous callbacks or save/load.
* Purge registry entries when an entity leaves runtime simulation; rebuild after load/map setup. Keep persistent logical data in serializable entity state or the Self Variables API instead of a transient registry.
* Use non-enumerable properties for transient handles that should not be serialized. Such properties will not be restored by JsonEx; recreate them rather than expecting persistence.
* Avoid storing sprites, DOM nodes, callbacks, circular graphs, or unrelated engine event references in persistent event data.

## Collision and spatial lookup

The Boundary integration's `Ritter.Spatial.eventsAt(x,y)` returns event objects for a current-map cell. It is not a universal collision predicate, and base-only Spawner need not provide it. The engine's `eventsXy`, `eventsXyNt`, and movement/collision rules are not globally replaced.

```js
function eventsForTile(x, y) {
    if (Ritter.Spatial && typeof Ritter.Spatial.eventsAt === "function") {
        return Ritter.Spatial.eventsAt(x, y);
    }
    return $gameMap.eventsXy(x, y);
}
```

Filter for your own trigger, through, priority, and ownership requirements. Never pass a returned Game_Event object where a numeric event ID is required.

The index hooks event `setPosition`, normal steps, jumps, and erasure, and is rebuilt on setup/load. Preserve aliases when overriding these methods. A custom movement system that writes `_x/_y` directly must explicitly update its spatial integration **after** the tile changes. `Ritter.Spatial.track(event)`/`untrack(event)` exist for the current implementation, but are low-level, version-specific interfaces; do not edit `_grid`, `_last`, or cell Sets directly.

See [Spatial Lookup & Placement](Documentation/spatial.html) for placement limits and cached candidates.

## Live, dormant, and preloaded data

Use [self variables](Documentation/Boundary/selfvariables.html) for persistent logical state, including updates to a resolvable dormant entity or preload. Do not call movement, collision, page, sprite, or interpreter methods on an offline clone merely because its prototype is Game_Event.

There is no general public offline position/visual setter. Modifying a saved clone/preload's coordinates directly does not relocate its tile-storage key or BEID bookkeeping. Store your intention as logical data and apply it to a verified live entity through engine methods.

## Save load and map rebuilding

Template hydration and runtime ID/BEID reconstruction happen before the map scene is fully ready. `$dataMap` can still be `null` during cold Continue even though template maps are loaded. Do not call map dimensions, scrolling, screen-centered boundary geometry, or sprite methods from a pure identity-rebuild hook.

Use `Scene_Map.onMapLoaded` after the original method when your integration needs map geometry/display objects. If aliasing `DataManager.extractSaveContents`, recognize that external restoration can defer extraction: confirm that the intended map was actually installed and plugin restoration completed before rebuilding a registry. Preserve MV's synchronous return contract and MZ's Promise/rejection chain when wrapping save/load. Do not convert MV failures into truthy Promises or swallow MZ errors.

Do not reset persistent entity data from `initialize` or a reconciliation pass. Saved respawn deliberately preserves character state instead of rerunning the full initializer. Same-map rebuilding also retains the previously live persistent set, inactive boundaries, and supported custody/interpreter references; it does not instantiate all dormant records.

## VisuStella Events & Movement Core

When Events & Movement Core is installed, load it above Ritter's plugins and keep BoundarySystem above EventSpawner. Ritter provides guarded compatibility for its own events alongside ordinary event/movement features. This is not support for interchanging Ritter and VisuStella spawn records or certification of every plugin/version combination.

The integration invalidates Events & Movement Core's event cache after successful spawn, unspawn, restoration, and reconstruction transitions. Ritter's event enumeration excludes its erased off-map recycling objects. Event lookup also resolves valid current-map `_events` slots, including Ritter IDs at or above `1000`, without assuming a numeric range belongs exclusively to one provider.

Allocation avoids foreign event slots. If different live objects nevertheless claim the same runtime ID, lookup reports the conflict instead of silently replacing either owner. Ritter does not write into VisuStella's private spawned-event collection or modify its plugin file. Preserve both plugins' accessors and aliases in additional integration patches, and test your exact movement features, page changes, streaming, and save/load configuration.

## Interpreter references and movement-route waits

A plugin that retains event references or route-wait targets must reconcile them when an ID changes. Supplied MV code uses an interpreter `_character` reference for route waits; MZ uses `_characterId`. Spawner rebinds supported engine-owned references during its same-map restore, including nested/parallel interpreters, but cannot identify arbitrary third-party caches or literal IDs stored in your own commands/variables.

Use BEIDs to reacquire the live target. Test a save/load or rebuild with an earlier free ID slot so that restoration actually changes IDs, then verify route waits continue while forcing and end when the restored route completes.

## Common symptoms and practical checks

| Symptom | Likely integration issue | Check |
| --- | --- | --- |
| An action affects a different NPC | Runtime ID/object was cached across recycling. | Resolve full BEID tuple again; verify live ownership. |
| Icons or effects remain behind | Owned sprite/effect was not detached on erase/rebind/scene destruction. | Audit each owner and release only its resources. |
| New spawns are absent from another plugin's collision cache | Cache built once from editor events. | Register successful dynamic spawns and remove erased ones; rebuild after load. |
| Offline method call crashes or changes unrelated state | Dormant clone/preload treated as live. | Check entry, representation, current map, and `spawned`. |
| Cold Continue fails but entering New Game helps | Integration assumed current-map geometry existed during extraction. | Defer geometry/display work to map-ready lifecycle. |
| Route wait hangs or ends early after restore | Old object or runtime ID retained. | Rebind the target and test both engine implementations. |
| Duplicate registrations after load | Initialization and reconciliation were not idempotent. | Clear/rebuild transient registry once; preserve persistent state. |

These are diagnostic starting points, not proof that a named third-party plugin is compatible. Test fresh/recycled spawns, saved respawns, cold/warm loads, map-version rebuilding, base-only operation, and streaming cycles with the complete intended plugin order.
