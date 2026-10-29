---
title: What's New In v2.1
parent: Spawner Documentation
nav_order: 9
---

# Ritter Ultimate Event Spawner v2.1

Ritter Ultimate Event Spawner v2.1 introduces new spawning options, expanded persistent event management, improved performance, and enhanced compatibility for RPG Maker MV and MZ.

This update also brings major improvements to the optional Boundary System, making it easier to create and manage large, persistent event populations.

## New Spawning Features

- **Terrain Tag Spawning:** Spawn events on tiles matching specific terrain tags, providing another placement option alongside coordinates and regions.
- **Spawn Child Events:** Create events associated with a parent event. When the parent is unspawned, its child events are automatically cleaned up.
- **Expanded MZ Plugin Commands:** New commands for spawning child events and working with terrain tags.
- **Spawn Result Variables:** Store spawned event IDs in RPG Maker game variables, including support for collecting multiple IDs in arrays.

All supported script calls remain available in both RPG Maker MV and MZ.

## Performance and Event Recycling

v2.1 introduces several improvements to event lookup, recycling, and restoration.

- **Spatial Lookup:** A new spatial indexing system improves event lookup performance in supported operations by locating events through their map positions.
- **Improved Recycling:** Additional safeguards protect occupied event IDs and improve event reuse.
- **Improved Event Transformations:** Better handling of event templates, graphics, positions, and page state.
- **Improved Sprite Management:** More reliable sprite reuse and cleanup during spawning, unspawning, and restoration.

Event recycling has been a core feature of Ultimate Event Spawner for years. v2.1 builds on that foundation with additional optimizations and reliability improvements.

## Boundary System Improvements

The optional Boundary System receives several major additions in v2.1.

### Persistent Boundary Event IDs (BEIDs)

Saved boundary events now have persistent identities that remain associated with them even when their RPG Maker runtime event IDs change.

The new **BEID Database** makes it possible to find and interact with individual saved events whether they are currently spawned, dormant, or preloaded.

### Boundary Event Self Variables

Store custom persistent information on individual boundary events, including:

- NPC dialogue and quest progress
- Resource quantities
- Character-specific settings
- Other custom gameplay data

Self Variables can be read and updated without requiring the event to be active on the map.

### Expanded Event Preloading

Additional script calls and MZ plugin commands make it easier to prepare persistent event populations before they appear.

Preloaded events receive BEIDs that can be used to identify and manage them later.

### Automatic Boundary Update Modes

Automatic boundaries now support two processing modes:

- **Movement:** Updates when the player moves. Ideal for streaming static events as the player explores, without repeatedly processing boundaries while the player stands still.
- **WaitTime:** Updates at configured intervals, even when the player is stationary. Useful for continuous spawning and monitoring moving events.

For static event populations, Movement mode can significantly reduce unnecessary processing.

For moving events, a short WaitTime interval is recommended for unspawn boundaries so events that wander outside their boundaries can be cleaned up promptly.

### Streaming Quick Start

Configure automatic spawn and unspawn boundaries directly through the Plugin Manager.

Quick Start provides an easier way to set up automatic event streaming without manually creating every boundary through script calls.

### Improved Saved Event Restoration

Additional improvements help preserve and restore persistent boundary populations when loading games, returning to maps, and streaming events in and out of active areas.

## Saving and Compatibility

### External Boundary Save Files

For games with large persistent populations, optional external saving allows boundary event data to be divided into additional save files.

In v2.1, these files are organized into **dedicated folders for each game save slot**, keeping the main save directory clean and manageable.

External saving remains optional. By default, boundary data is included in the regular RPG Maker save.

Existing supported external saves using the older file layout can still be loaded, with the new organization applied when the game is saved again.

### Existing Save Compatibility

v2.1 includes migration support for recognized saves created with Event Spawner v2.0a and Boundary System v1.5.

Existing saved boundary events can receive BEIDs automatically during migration, preserving their persistent identities for use with the new system.

### VisuStella Compatibility

Improved compatibility with VisuStella Events & Movement Core allows Ritter's event spawning and management features to coexist more reliably with its event and movement functionality.

The two plugins retain their own independent spawning systems.

## Updating to v2.1

Updating is straightforward:

1. Replace `Ritter_EventSpawner.js` with the new version.
2. If using the Boundary System, update `Ritter_BoundarySystem.js` with the companion version supplied with the release.
3. Keep the plugin filenames unchanged and load the Boundary System above Event Spawner.

Existing documented spawning workflows and script calls remain supported.

If you're updating a game with existing save files, back up your saved data and test a representative save after updating. Projects using external boundary saving should preserve the associated external save files as well.

For detailed upgrade instructions, compatibility information, and examples, see the [Upgrading to v2.1 Guide](https://notritter.github.io/SpawnerDocs/Documentation/upgrading.html).

## Documentation

Full documentation is available for both the Event Spawner and optional Boundary System, including script calls, plugin commands, examples, persistent events, BEIDs, Self Variables, and automatic boundary setup.

**[Ritter Ultimate Event Spawner Documentation](https://notritter.github.io/SpawnerDocs/)**
