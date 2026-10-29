---
title: Upgrading to v2.1
parent: Spawner Documentation
nav_order: 8
---

# Upgrading to v2.1

Ritter Ultimate Event Spawner v2.1 introduces new spawning options, improved event management, expanded compatibility, and new features for the optional Boundary System.

Existing spawning workflows remain supported, including coordinate and region spawning, saved events, event transformations, and automatic boundary streaming.

This guide covers what's new, how to update your project, and what to check before continuing with existing save files.

## What's New in v2.1?

### Event Spawner Improvements

- **Terrain Tag Spawning:** Spawn events on tiles matching specific terrain tags, providing another placement option alongside coordinates and regions.
- **Improved Event Recycling:** Updates to event allocation and recycling help manage dynamically spawned events while avoiding occupied runtime event IDs.
- **Saved Event Improvements:** Improved handling of saved events, restoration, and persistent event state.
- **Expanded Template Loading:** All project maps can now be used as event templates without requiring them to be listed in the Preload Spawn Maps parameter.
- **Compatibility Improvements:** Additional handling for plugins that interact with dynamic events, including VisuStella Events & Movement Core.

Existing spawning methods remain available, so you can continue using your current event commands and script calls.

See [Spawner Script Calls](scriptcalls.html) for the available spawning and event management functions.

### Boundary System Improvements

The optional Boundary System also receives several major additions:

- **Persistent Event IDs (BEIDs):** Saved events retain a unique identity even when their runtime event IDs change during streaming.
- **Self Variables:** Store persistent information such as quest progress, dialogue choices, and NPC-specific data.
- **Improved Preloading:** Additional script calls and RPG Maker MZ plugin commands make it easier to prepare persistent event populations.
- **Update Modes:** Choose whether automatic handlers process on player movement or at configured frame intervals.
- **Saving and Restoration:** Improvements to persistent populations, event restoration, and streaming behavior.

See [Boundary Script Calls](Boundary/scriptcalls.html) and [BEID Database](Boundary/beiddatabase.html) for more information.

## Updating Your Project

Updating to v2.1 is straightforward:

1. **Update Event Spawner:** Replace `Ritter_EventSpawner.js` with the new version.
2. **Update Boundary System (if installed):** Replace `Ritter_BoundarySystem.js` with the companion version included in the release.
3. **Check plugin order:** If using both plugins, keep BoundarySystem above EventSpawner in Plugin Manager.

Keep the plugin filenames unchanged.

Event Spawner and Boundary System have separate version numbers, so they do not need to match.

If you're upgrading an existing game with saved events, it's a good idea to test an existing save after updating.

## Event Spawner Upgrade Notes

### Existing Commands and Script Calls

Existing documented spawning, unspawning, and event management functions remain available.

This includes:

- Spawning events at coordinates or within regions.
- Creating temporary and saved events.
- Unspawning and restoring saved events.
- Transforming existing events.
- Working with event templates.

Script calls work in both RPG Maker MV and MZ. RPG Maker MZ also provides the Plugin Command interface.

You do not need to rewrite existing event commands simply because you've updated to v2.1.

### Terrain Tag Spawning

Terrain-tag spawning is a new placement option in v2.1.

You can now use terrain tags assigned in your tilesets to determine where events may spawn.

This is useful when you want to place events on certain terrain types without painting region IDs across the map.

For example, you could assign a terrain tag to grass tiles and use that tag when spawning environmental objects or NPCs.

See [Spawner Script Calls](scriptcalls.html) for the terrain-tag spawning functions and examples.

### Template Maps

v2.1 loads project maps for use as event templates.

The **Preload Spawn Maps** parameter is retained for compatibility, but it no longer restricts which maps are available as templates.

You can continue using your existing template maps and event IDs.

Make sure any maps and template events referenced by your spawning commands remain in your project.

### Saved Events and Recycling

v2.1 includes improvements to saved-event management, restoration, and runtime event recycling.

See [Saved Events](savedevents.html) for information about saving, unspawning, restoring, and permanently removing events.

## Boundary System Upgrade Notes

If your project uses the optional Boundary System, review the following changes.

### Boundary Expansion

The `createSpawnerBoundary()` script call now defaults to `expandBy = 0`.

If your existing setup relies on additional expansion in the player's movement direction, explicitly provide a positive value.

### Maximum Events

The `maxEvents` script argument defaults to `100`.

When creating boundaries through script calls, provide a positive maximum if you want automatic spawning to populate the area.

RPG Maker MZ plugin commands and Quick Start settings have their own defaults.

### Automatic Boundary Update Modes

Automatic boundaries now support two update modes: **WaitTime** and **Movement**.

| Mode | Behavior |
|---|---|
| `Movement` | Processes when the player moves. |
| `WaitTime` | Processes at a configured frame interval. |

New script calls default to `"WaitTime"` when the argument is omitted.

**Movement mode is recommended for static event spawning**, where events only need to be spawned or removed as the player travels through the map.

For systems that continuously generate new events around the player, such as ongoing enemy spawning, **WaitTime** remains the appropriate choice.

Use Movement when updates depend on the player moving. Use WaitTime when updates must happen regardless of player movement.

### Quick Start

Quick Start allows automatic streaming to be configured through plugin parameters.

The shared **Map IDs** setting identifies gameplay maps, while each Spawn Boundary has its own **Spawn Map ID** for its event templates.

See [Quick Start Setup](installation.html#quick-start-setup).

### Existing Boundary Commands

Existing documented commands and script calls remain available.

For new projects, you can also take advantage of BEIDs, Self Variables, and the additional preloading functions introduced in v2.1.

See [Boundary Script Calls](Boundary/scriptcalls.html) for details.

## Third-Party Plugin Compatibility

v2.1 includes additional compatibility handling for dynamic events, event lookup, and runtime ID allocation.

If your project uses **VisuStella Events & Movement Core**, load it above Ritter's plugins.

Recommended plugin order:

```text
VisuStella Events & Movement Core
Ritter_BoundarySystem
Ritter_EventSpawner
```

If you don't use the Boundary System, only Event Spawner is required.

Ritter and VisuStella maintain their own event-spawning systems. Their saved-event records and event management functions are not interchangeable.

When using other plugins that modify event spawning, movement, or event IDs, test the specific features your game uses.

## Loading Existing Save Files

v2.1 supports recognized saves created with **Event Spawner v2.0a and Boundary System v1.5**.

For projects using the Boundary System, older saved events without BEIDs receive persistent identities during the upgrade process.

### Recommended Save Upgrade Process

1. Back up your existing save files.
2. Install the updated plugins.
3. Start the game and load a copy of an existing save.
4. Verify that saved events and gameplay state are correct.
5. Save the upgraded game to a separate slot.
6. Restart the game and load the upgraded save to confirm everything works.

Loading an existing save does not overwrite it until you save the game.

### Important Compatibility Notes

- **Missing templates:** Removing source maps or template events used by existing saves may prevent those events from being restored correctly.
- **External saves:** If you're using external boundary saves, back up the entire save folder, including additional files. See [External Save Backups](Boundary/savedboundaryevents.html#backing-up-and-transferring-external-saves).
- **Rolling back:** Saves written by v2.1 are not guaranteed to work with older plugin versions. Restore your original backups if you need to downgrade.

Save compatibility can also depend on other plugins and custom changes in your project.

Test representative existing saves before releasing an update to your players.

## Testing Your Update

After updating, give your existing spawning and saved-event features a quick test to make sure everything works as expected.

If you're upgrading a project with existing save files, try loading one to confirm your saved events restore correctly.

For projects using the Boundary System, also check that automatic spawning and event streaming work as expected.

## Related Documentation

- [Spawner Installation](installation.html)
- [Spawner Script Calls](scriptcalls.html)
- [Saved Events](savedevents.html)
- [Boundary Script Calls](Boundary/scriptcalls.html)
- [Saved Boundary Events](Boundary/savedboundaryevents.html)
- [BEID Database](Boundary/beiddatabase.html)
- [Boundary Self Variables](Boundary/selfvariables.html)
