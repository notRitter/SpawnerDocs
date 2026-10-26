---
title: Welcome
nav_order: 1
---

# Ritter Ultimate Event Spawner Documentation
<img src="https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/RitterUltimateEventSpawnerLogo2.png" alt="Ritter Ultimate Event Spawner Logo" style="width: 50%; height: auto;"/><img src="https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/RitterBoundarySystemLogo.png" alt="Ritter Ultimate Event Spawner Logo" style="width: 50%; height: auto;"/>

Documentation for **Ultimate Event Spawner v2.1** and its accompanying Boundary System for RPG Maker **MV and MZ**. The companion has its own version numbering.

## Store and support

* [Ultimate Event Spawner on itch.io](https://notritter.itch.io/ritter-ultimate-event-spawner-rpg-maker-mv)
* [Boundary System on itch.io](https://notritter.itch.io/ritter-boundary-system-rpg-maker-mz)
* [Spawner support forum](https://notritter.itch.io/ritter-ultimate-event-spawner-rpg-maker-mv/community)

## Start here

1. [Install and configure](Documentation/installation.html) the base plugin and optional Boundary companion.
2. Set up [spawn maps and templates](Documentation/spawnmapsandtemplates.html).
3. Try [script calls](Documentation/scriptcalls.html) in either engine, or [MZ plugin commands](Documentation/plugincommands.html).
4. For streaming, read the [Boundary introduction](Guides/Boundary/introduction.html) and [setup examples](Documentation/Boundary/scriptcalls.html).

Upgrading an existing game? Read [Upgrading to v2.1](Documentation/upgrading.html) and [Save Storage & Restoration](Documentation/persistence.html) before replacing files or transferring saves.

## Base Event Spawner

Spawn template copies at coordinates, in regions, or on terrain tags; transform live events; choose saved or temporary behavior; and recycle event objects and sprites.

* [Installation](Documentation/installation.html)
* [Script Calls](Documentation/scriptcalls.html)
* [Plugin Commands (MZ)](Documentation/plugincommands.html)
* [Spawn Maps & Templates](Documentation/spawnmapsandtemplates.html)
* [Saved Events](Documentation/savedevents.html)
* [Spatial Lookup & Placement](Documentation/spatial.html)
* [Save Storage & Restoration](Documentation/persistence.html)
* [Event Recycling](Guides/eventrecycling.html)

## Boundary streaming and persistent entities

The Boundary companion provides geometry used by the Spawner's automatic handlers. Persistent entities can remain dormant or preloaded while only a selected population is live. Runtime event IDs are reusable; BEID tuples identify persistent boundary entities.

* [Boundary Script Calls and Setup](Documentation/Boundary/scriptcalls.html)
* [Boundary Plugin Commands (MZ)](Documentation/Boundary/plugincommands.html)
* [Boundary Types & Behaviors](Documentation/Boundary/boundarytypes.html)
* [Saved Boundary Events](Documentation/Boundary/savedboundaryevents.html)
* [Preloaded Boundary Events](Documentation/Boundary/preloadevents.html)
* [BEID Database](Documentation/Boundary/beiddatabase.html)
* [Self Variables](Documentation/Boundary/selfvariables.html)
* [Boundary Best Practices](Guides/Boundary/bestpractices.html)

Preloading and the six boundary types already existed in the published releases. v2.1 expands the public preload workflow with BEIDs and Self Variables, adds terrain-tag spawning and Spatial lookup, and strengthens persistence and restoration. See [upgrade notes](Documentation/upgrading.html) for API/default changes and known limits.

## Developer integrations

The [Developer Integration Guide](compatibility.html) covers dynamic initialization/removal, cached IDs/references, sprite rebinding, collision lookup, offline representations, interpreter waits, and restoration. It is an integration guide, not certification of third-party plugins.

**MV:** use Script commands for public control; automatic template rules can use the [MV metadata form](Documentation/Boundary/plugincommands.html#rpg-maker-mv-metadata). **MZ:** use Script commands or the editor's plugin-command interface.
