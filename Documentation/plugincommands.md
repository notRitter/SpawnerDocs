---
title: Plugin Commands
parent: Spawner Documentation
nav_order: 2
---

# Plugin Commands
MZ Only
{: .label .label-blue }

#### TABLE OF CONTENTS
 * [Spawn Event](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-event)
 * [Spawn Event Region](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-event-region)
 * [Spawn Event Terrain Tags](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-event-terrain-tag)
 * [Spawn Child](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#spawn-child)
 * [Transform Event](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#transform-event)
 * [Unspawn Event](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#unspawn-event)
 * [Unspawn Event Xy](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#unspawn-event-xy)
 * [Unspawn All](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#unspawn-all)

---

## Spawn Event

<details open markdown="block">
 
<summary>Show/Hide Section</summary>

Spawns or recycles an event on the game map on to x,y coordinates by creating a copy of a template event located on a spawn map.


![SpawnEvent_PluginCommand](../Assets/BaseSpawner/PluginCommands/spawnEvent.png)

* **mapId**: The ID or name of the map containing the event.
* **eventId**: The ID or name of the event to spawn.
* **x, y**: Coordinates to place the event on the map.
* **save**: `true` to save for restoration on map re-entry, `false` for temporary.
* **spawnOnEvents**: `true` to allow spawning on top of other events.
* **EventId -> Game Variable**: Game Variable ID to store the EventId.
* **Setter Type**: How to store EventId in the Game Variable.

  * **Set Value As Integer**: Stores EventId as an integer.
  * **Push Value As Array**: Pushes EventId into the variable as an array.

</details>

---
## Spawn Event Region

<details open markdown="block">

<summary>Show/Hide Section</summary>

Spawns or recycles an event on the game map on to a random tile marked with the provided regionId(s) by creating a copy of a template event located on a spawn map.

![SpawnEventRegion_PluginCommand](../Assets/BaseSpawner/PluginCommands/spawnEventRegion.png)

* **mapId**: The ID of the map containing the event.
* **eventId**: The ID of the event to spawn.
* **regions**: Region ID(s) to randomly place the event. Use an array for multiple IDs, e.g., `[4,8,15,16,23,42]`.
* **save**: `true` to save for restoration, `false` for temporary.
* **spawnOnEvents**: `true` to allow spawning on top of other events.
* **EventId -> Game Variable**: Game Variable ID to store the EventId.
* **Setter Type**: How to store EventId in the Game Variable.

  * **Set Value As Integer**
  * **Push Value As Array**

</details>

---

## Spawn Event Terrain Tag

<details open markdown="block">

<summary>Show/Hide Section</summary>

Spawns or recycles an event on the game map on to a random tile marked with the provided terrain tag(s) by creating a copy of a template event located on a spawn map.

![Plugin Command Image Goes Here](../Assets/BaseSpawner/PluginCommands/spawnEventTerrainTag.png)

* **mapId**: The ID of the map containing the event.
* **eventId**: The ID of the event to spawn.
* **tags**: Terrain Tag ID(s) to randomly place the event. Use an array for multiple tags, e.g., `[4,8,15,16,23,42]`.
* **save**: `true` to save for restoration, `false` for temporary.
* **spawnOnEvents**: `true` to allow spawning on top of other events.
* **EventId -> Game Variable**: Game Variable ID to store the EventId.
* **Setter Type**: How to store EventId in the Game Variable.

  * **Set Value As Integer**
  * **Push Value As Array**

</details>

---

## Spawn Child

<text color="secondary" weight="medium" size="xs">New in Event Spawner v2.1</text>

<details open markdown="block">
 
<summary>Show/Hide Section</summary>

Use **Spawn Child** to spawn an event that belongs to another spawned event.

When the parent is unspawned through the Event Spawner, its owned children are also cleaned up automatically.

![Spawn Child Plugin Command](../Assets/BaseSpawner/PluginCommands/spawn-child-plugin-command-1.png)

### Command Arguments

| Argument | Description |
|---|---|
| `ParentEventId` | Runtime event ID of the parent on the current map. Use `0` for the event executing the command. |
| `SpawnMap` | Map ID or name containing the child's template event. |
| `SpawnEventId` | ID or name of the template event to spawn. |
| `X` | Child's destination X coordinate. |
| `Y` | Child's destination Y coordinate. |
| `Save` | Whether the child should be saved. Defaults to `false`. |
| `SpawnOnEvents` | Whether the child may spawn on other events. Defaults to `false`. |

### Example

Imagine an enemy that summons minions during battle.

Inside the enemy's event, use **Spawn Child** with `ParentEventId` set to `0` to spawn a minion associated with that enemy.

The minion behaves like a normal spawned event, but it also belongs to its parent. When the enemy is unspawned, its owned minions are automatically cleaned up.

This is also useful for NPC companions, objects that generate smaller objects, and other events that manage temporary spawned events.

### Important Notes

- The parent must be an available event on the current gameplay map.
- If the parent is unavailable or the child's placement fails, no child is spawned.
- Unspawning a child individually removes its ownership link to the parent.
- Parent-child ownership uses runtime event relationships and is not automatically preserved across arbitrary boundary streaming cycles.

For script call examples, see [Spawn Child](scriptcalls.html#spawn-child).

</details>

---

## Transform Event

<details open markdown="block">

<summary>Show/Hide Section</summary>

Transforms an event on the game map into a copy of a template event located on a spawn map.

![Plugin Command Image Goes Here](../Assets/BaseSpawner/PluginCommands/transformEvent.png)

* **eventId**: ID of the event on the map to transform.
* **mapId**: Spawn map ID containing the template event.
* **spawnId**: ID of the template event on the spawn map.

</details>

---

## Unspawn Event

<details open markdown="block">

<summary>Show/Hide Section</summary>

Unspawns an event on the game map using it’s `eventId` and sets the unspawned `Game_Event` object to be **recycled** for future spawns.

![Plugin Command Image Goes Here](../Assets/BaseSpawner/PluginCommands/unspawnEvent.png)

* **eventId**: ID of the event to unspawn.
* **removeSave**: `true` to remove from saved events, `false` or empty to retain.

</details>

---

## Unspawn Event XY

<details open markdown="block">

<summary>Show/Hide Section</summary>

Unspawns an event on the game map using `x`,`y` coordinates and prepares the unspawned `Game_Event` object to be recycled for future spawns.

![Plugin Command Image Goes Here](../Assets/BaseSpawner/PluginCommands/unspawnEventXy.png)

* **x, y**: Coordinates to check for the event.
* **removeSave**: `true` to remove from saved events, `false` or empty to retain.

</details>

---

## Unspawn All

<details open markdown="block">

<summary>Show/Hide Section</summary>

Unspawns all events on the game map and prepares the unspawned `Game_Event` objects to be **recycled** for future spawns.

![Plugin Command Image Goes Here](../Assets/BaseSpawner/PluginCommands/unspawnAll.png)

* **removeSave**: `true` to delete all saved events, leave empty to retain.

</details>
