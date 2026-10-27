---
title: BEID Self Variables
parent: Boundary Documentation
nav_order: 6
---

# Boundary Event Self Variables

**Self Variables** allow you to store information that belongs to a specific saved boundary event.

For example, imagine your game has several NPCs, each with their own quest progress. Instead of creating separate RPG Maker game variables for every NPC, you can give each NPC its own Self Variables.

You could use Self Variables to remember:

- An NPC's current quest stage
- Whether a character has been spoken to
- How many resources remain in a gathering node
- An enemy's level or other custom information
- Whether an NPC has already given the player a reward

Each saved boundary event maintains its own values, independently of other events.

**Self Variables are persistent.** Their values are retained when an event is unspawned and restored when it spawns again. They also survive saving and loading your game.

Self Variables require the Boundary System and use [Boundary Event IDs (BEIDs)](beiddatabase.html) to identify the event they belong to.

They are separate from RPG Maker's regular game variables and self switches.

---

## Setting Self Variables

Use the following script call to assign a Self Variable to a saved boundary event:

```js
Ritter.setSelfVariable(boundaryName, mapId, BEID, varId, value);
```

### Arguments

| Argument | Description |
|---|---|
| `boundaryName` | The name of the spawn boundary that owns the saved event. |
| `mapId` | The ID of the gameplay map containing the saved event. |
| `BEID` | The event's persistent Boundary Event ID. |
| `varId` | A name or number identifying the Self Variable. |
| `value` | The information you want to store. |

### Example: Tracking an NPC's Quest Progress

Imagine you have an NPC belonging to the `"NPCs"` boundary on map 1, with a BEID of 5.

You want to remember that this NPC has reached quest stage 2.

```js
const boundaryName = "NPCs";
const mapId = 1;
const beid = 5;

Ritter.setSelfVariable(boundaryName, mapId, beid, "questStage", 2);
```

This creates or updates a Self Variable named `"questStage"` for that specific NPC.

Another NPC can have its own `"questStage"` variable with a completely different value.

**Tip:** You can use descriptive names such as `"questStage"`, `"hasMetPlayer"`, or `"resourcesRemaining"` to make your Self Variables easier to recognize.

If the specified saved event cannot be found, the setter does nothing.

---

## Getting Self Variables

Use the following script call to retrieve a saved event's Self Variable:

```js
Ritter.getSelfVariable(boundaryName, mapId, BEID, varId);
```

The arguments identify the event and the Self Variable you want to retrieve.

### Example: Reading an NPC's Quest Progress

Continuing with our previous example:

```js
const boundaryName = "NPCs";
const mapId = 1;
const beid = 5;

const questStage = Ritter.getSelfVariable(
    boundaryName,
    mapId,
    beid,
    "questStage"
);
```

The `questStage` variable now contains the value previously stored for that NPC.

You can use this information in your game's logic to determine which dialogue, quests, or interactions should be available.

### Storing the Result in a Game Variable

If you want to use the retrieved value with RPG Maker's regular event commands, you can copy it into a game variable.

For example:

```js
const boundaryName = "NPCs";
const mapId = 1;
const beid = 5;

const questStage = Ritter.getSelfVariable(
    boundaryName,
    mapId,
    beid,
    "questStage"
);

if (questStage !== undefined) {
    $gameVariables.setValue(3, questStage);
}
```

This copies the NPC's quest stage into RPG Maker game variable 3.

You can then use that game variable in conditional branches or other event commands.

### What Happens If a Variable Doesn't Exist?

If the requested Self Variable hasn't been assigned a value, or the saved event cannot be found, `getSelfVariable()` returns `undefined`.

You can check for this when necessary.

Keep in mind that values such as `0`, `false`, `""`, and `null` are valid stored values. They do not necessarily mean the variable is missing.

---

## What Can Self Variables Store?

Self Variables can store several types of information.

| Value Type | Example | Possible Use |
|---|---|---|
| Number | `5` | Quest stage, enemy level, resource quantity |
| Text | `"completed"` | Quest status, dialogue state |
| Boolean | `true` or `false` | Whether an action has occurred |
| Array | `[1, 3, 5]` | A collection of values |
| Object | `{ stage: 2, rewarded: false }` | Several related values |
| Null | `null` | An intentionally empty value |

### Example: Storing Multiple Quest Details

You can store an object containing several related values:

```js
const boundaryName = "NPCs";
const mapId = 1;
const beid = 5;

const questData = {
    stage: 2,
    rewarded: false
};

Ritter.setSelfVariable(
    boundaryName,
    mapId,
    beid,
    "quest",
    questData
);
```

You can retrieve the object later:

```js
const quest = Ritter.getSelfVariable("NPCs", 1, 5, "quest");

if (quest !== undefined) {
    const stage = quest.stage;
    const rewarded = quest.rewarded;
}
```

This can be useful when several pieces of information belong together.

### Important Notes

Self Variables should contain information that can be safely stored in an RPG Maker save file.

Use ordinary numbers, text, booleans, arrays, and simple objects.

Avoid storing functions, live event objects, sprites, or other complex JavaScript objects that aren't suitable for saving.

When changing an array or object, use `setSelfVariable()` again to store the updated value.

---

## Using Self Variables with Preloaded Events

You can assign Self Variables to a saved boundary event **before it ever appears on the map**.

This is especially useful when preloading NPCs, treasure chests, resources, or other persistent objects.

For example, imagine you're preparing an NPC that should begin with a particular dialogue state and quest progress.

### Example: Preloading an NPC with Custom Data

The following example assumes you've already created a spawn boundary named `"NPCs"`.

```js
const boundaryName = "NPCs";
const spawnMapId = 2;
const spawnEventId = 1;
const x = 8;
const y = 10;

const preload = Ritter.preloadEvent(
    boundaryName,
    spawnMapId,
    spawnEventId,
    x,
    y
);

if (preload) {
    const mapId = $gameMap.mapId();
    const beid = preload._BEID;

    Ritter.setSelfVariable(
        boundaryName,
        mapId,
        beid,
        "dialogue",
        "not-met"
    );

    Ritter.setSelfVariable(
        boundaryName,
        mapId,
        beid,
        "questStage",
        0
    );
}
```

This example:

1. Creates a saved boundary event at coordinates `(8, 10)` on the current gameplay map.
2. Retrieves the new event's BEID.
3. Assigns a dialogue state of `"not-met"`.
4. Sets its quest stage to `0`.

The event doesn't need to be spawned for these values to be assigned.

When the boundary later restores the NPC, its Self Variables are already available.

**Important:** Preloading can fail if the selected location already contains a saved event or preload belonging to that boundary. Always check that the preload was created successfully before using its BEID.

For more information, see [Preloaded Boundary Events](preloadevents.html).

---

## Updating Events While They're Unspawned

One of the most useful features of Self Variables is that **you can update a saved event's information even when that event isn't currently active on the map.**

Imagine an NPC who lives in a distant village.

The player completes a quest somewhere else in the world, and you want that NPC to recognize the completed quest the next time they meet.

You don't need to spawn the NPC just to update its quest progress.

### Example: Updating a Distant NPC

```js
const boundaryName = "NPCs";
const mapId = 1;
const beid = 5;

Ritter.setSelfVariable(
    boundaryName,
    mapId,
    beid,
    "questStage",
    3
);
```

That's all you need to update the NPC's stored quest stage.

The next time the NPC is spawned, the updated value is available.

This makes Self Variables useful for quests, world events, NPC relationships, and other systems where information may change while an event is inactive.

### What About Movement and Graphics?

Self Variables store information, but they don't automatically change an event's position, graphic, or appearance.

For example, you could store a value indicating that an NPC should use a different character graphic. Your event logic would still need to apply that change when the NPC is active.

Similarly, changing a Self Variable doesn't cause an unspawned event to execute movement routes or event pages.

**Think of Self Variables as persistent information that your event logic can read and act upon.**

---

## Saving and Removing Self Variables

Self Variables belong to their saved boundary events.

Their values are retained through:

- Event unspawning and restoration
- Boundary streaming
- Preloading and subsequent spawning
- Saving and loading the game

When an event is unspawned, its Self Variables remain with its saved data.

When the event is restored, those values are available again.

The values are written to persistent game storage when the game is successfully saved.

### What Happens When a Saved Event Is Deleted?

If you permanently delete a saved boundary event, its Self Variables are deleted along with it.

This is different from temporarily unspawning an event, which preserves its saved information for later restoration.

### Referencing Other Events

If you want one event to remember another persistent boundary event, store the information needed to identify it:

- Boundary name
- Gameplay map ID
- BEID

Avoid storing the entire event object or BEID database entry inside a Self Variable.

You can use those identifiers to find the other event again when needed.

---

## Summary

Boundary Event Self Variables give every saved boundary event its own persistent information.

Whether you're tracking NPC quests, resource quantities, dialogue choices, or other custom data, Self Variables allow those values to survive event streaming and game saves.

**The event doesn't even need to be active for you to read or update its information.**

Combined with BEIDs and the Boundary System's persistent event management, Self Variables provide a flexible way to build worlds where individual events remember what has happened to them.

For more information about identifying and managing saved boundary events, see the [BEID Database Documentation](beiddatabase.html).
