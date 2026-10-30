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

Upgrading an existing game? Start with [Upgrading to v2.1](Documentation/upgrading.html). If you use external boundary saves, also review the [backup and transfer instructions](Documentation/Boundary/savedboundaryevents.html#backing-up-and-transferring-external-saves).

## Base Event Spawner

Spawn template copies at coordinates, in regions, or on terrain tags; transform live events; choose saved or temporary behavior; and recycle event objects and sprites.

* [Installation](Documentation/installation.html)
* [Script Calls](Documentation/scriptcalls.html)
* [Plugin Commands (MZ)](Documentation/plugincommands.html)
* [Spawn Maps & Templates](Documentation/spawnmapsandtemplates.html)
* [Saved Events](Documentation/savedevents.html)
* [Spatial Lookup & Placement](Documentation/spatial.html)
* [Event Recycling](Guides/eventrecycling.html)

## Boundary streaming and persistent entities

Use the Boundary companion to bring NPCs, enemies, and objects into play as the player approaches, then save their state when they leave the active area. Prepare populations with preloads and use BEIDs to find the same saved events later.

* [Boundary Script Calls and Setup](Documentation/Boundary/scriptcalls.html)
* [Boundary Plugin Commands (MZ)](Documentation/Boundary/plugincommands.html)
* [Boundary Types & Behaviors](Documentation/Boundary/boundarytypes.html)
* [Saved Boundary Events](Documentation/Boundary/savedboundaryevents.html)
* [Preloaded Boundary Events](Documentation/Boundary/preloadevents.html)
* [BEID Database](Documentation/Boundary/beiddatabase.html)
* [Self Variables](Documentation/Boundary/selfvariables.html)
* [Boundary Best Practices](Guides/Boundary/bestpractices.html)

Use [Self Variables](Documentation/Boundary/selfvariables.html) to keep each NPC's quest progress or each resource's state, even while the event is not active. See [what's new in v2.1](Documentation/releasenotes.html) when upgrading an existing project.
