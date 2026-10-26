---
title: Upgrading to v2.1
parent: Spawner Documentation
nav_order: 8
---

# Upgrading to v2.1

The published comparison baseline is EventSpawner **v2.0a** with Boundary System **1.5**. The accompanying Boundary source still labels itself **1.5**; do not infer matching version numbers between the products.

## Retained workflows

Coordinate/region spawning, names for source map/events, overlap control, transformation, event recycling, base saved events, six boundary modes, auto handlers, and basic preload helpers already existed. Review the argument, namespace, and save differences below when retaining these workflows.

v2.1 adds terrain-tag spawning, `spawnChild`, BEID lookup/control, boundary self variables, public preload wrappers and MZ preload commands, and explicit auto-handler update modes. Existing preloading now integrates with BEIDs and self-variable state rather than being an entirely new concept.

## Review configuration and integrations

* Use the matching current Spawner and Boundary files together; the boundary storage ownership changed from `$gameSystem` to `$gameMap`. Those internal containers are not a stable application API.
* The `createSpawnerBoundary` script defaults `expandBy = 0` and `maxEvents = 0` are intentional. Request directional expansion only when wanted, and supply positive spawn capacity; zero is not unlimited. Quick Start's separate Max Events editor default remains `400`. Unspawn boundaries do not need a spawn cap.
* `addAutoHandler` accepts an eighth `updateMode` argument: `"WaitTime"` (default when omitted by the call) or `"Movement"`. `wait` is not a movement cooldown in Movement mode. Recognized historical saves without a stored mode migrate to `"Movement"`, matching the published step-driven automatic handler.
* Boundary unspawn entry points include `Ritter.unspawnEvent` and `Ritter.unspawnBoundaryEvent`. The historical `Ritter.Boundary.unspawnBoundaryEvent(eventId, removeSave = false)` namespace is retained as an ownership-checked adapter to the public unspawn lifecycle, including child cleanup. It returns `undefined`.
* `Preload Spawn Maps` remains visible, but the boot loader caches all listed project maps. The old Developer Mode settings are not active parameters in this implementation.
* Quick Start now requires **Spawn Map ID** on every Spawn Boundary; different boundaries can use different template maps. The shared Map IDs list is for gameplay destinations. Because Quick Start was unreleased, update existing development entries manually in Plugin Manager; missing source IDs are configuration errors. See [Quick Start setup](installation.html#quick-start-setup). Existing saved boundaries retain their stored configuration.
* VisuStella Events & Movement Core integration now uses guarded event-cache invalidation and event lookup/enumeration handling instead of the old dedicated add/remove hooks. Load Events & Movement Core above Ritter. This supports Ritter events alongside ordinary event/movement features; it does not promise interoperability between the plugins' spawning systems. See [integration scope](../compatibility.html#visustella-events--movement-core).

## Save compatibility

Keep full project/save backups and test copies before upgrading player data. Read [Save Storage & Restoration](persistence.html#historical-v20a-boundary-saves). The loader imports recognized published v2.0a/Boundary 1.5 ownership from `$gameSystem._ritterBoundaryData` into current map-owned storage before hydration and identity reconstruction. Live saved events, dormant records, and preloads retain their supported state; records without BEIDs receive identities during reconstruction. Successful migration removes the obsolete system container so it is not imported again after resaving.

Native v2.1 saves are not reimported. Mixed storage with disjoint boundary owners can merge; ambiguous shared names, conflicting representations, missing templates, or malformed historical data stop loading explicitly. Missing historical dormant self-switch evidence cannot be recovered from a recycled runtime ID and is not guessed. Test representative original player saves and plugin combinations before promising universal upgrade compatibility. Known legacy tile shapes and version-one external manifests remain supported; new external saves use generation-based version-two manifests.

Validate a cold Continue, a warm save/load, map re-entry, project-version map rebuilding, and the specific features your game uses. For boundary populations confirm live entities, dormant records, preloads, and their BEIDs rather than comparing only sprite counts.

## Developer references

See [Compatibility Guide](../compatibility.html), [BEID Database](Boundary/beiddatabase.html), and [Self Variables](Boundary/selfvariables.html) for reference lifetime and integration rules. Avoid depending on underscored pools, serialized internal containers, or private restoration helpers.
