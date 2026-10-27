---
title: Save Storage & Restoration
parent: Spawner Documentation
nav_order: 6
published: false
---

# Save Storage & Restoration

## What is saved

| Representation | Storage and restoration |
| --- | --- |
| Live map event | Its runtime state is in the main engine save. Persistent boundary events also retain their owning boundary and BEID. |
| Dormant saved boundary event | A saved event clone in the flattened boundary collection. |
| Preloaded boundary event | A lightweight record in that collection; it is not instantiated merely because the game loads. |
| Base saved event | Retained in the base map/event saved records, independently of the BEID database. |

The plugin reconstructs template-backed event pages from the cached source maps. Dormant/preloaded boundary records are flattened for serialization, then restored to the boundary's map/tile containers. The BEID database and runtime lookup tables are rebuilt from the reconstructed entities.

## External boundary chunks

`BoundarySaveSettings.externalSave` selects external storage for the flattened dormant/preloaded collection. `externalSaveCount` is the maximum **records per chunk**, not a limit on persistent entities or live events. The normal value is `4000`.

Current external saves use a version-two manifest in the main save. It identifies the exact chunk generation, record count, and chunk count. New chunks are staged before the main save is committed. The previous generation is retained for engine backup recovery; older generations may be reclaimed after successful commits.

Loading follows the **saved manifest**, even if the current external-save parameter has changed. Supported version-one manifests use their original chunk names. Incomplete manifests, missing/invalid chunks, count mismatches, conflicting persistent identities, and missing templates required to hydrate saved Game_Events are errors; they are not an instruction to start an empty population. A never-spawned preload is metadata and is not fully template-validated simply by loading it; if its later promotion fails, its stored record remains available for retry.

Do not rename individual chunk files. Copy/restore the main save **and all referenced chunk files together**, including a backup generation when relying on engine backups. Distributing just the main file is insufficient for an external save. Browser builds use the engine's browser storage rather than ordinary files; backup/export tooling must account for every referenced record there too.

## Continue and map reloads

Template loading completes before normal title-screen New Game/Continue. Membership reconstruction does not require current-map screen geometry to exist yet.

Previously live **persistent boundary events** restore as live. An engine-requested same-map rebuild (for example after a project version change) captures and restores that exact saved-live set before sprites are created. It does not fill the world with all preloads or require player movement to recover saved-live events. Actual active/inactive boundary state is preserved through that rebuild.

Dormant and preloaded entities remain eligible for normal streaming or explicit initialization. Temporary events retain their existing map-lifetime semantics and may end when the engine rebuilds the map.

## Supported state and limits

Self variables, self switches, position, and serializable runtime state follow the saved entity. Page-condition changes can still apply new page settings. Template updates change the data used to hydrate pages. Runtime sprites, textures, DOM objects, callbacks, and arbitrary third-party caches are not persistent entity state.

Save/load replaces object references. Store a `(boundaryName, mapId, BEID)` tuple and reacquire its [entry/entity](Boundary/beiddatabase.html); do not keep an old entry or `Game_Event` reference across a load.

MV keeps the synchronous engine save/load return contract; MZ keeps Promise-based completion. See the [developer integration guide](../compatibility.html#save-load-and-map-rebuilding).

## Existing player saves

Back up complete saves before upgrading. Known legacy tile structures and version-one external manifests have compatibility handling, but this is not certification of every historical commercial save or another plugin's custom format. Test representative old saves with your actual game and plugin combination. New version-two external saves are not certified for older plugin builds.

### Historical v2.0a boundary saves

With the matching Boundary companion installed, recognized published EventSpawner v2.0a/Boundary 1.5 storage under `$gameSystem._ritterBoundaryData` is imported into the current `$gameMap` boundary representation before template hydration and runtime/BEID reconstruction. Migration preserves definitions, saved live events, dormant snapshots, preloads, placement, template references, and available state. Missing BEIDs are assigned through the current identity system; proven live/snapshot duplicates are consolidated, not cloned into extra entities.

The importer validates historical definitions and records rather than treating any property with that name as a valid old save. Native current storage is retained. Disjoint historical/current boundary owners can merge; ambiguous same-name populations, unverifiable live/snapshot overlap, invalid records, and missing templates cause an explicit load failure. It does not silently choose an older population over current data.

After successful import the obsolete system container is removed from the loaded game. Save normally to retain the migrated representation; subsequent loads of that new save do not reimport the old population. This does not rewrite the original save during loading.

Live historical events can recover self switches from the saved engine self-switch table. Dormant records use their own available switch metadata. Some historical snapshots were captured before that metadata was recorded; migration reports incomplete evidence and does not borrow switch values from a possibly recycled runtime ID. Lost historical values cannot be reconstructed reliably. Keep backups and validate authentic saves from your game, including streaming after the upgrade.
