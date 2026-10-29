---
title: Version History
parent: Spawner Documentation
nav_order: 10
---

# Version History

A history of updates, new features, performance improvements, compatibility changes, and bug fixes for **Ritter Ultimate Event Spawner**.

For installation instructions, migration information, and important changes when updating an existing project, see the [Upgrading to v2.1 Guide](https://notritter.github.io/SpawnerDocs/Documentation/upgrading.html).

---

## v2.1: Major Feature and Stability Update

Version 2.1 introduces new spawning capabilities, faster event lookups, improved recycling and restoration, and expanded tools for managing persistent event populations.

The optional **Ritter Boundary System** also receives significant improvements, including persistent event identities, Self Variables, enhanced preloading, configurable automatic boundary processing, and more reliable saving and loading.

### New Spawning Features

- **Terrain Tag Spawning**

  - Added `Ritter.spawnEventTerrainTag` for spawning events on tiles matching specified terrain tags.
  - Added the **Spawn Event Terrain Tag** plugin command for RPG Maker MZ.
  - Complements existing coordinate-based and region-based spawning.

- **Parent and Child Events**

  - Added `Ritter.spawnChild` for creating events associated with a parent event.
  - Child events are automatically unspawned when their parent is unspawned.
  - Added the **Spawn Child** plugin command for RPG Maker MZ.
  - Parent ID `0` in the MZ command refers to the event executing the command.

- **Expanded MZ Plugin Command Outputs**

  - Coordinate, region, and terrain-tag spawning commands can now store successful runtime event IDs in game variables.
  - Supports assigning an event ID directly or appending IDs to an array.
  - Results are available to subsequent event commands.

### Performance and Event Recycling

- **Spatial Indexing**

  - Added a built-in Spatial Index to the base Event Spawner.
  - Enables faster tile-based event lookups in supported spawning, unspawning, and restoration operations.
  - Automatically maintains indexed positions as events move, jump, spawn, unspawn, or are erased.
  - Rebuilds and synchronizes the index during map setup and save restoration.
  - Does not require the Boundary System or a separate Spatial Grid plugin.

- **Improved Event Recycling**

  - Strengthened runtime event ID ownership and reuse checks.
  - Improved protection of occupied event slots and retained saved events.
  - Improved collision handling during restoration.
  - Improved event reference handling when recycled IDs are reassigned.

- **Event Transformation Fixes**

  - Corrected cases where transformed or recycled events could retain graphics from a previous template.
  - Improved event page and template state initialization.
  - Corrected transformations that could unexpectedly reset an event's position.

- **Sprite Management**

  - Improved sprite rebinding when event slots are recycled.
  - Improved duplicate sprite prevention and cleanup.
  - Corrected visibility and effect state when sprites are reused.
  - Improved compatibility with RPG Maker MV's sprite effect handling.

- **Saved Event Restoration**

  - Improved saved-event snapshot updates and restoration.
  - Strengthened protection against recycled event IDs referencing the wrong saved event.
  - Added template-backed saved-event snapshots and template readiness checks during game initialization and loading.
  - Improved save/load error handling while preserving the expected behavior of both RPG Maker MV and MZ.

> **Note:** Event recycling and sprite reuse have been core features of Ultimate Event Spawner since earlier releases. Version 2.1 improves their reliability and performance rather than introducing them for the first time.

### Boundary System: Persistent Event Identities

*Requires the optional Ritter Boundary System.*

- **Boundary Event IDs (BEIDs)**

  - Added persistent identities for saved boundary events.
  - BEIDs remain associated with their saved entities even when runtime event IDs change.
  - Identities are scoped by boundary name and gameplay map.
  - Added `Ritter.getBEIDEntry` for retrieving saved event database entries.
  - Added `entry.event()` for resolving an entry's currently active event, when available.

- **Persistent Event Management**

  - Added `Ritter.restoreByBEID` for restoring supported saved entities on the current map.
  - Added `Ritter.unspawnByBEID` for unspawning supported entities by their persistent identity.
  - Improved BEID ownership validation and saved-event tracking.

### Boundary System: Self Variables

- Added **Boundary Self Variables** for storing custom data on individual persistent boundary events.
- Supports saved data associated with active, dormant, and preloaded entities.
- Allows supported saved data to be accessed or modified without requiring an event to be active on the map.
- Useful for NPC states, quest progress, dialogue choices, resource quantities, and other entity-specific information.

### Boundary System: Event Preloading

- Expanded the existing persistent event preloading system.
- Added `Ritter.preloadEvent` and `Ritter.preloadEventRegion`.
- Added MZ plugin commands for preloading individual events and region-based populations.
- Successful preloads can return persistent BEIDs for later reference.
- Improved preload promotion, saved identity ownership, and restoration.

### Boundary System: Automatic Update Modes

Added two explicit processing modes for automatic boundaries:

- **Movement**

  - Updates boundaries when the player moves, including jumps.
  - Avoids repeated boundary processing while the player remains stationary.
  - Recommended for streaming static event populations.

- **WaitTime**

  - Updates boundaries at configured intervals.
  - Continues processing even while the player is stationary.
  - Useful for continuous spawning and monitoring independently moving events.

Spawn and unspawn boundaries can use different update modes.

For moving event populations, a short WaitTime interval on the unspawn boundary can help remove events that wander outside the active area without waiting for player movement.

### Boundary System: Streaming Quick Start

- Added **Streaming Quick Start** configuration through the Plugin Manager.
- Supports automatic creation of paired spawn and unspawn boundaries.
- Allows individual spawn boundaries to use different template maps.
- Supports configurable population limits and boundary settings.
- Provides a simpler way to establish automatic event streaming without manually creating every boundary through script calls.

### Boundary System: Streaming and Restoration

- Improved boundary membership tracking and saved tile records.
- Improved persistent event ownership and self-switch capture.
- Improved removal and cleanup of saved boundary populations.
- Improved boundary target handling and geometry consistency.
- Rebuilds runtime memberships, event pools, persistent identities, and supporting indices when loading.
- Previously active saved boundary events are restored immediately during loading, including supported same-map rebuild scenarios.
- Dormant and preloaded populations remain available for subsequent streaming.

### Boundary System: External Save Storage

- Added optional external storage for large persistent boundary populations.
- Dormant and preloaded boundary records can be distributed across additional save files.
- External storage remains optional; regular RPG Maker save storage is the default.
- Added stronger validation and safer save generation handling.
- Added verification of newly written external chunks before completing the main save.
- Organized new external files into dedicated folders for each save slot on desktop.
- Browser deployments use corresponding per-slot storage namespaces rather than physical directories.
- Preserved loading support for recognized earlier external-save layouts.
- Existing supported flat-file external saves are reorganized into the new layout when saved again.

### Save Migration and Compatibility

- Added migration support for recognized saves created with **Event Spawner v2.0a and Boundary System v1.5**.
- Existing saved boundary populations are retained during supported migration.
- Missing BEIDs are assigned automatically.
- Improved handling of historical saved-event records and duplicate ownership.
- Improved cold-load and Continue restoration in RPG Maker MV and MZ.
- Preserved the historical `Ritter.Boundary.unspawnBoundaryEvent` script call.
- Kept base Event Spawner saving, restoration, and Spatial functionality independent of the optional Boundary System.

### Boundary System Companion Improvements

The Boundary System companion supplied with this release includes additional fixes and optimizations. The companion maintains its own version numbering.

- Added cached boundary-name lookups with appropriate cache invalidation.
- Improved boundary tile calculations and geometry caching.
- Corrected inconsistent boundary calculations involving even-sized dimensions.
- Improved map-edge clipping and boundary membership checks.
- Corrected property editing involving zero values and case-insensitive property names.
- Improved boundary deletion and cleanup.
- Improved handling of local anchor coordinates and movement destinations at coordinate `0`.
- Improved standalone Boundary System region selection without requiring Event Spawner placement helpers.

### Plugin Compatibility

- Improved coexistence with **VisuStella Events & Movement Core**.
- Improved event lookup, cache invalidation, and runtime event enumeration.
- Strengthened protection of event slots owned by other plugins.
- Improved handling of runtime event IDs of `1000` and above.
- Preserved existing MZ plugin command identifiers.
- Improved plugin command list parsing, numeric property handling, and interpreter context.

VisuStella and Ritter's event spawning systems remain independent. These improvements are intended to support ordinary event and movement functionality alongside Ritter's spawning features.

### Important Changes for Existing Projects

- `createSpawnerBoundary` now defaults to:
  - `expandBy = 0`
  - `maxEvents = 100`
- Automatic boundary handlers now support an additional `updateMode` argument, defaulting to `"WaitTime"` for new definitions.
- Recognized historical saved boundary definitions without an explicit update mode retain their wait-time-driven behavior.
- Template maps can be loaded as needed without requiring every source map to be listed in the historical **Preload Spawn Maps** setting.
- The earlier Developer Mode configuration is no longer active.
- Existing saved boundary populations require the Boundary System companion to remain installed.
- Supported older saves can be migrated forward, but saves written by v2.1 are not guaranteed to work with older plugin versions.

Before updating a game with existing saved progress, preserve the relevant save data and test the update. If external boundary storage is enabled, include its associated files and subfolders when backing up or transferring saves.

**[Read the full v2.1 Upgrade Guide](https://notritter.github.io/SpawnerDocs/Documentation/upgrading.html)**

---

## v2.0a: Hotfix and Compatibility Update

- Added compatibility with **VisuStella Events & Movement Core**.
- Fixed an edge case in spawned event sprite handling that could cause instability during page refresh.
- Fixed a typo in the **Spawn Event** plugin command.

---

## v2.0: Major Update

- Added cross-engine support for **RPG Maker MV and MZ**.
- Significantly optimized the Event Spawner core.
- Improved Boundary System integration.
- Enhanced performance for large-scale event streaming.
- Expanded and improved developer APIs and plugin commands.
- Included additional bug fixes and stability improvements.

---

## v1.9d: Hotfix

- Fixed an edge case involving missing `$dataMap.events` data when restoring saved events.

## v1.9c: Feature Update

- Added the option to spawn events on occupied tiles using coordinate-based and region-based spawning.

## v1.9b: Hotfix

- Fixed a situational crash during playtest launch caused by template-map loading timing.
- Resolved the error `Cannot set property 'events' of undefined`.

## v1.9: Stability Update

- Fixed a crash involving saved events under specific circumstances.

---

## v1.8: Performance Update

- Optimized automatic Boundary System processing.
- Improved performance under heavy event-streaming workloads.

## v1.7: Compatibility Update

- Added compatibility with **RPG Maker MZ Core Scripts 1.7.0**.

## v1.6: Maintenance Update

- Improved spawn-map loading efficiency and organization.
- Consolidated spawn-map data into `$MapData[mapId]`.

---

## v1.5: Boundary System Integration

- Added plugin commands for Event Spawner.
- Added plugin commands for Boundary System.
- Introduced automatic boundary processing.
- Added saved boundary events.
- Introduced Developer Mode warning alerts and code guidance as an experimental feature.
- Expanded informational comments throughout the plugin code.
- Fixed an issue where recycled event sprites were not completely removed.
- Fixed a saved boundary-event bug (August 23, 2021).

---

## v1.2: Feature Update

- Added spawning events by **Spawn Map Name** and **Event Name**.
- Added coordinate-based event unspawning.
- Added script calls for editing boundary properties.
- Added boundary-filling script calls.
- Added `unspawnEventInBoundary`.

## v1.1: Hotfix

- Fixed a rare crash caused by attempting to unspawn an already unspawned event and subsequently changing scenes.

## v1.0: Initial Release

- Initial release of **Ritter Ultimate Event Spawner**.

---

For installation instructions, script calls, plugin commands, and feature guides, return to the **[Ritter Ultimate Event Spawner Documentation](https://notritter.github.io/SpawnerDocs/)**.
