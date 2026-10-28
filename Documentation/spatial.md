---
title: Spatial Lookup & Placement
parent: Spawner Documentation
nav_order: 7
---

# Spatial Lookup & Placement

The Spatial system helps the Event Spawner and Boundary System efficiently find events at specific map coordinates.

Instead of searching through every event on the map to find which events occupy a tile, the Spatial system keeps track of event positions for faster lookups.

This is especially useful when working with large numbers of events.

Both the base Event Spawner and the optional Boundary System use Spatial to efficiently find events at specific map coordinates.

You can also access the Spatial lookup directly:

```js
const events = Ritter.Spatial.eventsAt(x, y);
```

This returns an array of events located on that tile.

## Event Placement

When spawning events, the Event Spawner checks whether the destination tile is available.

By default, spawning checks:

- Whether the coordinates are within the map.
- Whether the player is occupying the tile.
- Whether another event is occupying the tile.

You can allow spawning on tiles already occupied by events using the `spawnOnEvents` argument.

For example:

```js
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;
const save = false;
const spawnOnEvents = true;

Ritter.spawnEvent(spawnMapId, spawnEventId, x, y, save, spawnOnEvents);
```

This allows the event to spawn on a tile occupied by another event. It does not allow spawning outside the map or directly on the player's tile.

**Note:** Event placement does not automatically check every movement or terrain restriction. A valid spawn location is not necessarily a tile an event can walk onto.

### Finding Events at a Location

Spatial provides two functions for finding events at specific map coordinates.

**Get event objects:**

```js
const x = 8;
const y = 4;

const events = Ritter.Spatial.eventsAt(x, y);
```

Returns an array of `Game_Event` objects at the specified coordinates.

**Get event IDs:**

```js
const x = 8;
const y = 4;

const eventIds = Ritter.Spatial.eventIdsAt(x, y);
```

Returns an array of event IDs at the specified coordinates.

Both functions return an empty array (`[]`) when no events are found.

Use whichever function best suits your needs.

Manual Spatial lookup is optional. Most developers can continue using RPG Maker's normal event commands and script calls without interacting with it directly.

## Keeping Event Positions Updated

The Spatial system automatically tracks events as they move, spawn, unspawn, or change positions through normal RPG Maker methods.

For example, moving an event with `setPosition()` or `locate()` updates its position correctly.

Avoid changing an event's `_x` or `_y` properties directly. Doing so bypasses normal movement handling and may cause the Spatial system to report an outdated location.

If you're using other plugins that modify event movement, test them together to make sure event positions remain accurate.

## Performance Benefits

Spatial lookup provides a significant performance improvement for the Boundary System, especially on maps containing hundreds of active events or multiple large boundaries.

### Why It Matters for the Boundary System

When a boundary performs a spawning or unspawning pass, it checks tiles within its configured area to determine which locations contain events.

Without Spatial, checking a single tile may require searching through the entire map's event list. A boundary checking hundreds of tiles can repeat that search hundreds of times during a single pass.

**Spatial eliminates the need for those repeated full-map searches.**

Instead, the Boundary System can look directly at the events registered at each tile, regardless of how many other events exist elsewhere on the map.

### What This Means for Your Game

- **Faster boundary passes:** Large boundaries can check their tiles without repeatedly scanning every event on the map.
- **Better performance with hundreds of events:** Tile lookups remain efficient as the number of active events increases.
- **More room for larger boundaries:** Larger boundary areas become more practical because occupancy checks are significantly cheaper.
- **More flexibility with multiple boundaries:** Projects using several spawning and unspawning boundaries benefit from reduced lookup overhead.
- **Less repeated processing:** Frequent boundary updates spend less time searching for events at individual coordinates.

If you're upgrading an existing project that uses the Boundary System with several hundred active events, you may notice a substantial performance improvement without changing your boundary configurations.

### Beyond the Boundary System

Spatial is built into the base Event Spawner and does not require the Boundary System.

The base Event Spawner also benefits from faster occupancy checks when spawning events, particularly when placing large numbers of events.

**Note:** Spatial optimizes event location lookups. It does not eliminate the processing costs of event pages, movement logic, graphics, or other RPG Maker systems. Actual performance improvements depend on your project's event counts, boundary configurations, and other active systems.
