---
title: Spatial Lookup & Placement
parent: Spawner Documentation
nav_order: 7
---

# Spatial Lookup & Placement

The Spatial system helps the Event Spawner and Boundary System efficiently find events at specific map coordinates.

Instead of searching through every event on the map to find which events occupy a tile, the Spatial system keeps track of event positions for faster lookups.

This is especially useful when working with large numbers of events.

**Spatial works automatically when using the Boundary System.** There is no additional plugin to install or configuration required.

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

## Finding Events at a Location

RPG Maker provides a built-in way to find events at specific coordinates:

```js
const x = 8;
const y = 10;

const events = $gameMap.eventsXy(x, y);
```

When the Boundary System is installed, you can also use the Spatial lookup:

```js
const x = 8;
const y = 10;

const events = Ritter.Spatial.eventsAt(x, y);
```

Both examples return an array of events at the specified coordinates.

For example, to retrieve their event IDs:

```js
const x = 8;
const y = 10;

const events = Ritter.Spatial.eventsAt(x, y);

const eventIds = events.map(function(event) {
    return event.eventId();
});
```

Manual Spatial lookup is optional. Most developers can continue using RPG Maker's normal event commands and script calls without interacting with it directly.

## Keeping Event Positions Updated

The Spatial system automatically tracks events as they move, spawn, unspawn, or change positions through normal RPG Maker methods.

For example, moving an event with `setPosition()` or `locate()` updates its position correctly.

Avoid changing an event's `_x` or `_y` properties directly. Doing so bypasses normal movement handling and may cause the Spatial system to report an outdated location.

If you're using other plugins that modify event movement, test them together to make sure event positions remain accurate.

## Performance Benefits

Spatial lookup helps reduce unnecessary event searches, particularly on maps containing large numbers of events.

This can improve performance for systems that frequently need to find events at particular coordinates, including spawning and boundary management.

The improvement depends on the number of events, how frequently lookups occur, and the other systems running in your project.

Spatial lookup does not eliminate the processing cost of event pages, movement, graphics, or other RPG Maker systems.
