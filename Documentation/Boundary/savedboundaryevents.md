---
title: Saved Boundary Events
parent: Boundary Documentation
nav_order: 3
---

# Saved Boundary Events

Saved Boundary Events allow NPCs, resources, enemies, and other events to remember their state even when they are no longer active on the map.

For example, imagine a player meets an NPC, completes a quest, and then travels to another area. When the player returns, that NPC can reappear at its saved position with its self switches, quest progress, and other supported state intact.

The Boundary System handles this without needing to keep every saved event running at the same time.

Unlike regular saved events created by the base Event Spawner, saved boundary events use a persistent **Boundary Event ID (BEID)**. This allows the system to recognize the same event even when its runtime event ID changes.

## How Saved Boundary Events Work

A saved boundary event can exist in three states:

| State | Description |
|---|---|
| **Live** | The event is currently spawned on the map. Its event pages, movement, and graphics operate normally. |
| **Dormant** | The event has spawned before but is currently stored. It retains its saved state without running event commands or displaying a sprite. |
| **Preloaded** | The event has been prepared with a template and position but hasn't appeared on the map yet. |

### Example: An NPC Leaving and Returning

Imagine a town containing 200 persistent NPCs.

When the player enters the town, the Boundary System can spawn the NPCs needed in the surrounding area.

As the player explores:

1. Nearby NPCs become live events.
2. NPCs that leave the active area are unspawned and stored.
3. Their saved state remains available while they are dormant.
4. When the player returns, those NPCs can be restored with their previous state.

The player can explore a world containing many persistent events without requiring all of them to run simultaneously.

## Persistent Event Identity (BEIDs)

Every saved boundary event has a persistent **BEID**.

Its regular RPG Maker runtime event ID can change when the event unspawns and respawns, but its BEID remains associated with that saved event.

A complete reference consists of three values:

```js
const npcRef = {
    boundaryName: "NPCs",
    mapId: 1,
    BEID: 5
};
```

| Value | Description |
|---|---|
| `boundaryName` | Name of the spawn boundary that owns the event. |
| `mapId` | Gameplay map where the event belongs. |
| `BEID` | Persistent ID assigned to that event. |

The same BEID number can exist on different maps or under different boundaries, so keep all three values when identifying a saved event.

**Important:** Keep boundary names consistent once they own persistent events. If an event is permanently deleted, stop using its old BEID reference. That number may eventually be reused.

For more information, see the [BEID Database](beiddatabase.html).

## What Information Is Saved?

Saved boundary events retain supported event information when they are unspawned and restored.

This includes:

- **Position:** The event can return to its last saved location.
- **Self Switches:** Individual event self-switch states are retained.
- **Self Variables:** Custom values such as quest progress, dialogue choices, and resource quantities.
- **Character State:** Supported movement, appearance, and other event state.

### Using Self Variables

Self Variables are particularly useful for persistent NPCs and interactive objects.

For example, you could store:

```js
const boundaryName = "NPCs";
const mapId = 1;
const beid = 5;
const varId = "questStage";
const value = 3;

Ritter.setSelfVariable(
    boundaryName, mapId, beid, varId, value
);
```

This stores the NPC's quest stage without requiring the event to remain active.

You can update Self Variables even while a saved event is dormant or preloaded.

See [Boundary Event Self Variables](selfvariables.html) for more examples.

### Important Notes

Saved events still use their source event templates for event pages and commands.

If you change a template, those changes can affect its saved events when they are restored. Event page settings may also change an event's graphics or movement behavior.

Keep the required template maps and events in your project.

Other plugins may manage their own event data differently, so test compatibility when using custom event systems.

## Bringing Saved Events Back

The Boundary System automatically prioritizes eligible saved events when processing a spawn boundary.

Instead of always generating a new event, it first checks for saved events that can be restored.

This allows persistent NPCs and objects to return as the player moves through the map.

Restoration follows the boundary's configured area and event capacity, so only the needed population becomes live.

### Initializing a Saved Population

If you've preloaded events and want eligible ones inside an active boundary to appear immediately, use:

```js
const boundaryName = "NPCs";

Ritter.Boundary.activate(boundaryName);
Ritter.Boundary.initBoundaryEvents(boundaryName);
```

This restores eligible saved events within the boundary's interior, up to its configured event limit.

It does **not** spawn every saved event across the entire map.

For more information, see [Preloaded Boundary Events](preloadevents.html#when-it-appears).

## Saving, Loading, and Streaming

Saved boundary events retain their persistent information when they are unspawned, restored, or saved with the game.

Their runtime event IDs may change during streaming, but their BEIDs remain consistent. Use BEIDs to identify saved events rather than relying on their previous runtime IDs.

For information about updating existing projects and maintaining save compatibility, see [Upgrading to v2.1](../upgrading.html).

## Controlling Individual Saved Events

You can interact with a particular saved boundary event using its BEID.

This is useful when you want to restore a specific NPC, temporarily remove an event, or permanently delete a saved entity.

### Restore an Event by BEID

```js
const boundaryName = "NPCs";
const beid = 5;

Ritter.restoreByBEID(boundaryName, beid);
```

This attempts to restore the saved event on the current gameplay map.

Explicit restoration can bring an event into play independently of normal automatic boundary geometry and event-capacity selection.

Use it when you intentionally need a particular event, such as for a cutscene or scripted encounter.

### Unspawn an Event but Keep Its Data

```js
const boundaryName = "NPCs";
const beid = 5;
const removeSave = false;

Ritter.unspawnByBEID(
    boundaryName, beid, removeSave
);
```

If the event is live, it is unspawned while retaining its saved state.

It can be restored again later.

### Permanently Delete a Saved Event

```js
const boundaryName = "NPCs";
const beid = 5;
const removeSave = true;

Ritter.unspawnByBEID(
    boundaryName, beid, removeSave
);
```

Setting `removeSave` to `true` permanently removes the saved event's identity and associated persistent information.

This also works for dormant and preloaded events.

**Important:** Both BEID control calls operate on the current gameplay map. They do not take a gameplay map ID argument.

### Updating Dormant Events

You can update an event's Self Variables without spawning it.

However, movement and visual changes should be applied when the event is live on the current gameplay map.

A dormant event doesn't run its normal event pages or movement, and a preloaded event hasn't been given a live sprite yet.

For more information, see [BEID Database](beiddatabase.html) and [Self Variables](selfvariables.html).

## External Save Settings

By default, saved boundary event data is included in RPG Maker's regular game save.

For games with large persistent event populations, the Boundary System also supports external saving.

You can configure this under:

**Plugin Manager → Ritter_EventSpawner → Boundary Save Settings**

### Available Settings

| Setting | Description |
|---|---|
| `externalSave` | Enables or disables additional boundary save files. |
| `externalSaveCount` | Number of boundary event records stored per additional file. Normally defaults to `4000`. |

When `externalSave` is disabled, boundary data is stored in the main game save.

When enabled, dormant and preloaded boundary data can be stored in additional files.

**Note:** `externalSaveCount` controls how saved records are divided between files. It does not limit the total number of persistent events your game can contain.

## Backing Up and Transferring External Saves

If you're using external saves, backing up only the main save slot is not enough.

The additional boundary save files are part of the saved game's persistent world.

### Creating a Backup

1. Close the game before copying saved data.
2. Locate the game's save folder, normally `save/` for a local desktop deployment.
3. Copy the **entire save folder**, including the main save files, additional boundary files, and any engine-created backups.

### Restoring or Transferring a Backup

When restoring or transferring saved data, keep all files from the same backup together.

Before replacing a destination's save folder, back up its existing saved data.

**Do not mix a main save file from one backup with external boundary files from another backup.**

These files belong together and may reference the same persistent event population.

### Important Warnings

- Do not rename or manually remove individual external boundary save files.
- Older saves may still depend on external boundary data even if you later disable the external-save setting.
- Missing or damaged required boundary data can cause a save to fail loading rather than silently discarding the saved population.
- Always keep a complete backup before upgrading plugins or changing how saved data is stored.

### Browser Deployments

Browser-based RPG Maker games use browser storage rather than a normal desktop `save/` folder.

If your game uses external boundary saving, any backup or export system must preserve both the main save data and its associated external boundary data.

Clearing browser site data may remove saved games and boundary data.

Make sure your game's backup or transfer method supports both before relying on it.

## Final Notes

Saved Boundary Events are designed to let your game maintain a persistent world without keeping every event active.

The Boundary System manages when saved events appear and disappear, while their BEIDs allow you to keep track of individual NPCs, resources, and other objects over time.

For more information, see:

- [Preloaded Boundary Events](preloadevents.html) for preparing saved events before they appear.
- [BEID Database](beiddatabase.html) for identifying and accessing individual saved events.
- [Boundary Event Self Variables](selfvariables.html) for storing custom persistent data.
- [Boundary Script Calls](scriptcalls.html) for restoration, unspawning, and saved-event management.
