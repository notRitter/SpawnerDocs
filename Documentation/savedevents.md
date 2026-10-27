---
title: Saved Events
parent: Spawner Documentation
nav_order: 5
---

# Saved Events

Saved Events allow dynamically spawned NPCs, objects, and other events to remain persistent when the player leaves a map and returns later.

To create a saved event, simply set the `save` argument to `true` when spawning it.

Unlike temporary spawned events, saved events retain their state between map visits and can be restored after being unspawned.

**Note:** This page covers saved events created by the base Event Spawner. The Boundary System uses a separate persistence system with BEIDs for automatic event streaming. See [Saved Boundary Events](Boundary/savedboundaryevents.html) for more information.

## Creating a Saved Event

Use `Ritter.spawnEvent()` with `save` set to `true`.

```js
Ritter.spawnEvent(mapId, eventId, x, y, save, spawnOnEvents);
```

### Example: Spawning a Saved NPC

```js
// Spawn Settings
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;
const save = true;
const spawnOnEvents = false;

// Game Variable for the Spawned Event ID
const eventIdVariable = 21;

// Spawn Event
const event = Ritter.spawnEvent(
    spawnMapId, spawnEventId, x, y,
    save, spawnOnEvents
);

// Store Runtime Event ID
if (event) {
    $gameVariables.setValue(eventIdVariable, event.eventId());
}
```

This example spawns event `1` from template map `2` at coordinates `(8, 10)` on the current gameplay map.

Because `save` is `true`, the event is retained when the player leaves the map and can return on later visits.

If spawning succeeds, the event's runtime ID is stored in game variable `21`.

You can change `eventIdVariable` to any game variable you want to use.

### Important Notes

Saved events retain their runtime event IDs so they can be restored later.

Keep the event's source template available in your project, since saved events continue using their template data.

Changes to template pages may affect the event's appearance or behavior when it is restored.

## Unspawning a Saved Event

You can temporarily remove a saved event without deleting its persistent data.

Use `Ritter.unspawnEvent()` with `removeSave` set to `false`.

```js
// Unspawn Settings
const eventIdVariable = 21;
const eventId = $gameVariables.value(eventIdVariable);
const removeSave = false;

// Unspawn Event
Ritter.unspawnEvent(eventId, removeSave);
```

This removes the event from the active map while keeping its saved information.

The event's runtime ID remains reserved, preventing other spawned events from taking its slot.

You can restore it later using the same event ID.

## Restoring an Unspawned Saved Event

Use `Ritter.restoreUnspawnedEvent()` to bring a previously unspawned saved event back onto the current gameplay map.

```js
// Restore Settings
const eventIdVariable = 21;
const eventId = $gameVariables.value(eventIdVariable);

// Restore Event
Ritter.restoreUnspawnedEvent(eventId);
```

This restores the saved event using its reserved runtime event ID.

If the event is already live, calling the function again does not create a duplicate.

**Important:** Run restoration on the gameplay map where the event was originally saved.

## Permanently Removing a Saved Event

If you no longer need a saved event, you can permanently remove it and its saved information.

Use `Ritter.unspawnEvent()` with `removeSave` set to `true`.

```js
// Unspawn Settings
const eventIdVariable = 21;
const eventId = $gameVariables.value(eventIdVariable);
const removeSave = true;

// Permanently Remove Event
Ritter.unspawnEvent(eventId, removeSave);

// Clear Stored Event ID
$gameVariables.setValue(eventIdVariable, 0);
```

This removes the event and its persistent saved data.

Its runtime event ID becomes available for recycling and may be assigned to another spawned event later.

Clearing the game variable prevents you from accidentally using the old event ID to reference a different event.

### Unspawn vs. Permanent Removal

| Action | Saved Data | Runtime Event ID |
|---|---|---|
| `Ritter.unspawnEvent(eventId, false)` | Retained | Reserved for restoration |
| `Ritter.restoreUnspawnedEvent(eventId)` | Retained | Restored using its reserved ID |
| `Ritter.unspawnEvent(eventId, true)` | Removed | Available for recycling |

## Saved Event State

Saved events retain supported information such as:

- **Position:** The event's saved map coordinates.
- **Self Switches:** Individual event self-switch states.
- **Character State:** Supported event movement, appearance, and other state.

Saved events continue to use their source event templates for event pages and commands.

This means changes to the source template can affect the event when it is restored.

Other plugins may manage additional event state independently, so test compatibility when using custom event systems.

### Runtime Event IDs and Plugin Compatibility

Base saved events normally retain their runtime event IDs, including when they are unspawned and later restored.

The Event Spawner checks for occupied event IDs when allocating new events, preventing it from overwriting existing events.

However, other event-spawning plugins may use different ID allocation methods. If another plugin assigns an ID already reserved by a saved event, an ID conflict can occur.

**For best compatibility:**

- Avoid using multiple event-spawning plugins unless you've tested them together.
- Make sure other plugins respect existing and reserved event IDs.
- Test saved-event restoration when combining the Event Spawner with other systems that create events dynamically.

If you need persistent identities for automatically streamed events, see the Boundary System's [BEID Database](Boundary/beiddatabase.html).

## Saved Events and Game Saves

Saved event data is included in the game's normal save system.

Temporary spawned events can also be included in a game save while they are live, but events created with `save = false` are not retained for future map visits.

For information about updating an existing project, see [Upgrading to v2.1](upgrading.html).

If you're also using the Boundary System with external saves, see the [External Save Backup Instructions](Boundary/savedboundaryevents.html#external-save-backups-and-transfers).

## Related Documentation

- [Spawner Script Calls](scriptcalls.html) for all spawning, unspawning, and transformation APIs.
- [Spawn Maps & Templates](spawnmapsandtemplates.html) for creating reusable source events.
- [Saved Boundary Events](Boundary/savedboundaryevents.html) for persistent streamed populations.
- [BEID Database](Boundary/beiddatabase.html) for identifying and managing individual boundary events.
